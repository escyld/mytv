myTV RSS Scraper

myTV is a script that will scrape your favorites rss feed from supported
TV and Movie Listing websites.  Currently supported are tvtorrents.com
(http://www.tvtorrents.com) and showRSS(http://showrss.karmorra.info/).
The script will submit new links to transmission-daemon.  It includes regex
filters, basic torrent management, twitter notification of completed torrents
and rss history.  The script will automatically move completed downloads into
proper directories:  TV episodes will be moved into a directory for each show. 
Movies will be moved into their own directory.



PREAMBLE
===============

The author of this script has NO AFFILIATION WHATSOEVER with the administrators
of tvtorrents.com or showRSS.  The administrators of the afore-mentioned
sites are not responsible for for this script, or any damages done to systems
due to the performance of this script.

This software is released under the GNU Public License V2, and comes with
NO WARRANTIES, WRITTEN OR IMPLIED.  Use at your own risk.  A copy of the GPLv2
is supplied with this software.  If it is not present, it can be obtained by
downloading it from http://www.gnu.org/licenses/old-licenses/gpl-2.0.txt

COPYRIGHT
===============

Original source was Copyright 2009 Terry Soucy <tsoucy@nb.sympatico.ca>

INSTALL
===============

This software works with the current version of transmission-cli.  As of this
writing, the current version is 2.92.  Install transmission-cli and edit the
rpc user and password.  Also make sure to whitelist the system from which
myTV will be connecting by adding the appropriate IP address to the
rpc-whitelist line.

FYI, transmission-daemon will overwrite the settings.json file on exit, so you
will need to stop transmission-daemn before modifying this file.

This software also relies on the following perl modules which are typically not
included in a default perl environment:
 XML::RSSLite
 Net::Twitter
 
They can be downloaded from cpan.org.  If you are using Ubuntu (9.10),
you can get them by installing the following packages via apt-get:
 apt-get install libxml-rsslite-perl
 apt-get install libnet-twitter-perl

For fedora/redhat users, grab the perl-XML-RSSLite package from the DAG repo.
If you have no idea what I'm talking about, then Google is your friend.
The Net::Twitter perl package is not available at the Dag repository.  Download
and install from CPAN.

First determine the user who will be running the script.  I'm assuming here
that it will be the root user.  If it is another user, you will need to chown
the /var/cache/mytv directory to the appropriate uid, and edit the cron entry
so that the cron job runs as the appropriate user.

Installation is simple.  Perform the following actions as root ...
1. cp mytv.conf /etc/mytv.conf
2. mkdir -p /var/cache/mytv
3. chown user.group -R /var/cache/mytv (if required)
4. cp mytv /usr/sbin
5. cp mytv.cron /etc/cron.d/mytv
6. /etc/init.d/cron restart
7. Go through the mytv.conf file and add your favorites RSS feeds, set the download 
   directories, etc. Make sure your transmission-daemon credentials are correct.


CONFIGURATION
===============

The config file, by default, must be located at /etc/mytv.conf.  The comments
in the config file are pretty self-explanatory.  The filters are perl regular
expressions.  The sample filters supplied should give you enough of a start to
get what you are looking for.  Filters are NOT case sensative.  If you wish to
load an alternative configuratiuon file at runtime, use the -c option ...

  mytv -c /path/to/config

This will load an alternative configuration file.  This can be useful for
debugging with the -d option.  Using the debug option will allow you to test
the filters and other configuration functionality, but no files will be
altered, and no torrents will be submitted.

FILTERS
===============

The filtering system is a First Match basis.  This is the best method for
filtering shows where you wish to retrieve a Hi-Def version of only certain
shows.

The order of the filters in the configuration file is the order in which the
filter processor will process the show titles.  Filters prefixed with a + are
"Match and Accept", while filters prefixed with - are "Match and Reject".
The configuration file contains some starter filters.

Attached to the filter is the destination for the completed torrent ...
+Californication::/path/to/destination

If no destination exists for a filter, then the default destination will be
used, which is one defined by the defaultDestination configuration option.
Lastly, be sure that the user which transmission-daemon is running as has
the permission to write to the destination directories.  Transmission-daemon
will pause your torrent in the event that it can not be moved.

