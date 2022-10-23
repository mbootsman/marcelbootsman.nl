---
title: 'Exporting / importing Gravity Forms MailChimp feeds'
date: '2017-09-16T15:21:48+02:00'
draft: false
author: 'Marcel Bootsman'
summary: 'How do you export/import MailChimp feeds from Gravity Forms? In this post I''ll explain you how to do it.'
cover: 'images/tilt-shifted-containers.jpg'
coverAlt: 'Tilt shifted containers'
useRelativeCover: true
categories:
    - 'WordPress Tips'
url: '/exporting-importing-gravity-forms-mailchimp-feeds'
---
Introduction
------------

During a project for [nostromo](https://nostromo.nl), I had to configure lots of [Gravity Forms](https://nostromo.nl/gravityforms/) which were connected to MailChimp lists. I set up all the forms and MailChimp feeds (thanks to the [MailChimp Add-on](http://www.gravityforms.com/add-ons/mailchimp/)) on the development environment, and included the migration steps in the ‘go-live-docs’. In this case the project included a WordPress multisite network with three existing sites and one new site. Challenge accepted!

So, when testing the ‘go-live-docs’, I was ready to export and import the forms. The export was done with the standard Gravity Forms export feature, found in your WordPress dashboard under `Forms -> Import/Export`. The next step was importing the forms. Again this was a flawless task. When checking the forms however, I saw the MailChimp feeds were not imported. Hmm, that’s odd, or is it just how the import/export of Gravity Forms is supposed to work?

I contacted Gravity Forms support and got a fast, clear, disappointing answer:

> I’m afraid that exporting feeds is not currently possible. We do have this in our feature request list so I have added your vote. I’m sorry for any inconvenience that this may cause you.

This did not bring hope, and I was preparing to manually copy the feeds. Until I realized I was part of a great community, the WordPress community. A community in which you can ask questions, no matter what the level is, and where someone is always available to answer your question. I decided to go to the [Post Status Slack](https://poststatus.com/club/) and post my question in the `#heavydev` channel.

After about 40 minutes I got a reply from [Naomi C. Bush](https://twitter.com/NaomiCBush) from [gravityplus.pro](https://gravityplus.pro). They also have a [Slack](https://gravityplus.pro/community/) team, which I have joined. Naomi directed me to a plugin, [Import/Export Add-On Feeds for Gravity Forms](https://wordpress.org/plugins/importexport-add-on-feeds-for-gravity-forms/). She had no experience with it, and I decided to give it a try. Since I have written this blog, you can conclude it works. Thanks Naomi, and thanks[ Anthony Montalbano](https://twitter.com/italianst4) for developing the plugin and sharing it with the WordPress community.

Let’s go to the details, how to use this plugin.

Installing the plugin
---------------------

Install the plugin from your WordPress dashboard, by going to `Plugins -> Add New`. Search for the plugin by entering `import export feeds gravity forms` in the search box in the top right corner. Install and activate (or network activate if you need it in all sites in a network). The plugin is now active and we can use it.

How to export MailChimp feeds
-----------------------------

To export feeds, we need to navigate to `Forms -> Import/Export`. Besides the default export options, we see two new options: `Export Feeds`, and `Import Feeds`.

{{< figure src="images/export-entries.png" caption="Export options in Gravity Forms" title="Export options in Gravity Forms" alt="Export options in Gravity Forms">}}

Click on `Export Feeds`. In most cases you can select all feeds, or just select the feeds you want to export and click `Download Export File`. If you have chosen smart names for your feeds, selecting will be easy. If not, well, lesson learned right?

How to import MailChimp feeds
-----------------------------

Go to `Forms -> Import/Export` and click on the Import Feeds link on the site where you want your feeds to be imported. Select the `.json`file that you have exported and click on import.
{{< figure src="images/import-feeds.png" caption="Import feeds from Gravity Forms" title="Import feeds from Gravity Forms" alt="Import feeds from Gravity Forms">}}

After the import is done, you will see a comforting notification on your screen:
{{< figure src="images/import-succeeded.png" caption="Import of Gravity Forms feeds succeeeded" title="Import of Gravity Forms feeds succeeeded" alt="Import of Gravity Forms feeds succeeeded">}}

See the result
--------------

To see the result, go to your form. In the Settings tab go to Mailchimp. You will see your imported feed(s) in the MailChimp feeds list.
{{< figure src="images/import-result.png" caption="Result of importing Gravity Forms feeds" title="Result of importing Gravity Forms feeds" alt="Import of Gravity Forms feeds succeeeded">}}

If you have any questions about this, please let me know, I’m happy to help.