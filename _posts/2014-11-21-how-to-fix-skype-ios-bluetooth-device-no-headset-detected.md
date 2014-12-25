---
title: Pubsync Client / Server alpha released
---

Pubsync Client / Server alpha released
---
In 2012 I created [PubSync](http://pubsync.codeplex.com/), an application designed to enable simple, powerful, and efficient publishing of files from one computer to the next.
This was driven by issues with Visual Studio's publishing feature, especially the inability to ignore a single sub-directory in both the source and destination as well as performance issues.
Pubsync worked well, but had a few limitations I wanted to do away with:

1. It required SMB to copy the files
1. Because it was a client-side only tool, comparing the contents of a local source with a remote destination toook a long time when there were more than approximately 50 files
1. Because pubsync only used a file's size and modified date to compare files, if you were to check out the same files from version control in a different directory, pubsync would see them as different due to the modified date having changes. As a client-side only tool, calculating hashes was out of the question
1. Many larger files could have taken a lot less time to transfer if they were compressed first
1. Network issues could cause an application to be left in a non-working state

A few months ago, I decided to address these issues by creating pubsync client / server.
The programs are both written in Node.js, and the long-term goal is to make them compatible with Linux and OSX (currently only Windows has been tested).
The following enhancments have been implemented:

1. HTTP file transfers
1. File hash comparisons using md5 to quickly identify truly changed files
1. Speedy comparisons because the client doesn't have to read the destination files
1. GZIP encryption (currently excluding JPG and PNG files, this is customizable in the code and will eventually be configurable)
1. Files are copied to a temporary space on the destination, any files to be changed/deleted are backed up, and then the changes are applied. If an error occurs, the changes are rolled back

The new PubSync is hosted on Github at the links below and can be installed via NPM using the instructions on the repository home pages:

[pubsync-client](https://github.com/nathantreid/pubsync-client)

[pubsync-server](https://github.com/nathantreid/pubsync-server)
