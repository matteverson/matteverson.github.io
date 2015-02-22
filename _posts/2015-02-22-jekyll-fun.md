---
layout: post-no-feature
title: "Jekyll Fun"
description: "In which the Unicode/ASCII dichotomy becomes troublesome, again."
category: articles
tags: [jekyll, programming]
---

While playing with [Jekyll](http://jekyllrb.com/) I thought a fresh theme would be great fun. After looking around I settled on [Balzac](https://github.com/ColeTownsend/Balzac-for-Jekyll), which had room to write and a nice focus on typography. I hate looking for images when they’re not relevant. Jekyll couldn’t compile it though.

~~~ shell
jekyll build
Configuration file: /source/_config.yml
            Source: /source
       Destination: /source/_site
      Generating...
     Build Warning: Layout 'none' requested in feed.xml does not exist.
  Conversion error: Jekyll::Converters::Scss encountered an error while 
  converting 'assets/css/i.scss':
                    Invalid US-ASCII character "\xE2" on line 20
jekyll 2.5.3 | Error:  Invalid US-ASCII character "\xE2" on line 20
~~~

This is correct, a [Unicode](http://www.joelonsoftware.com/articles/Unicode.html) character was included in a Sass file. Ruby tried to interpret the file as ASCII and choked on the double
byte character.

## Not really ‘line 20’

The error message points to line 20 but that is the line within Jekyll where Scss is called, not the line in our content with the Unicode character. To find it, I opened all the Sass files in vim and searched: `/[^\x00-\x7F]`. This regex looks for any characters not in the 0-127 (ASCII) code range.

~~~ css
a:before {
	content:’“‘;
	...
	}
~~~

## Fixing the issue

The solution was to convert the unicode character to its numeric value `\201c` so all characters used would be in encoded in ASCII.


~~~ css
a:before {
	content:’\201c‘;
	...
	}
~~~

And now Jekyll has no trouble loading the theme.
