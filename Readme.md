## What is this?
I'm testing to see if the xPL home automation protocol documentation protocol could be better managed with git. Currently, the protocol is documented using [MediaWiki on the xPL site](http://xplproject.org.uk/wiki/index.php/Project_documentation)

Jump into the current results [Here](xPL-Protocol/tree/master/xPL)

# How is this supposed to work?
First off, I'd like to import the existing docs in and tag them as "release 1.0" or something. I'm working on that now, and I'm having success importing everything as MediaWiki, only updaing the links.

One issue is that viewing  a Readme.* file from it's directory causes it's relative links to point different places than viewing it directly.

Next, I'd like to demonstrate that branches could replace the current 'draft' versions of some of the schemas.

If this were to work out, someone could look after this repo, and merge branches into trunk to accept changes. They could also handle pull requests for anyone who forks the protocol and want to merge their changes back it.
