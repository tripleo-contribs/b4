ty: sending automated contributor feedback
==========================================
B4 makes it easier to send automated developer feedback when you apply
patches or pull requests to your git tree.

Tracking retrieved patches and PRs
----------------------------------
Any patches or pull requests you retrieve with ``b4 am,shazam,pr`` will
be automatically tracked by b4 in your homedir (usually, in
``$HOME/.local/share/b4``, but may vary if your ``$XDG_DATA_HOME`` is
set to a different value). There are four kinds of files in that
directory:

* .am: contain information about patches retrieved with ``b4 am`` or
  ``b4 shazam``
* .pr: contain information about pull requests retrieved with ``b4 pr``
* .sent: either .am or .pr tracked files that were successfully sent
  using ``b4 ty``
* .discarded: either .am or .pr tracked files that were deleted using
  ``b4 ty``

All of these files contain JSON data about the series or pull requests
being tracked.

Using the Auto-Thankanator
--------------------------
If you've retrieved and applied some patches to your tree, you should be
able to fire up the "auto-thankanator", which uses patch-id and commit
subject tracking to figure out which series from those you have
retrieved were applied to your tree. The process is usually pretty
fast and fairly accurate.

Manually listing and thanking
-----------------------------
If you don't want to use the auto-thankanator, or if it's not finding a
patch series (e.g. because you've made changes to a commit before
applying it to your tree), you can use a more manual process. First,
list all tracked series::

    $ b4 ty -l

Identify the series that you're sure got applied, then generate the
thank-you message::

    $ b4 ty -t 1

This will write out a .thanks file in the current directory, which you
can then modify and send out.

Sending out mail vs. writing .thanks files
------------------------------------------
By default, ``b4 ty`` will write out .thanks files in the current
directory, which allows you to edit the body of the message before
sending it out, e.g. using ``mutt``::

    $ mutt -f foo.thanks

However, if you have a configured ``sendemail`` section, you can also
tell b4 to send out the thanks message directly::

    $ b4 ty -aS --dry-run

The above command will locate all tracked series that got applied to
your tree and show the messages that are going to be sent out. If you're
happy with the results, you can omit the ``--dry-run`` switch to
actually send the mail.

If you want ``b4 ty`` to always send mail, you can make the ``-S``
switch permanent by setting the ``b4.ty-send-email`` config variable to
``yes``.

Editing the templates
---------------------
You can edit the default templates that are provided with b4 to
customize the thank-you message. Once you have your own versions, you
can specify the path to the template to use via the
``b4.thanks_am_template`` and ``b4.thanks_pr_template`` configuration
parameters. See :ref:`ty_settings` for details.

Optional flags
--------------
``-g GITDIR, --gitdir GITDIR``
  The git tree to use instead of the current working directory.

``-o OUTDIR, --outdir OUTDIR``
  Where to write the .thanks files if not into the current directory.
  Has no effect when ``-S`` is used.

``-l, --list``
  Lists all tracked patch series and pull requests.

``-t THANKFOR, --thank-for THANKFOR``
  From the listing generated by ``--list``, specify which thank-you
  notes should be sent. This command accepts comma-separated values and
  ranges, including open-ended ranges, e.g.: ``-t 1,3,5-7,9-``. When using
  a single number, it's possible to use negative values to refer to the
  latest patch series, e.g.: ``-t -1`` refers to the last patch series.
  It also accepts ``all``.

``-d DISCARD, --discard DISCARD``
  From the listing generated by ``--list``, specify which thank-you
  notes should be discarded. This command accepts comma-separated values
  and ranges, including open-ended ranges, e.g.: ``-t 1,3,5-7,9-``. It
  also accepts ``all``.

``-a, --auto``
  The auto-thankanator: uses patch-id and commit subject matching to
  figure out which tracked series or pull request have been applied to
  your tree.

``-b BRANCH, --branch BRANCH``
  When using ``--auto``, specify which git branch should be used if not
  the currently active branch.

``--since SINCE``
  When using ``--auto``, this lets you adjust how far back b4 will look
  to find your own commits. Takes the same format as ``--since`` flags
  passed to git, with the default of ``1.week``.

``-S, --send-email``
  Instead of writing .thanks files, send the email directly. Requires
  that the ``sendemail`` section is present in your git configuration.

``--dry-run``
  When used with ``-S``, will not actually send email, just print them
  out to stdout.

``--pw-set-state PW_SET_STATE``
  When patchwork integration is configured, sets the specified patchwork
  state instead of the default specified in config settings (use with
  -a, -t or -d). See :ref:`patchwork_settings` for more details.

``--me-too`` **(v0.15+)**
  Include yourself in the thank-you message cc's. This is useful if you
  want to keep track of which patches you applied to your tree.
