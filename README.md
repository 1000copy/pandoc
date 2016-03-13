# quick start 

run 

$ cd sample && pan.sh && open my-ebook.epub

# Creating an ebook 

Starting with version 1.6, pandoc can produce output in the EPUB electronic book format. EPUB books can be viewed on iPads, Nooks, and other electronic book readers, including many smart phones. (They can also be converted to Kindle books using KindleGen.)

This means that it’s now very easy to produce an electronic book! Let’s try it.

A toy example
Use your text editor to create a file mybook.txt, with the following contents:

% My Book
% Sam Smith

This is my book!

# Chapter One

Chapter one is over.

# Chapter Two

Chapter two has just begun.
To make this into an ebook takes only one command:

pandoc mybook.txt -o mybook.epub
You can upload mybook.epub to your ebook reader and try it out.

Note that if your markdown file contains links to local images, for example

![Juliet](images/sun.jpg)
pandoc will automatically include the images in the generated epub.


Changing the format
You can use the --epub-stylesheet option to specify a CSS file for the book. The default CSS is

/* This defines styles and classes used in the book */
body { margin: 5%; text-align: justify; font-size: medium; }
code { font-family: monospace; }
h1 { text-align: left; }
h2 { text-align: left; }
h3 { text-align: left; }
h4 { text-align: left; }
h5 { text-align: left; }
h6 { text-align: left; }
h1.title { }
h2.author { }
h3.date { }
ol.toc { padding: 0; margin-left: 1em; }
ol.toc li { list-style-type: none; margin: 0; padding: 0; }
You can even embed fonts in the EPUB if you want; see the User’s Guide under --epub-embed-font for instructions.



# Pandoc ： Ebook swiss tool 

## 主流电子书生成的麻烦之处

The prospect of creating MobiPocket or EPUB versions of your ebook can be overwhelming. Check out Amazon's Kindle Publishing Guidelines or read through this epic ebook creation tutorial from BB ebooks, which provides a comprehensive list of all the things you need to do.

## 电子书生成的一般步骤

The basic workflow they suggest is that you need to convert your content to HTML. Sounds simple enough, but then you have to do a bunch of additional things including:

1. Adding anchor tags to the HTML for the chapter markers and subchapter headers
2. Adding tags within your markup to indicate page breaks
3. Handcrafting an internal navigation file named toc.ncx
4. Creating a very similar HTML version of the table of contents
5. Creating a cover image & title page
6. Creating a content.opf XML file that defines all sorts of additional publishing metadata, the manifest of chapters, the spine ncx navigation, and the HTML table of contents guide file.

## 存在的问题

First off, manually adding HTML tags to content is a pain. That's why John Gruber created Markdown: so writers could use a lightweight syntax that would convey meaning without adding opening and closing HTML tags, and without needing to see the fully-rendered presentation of the formatting. Starting from Markdown was one of our requirements.

I then started crafting some HTML from Markdown that would be converted to a Kindle-ready MobiPocket file with Amazon's KindleGen program. However, the problem with starting with creating a MobiPocket file is that there's a ton of additional manual steps listed above that had no clear path toward automation.

So I wasn't able to find a satisfying tool to be able to start with creating a MobiPocket file. The consensus seems to be that it's better with an EPUB version, and then use that to generate your MobiPocket file with KindleGen.

There are a lot of automated tools out there for converting Markdown to EPUB, but the biggest issue issue I ran into is that many either completely ignore the process of generating an HTML table of contents and ncx navigation file, or the process of adding these and other metadata was too manual and unwieldy.

## pandoc 来拯救

Pandoc is emerging as the Swiss army knife of document conversions. It's really taking off in the academic and scientific community since it takes lightweight Markdown (or a dozen other) inputs and outputs to LaTeX, HTML, EPUB, PDF and about 30 other formats.

This is about the simplest pandoc command you can run.

    pandoc -o my-ebook.epub my-ebook.md

That command converts a Markdown file to an EPUB file.

You can also concatenate input files by just adding a space and the file name. This command combines three Markdown files into one EPUB file.

    pandoc -o my-ebook.epub chapter01.md chapter02.md chapter03.md

The best thing about Pandoc is that it uses H1 tags to determine the break points for chapters. This means you don't have to manually split your content into individual HTML chapter files. 

You can also designate H2 or H3 tags as the break points with the --epub-chapter-level option. 

It also automatically generates the toc.ncx navigation files and HTML table of contents and adds all of the appropriate anchor tags so you don't have to manually craft these files or add any page breaks. You get all of these navigation features for free based upon the inherent HTML structure of your document.

If you want to have second- or third-level navigation within these navigation files, you can pass in the table of contents depth, an it'll look to your H2 and H3 tags and automatically add anchor tags to your HTML and add it to the table of contents and navigation.

    pandoc  -o my-ebook.epub my-ebook.md --toc-depth=2

Pandoc also has a very lightweight way to add all of the additional metatdata, and even add a customized CSS file if you'd like. 

    pandoc -o my-ebook.epub title.txt my-ebook.md --epub-stylesheet=stylesheet.css


## Installing Pandoc 

You can download installation options here.[dmg](https://code.google.com/archive/p/pandoc/downloads)

Now let's take a look at the final commands and unpack what's happening.

## Pandoc 


Here are the commands that I ran in order to generate an EPUB and MobiPocket file from Markdown.

First get an EPUB version with Pandoc:

    pandoc -o my-ebook.epub title.txt my-ebook.md --epub-cover-image=cover.jpg --epub-metadata=metadata.xml --toc --toc-depth=2 --epub-stylesheet=stylesheet.css

Then you can feed that into KindleGen to generate a MobiPocket file:

kindlegen my-ebook.epub 

That's it! Done! You should now have both a my-ebook.epub & my-ebook.mobi file to start testing.

## Deep dig 

Let's unpack what's happening with each argument we used:

    -o my-ebook.epub
    Tells pandoc to generate an EPUB file
    title.txt
    Creates a title page and some title and author metadata. You can optionally append this information to the beginning of your first input document.
    my-ebook.md
    The actual ebook content in Markdown format. It contains multiple H1 tags, which will get split into different chapters. You can concatenate more files here if need to add chapters. You can also pass HTML as input or add URLs here.
    --epub-cover-image=cover.jpg
    Creates a cover image file and embeds the proper metadata
    --epub-metadata=metadata.xml
    You can define additional Dublin core tags about the file, including published date, author, publisher, rights, and language. Some of this is pulled from the title page, such as title, author & published date.
    --toc
    This tells Pandoc to add an HTML table of contents to the beginning of the EPUB file. The machine navigation and toc.ncx file are generated automatically, but I found that the HTML version is not always included.
    --toc-depth=2
    Tells Pandoc to look at the H2 tags and add then as secondary navigation links within both the navigation file and the HTML index. By default, it will look at a depth of 3.
    --epub-stylesheet=stylesheet.css
    You can include this option if you want to make any CSS changes or tweaks

## Metadata Files for Pandoc

Let's look at what's in a couple of these metadata files that we passed into Pandoc.

The title.txt file is just two lines containing the book title and the author. This generates the title page as well as some additional metadata:

    % My Ebook Title
    % John Doe

The metadata.xml file adds in additional metadata to the file according to the Dublin Core Metadata Element Set

    <dc:title>My Ebook Title</dc:title> 
    <dc:language>en-US</dc:language> 
    <dc:creator opf:file-as="Doe, John" opf:role="aut">John Doe</dc:creator> 
    <dc:publisher>John Doe Publishing</dc:publisher>
    <dc:date opf:event="publication">2013-11-07</dc:date>
    <dc:rights>Copyright ©2013 by John Doe</dc:rights>

Finally, you can make additional design tweaks by altering the default EPUB CSS stylesheet. What I did was create a simple prototype EPUB document with pandoc -o my-ebook.epub my-ebook.md.

## view EPub as zip 

To look at the guts(内脏) of an EPUB file, you can change the .epub extension to .zip. An EPUB file is technically just a compressed folder containing a number of HTML files, images, and a number of other XML manifest and metadata files.

You'll see a stylesheet.css in the unzipped folder that you can copy back into the base directory where you're executing the conversion. Feel free to add any CSS styling changes here and be sure to set it with the --epub-stylesheet flag.

So after you've generated some initial EPUB and MobiPocket files, you're going to want to test them and iterate on formatting and design.

## Previewing Your EPUB 

Essential tools for previewing your ebook files include:

1. Adobe's free Digital Editions EPUB reader

2. Amazon's Kindle Previewer, which allows you to preview what the MobiPocket file looks like all of the variants of their e-ink devices, Kindle Fires and Kindle for iOS readers.

3. A good tool I used to confirm that all of the metadata was properly added was Calibre. Calibre is an ebook conversion program with a GUI and some great features to confirm that the metadata you entered into metadata.xml was saved to the file properly.

## Validating & Debugging your EPUB 

It's also good to run your EPUB file through a validator to be sure you don't have any broken links or other warnings or errors.You can upload your EPUB file to the International Digital Publishing Forum website, and it'll run the EpubCheck validator on it.

If you'd prefer to run it locally, then you can download EpubCheck from their releases and run the following command after unzipping it:

    java -jar ~/Downloads/epubcheck-3.0.1/epubcheck-3.0.1.jar my-ebook.epub

If you're curious about what the source HTML looks like on an EPUB, then you can simply change the *.EPUB extension to *.zip and unarchive the folder as mentioned above.

You can also decompile your MobiPocket file, then you can use this Mobi Unpack (v047) program as described by Liz Castro here.

You can navigate to the Mobi_Unpack_v047 folder from the terminal, and then run

    python Mobi_Unpack_v047.pyw

Select your MobiPocket input file, and an appropriate output directory, and then hit "Start." It'll unpack your MobiPocket file into a mobi7 folder with the source files for e-ink readers and an mobi8 folder with the KF8 files used for the Kindle Fire.

If you're getting some validation errors, then BB Ebooks has an excellent troubleshooting section in their how-to write up.



## Images, Design & CSS Media Queries

Pandoc and KindleGen are powerful tools that get you 80-90 percent of the way there in creating professional looking ebooks. The last 10-20 percent is a lot of iteration on formatting, styling and special CSS rules, and even customized fonts with the --epub-embed-font option.

## 后记

Learning Pandoc's Markup
There are many different flavors and implementations of Markdown syntax. Pandoc uses its own special flavor of Markdown.

The Pandoc author wrote a neat online tool called Babelmark 2 that allows you to compare and contrast how different markup syntaxes will render into HTML.

For example, I wanted to find which Markdown variant provided the easiest table syntax. I ended up settling on this pipe table syntax.

For more details on the nuances of the Pandox syntax, then take a look at the "Pandoc's markdown" section on pandoc's README.

You can also test out conversion snippets on John MacFarlane's Try pandoc! page.

I enjoyed using the Mou markdown editor in order to get bootstrapped on Markdown because it has a nice live preview. However, it doesn't use Pandoc's Markdown syntax, and can get slow if the file starts to get too big.

There is a nice browser-based, markdown live editor that is compliant with Pandoc's syntax called Markx. They have a hosted version at http://markx.herokuapp.com that is helpful for getting a little bit more interactive experience for how the Markdown will render to HTML.

There was also an alpha-quality app called Kalam that uses node-webkit to create a live preview of Pandoc syntax. I found it to be a bit buggy, and limited as an interactive editor, but helpful as another Pandoc live preview option.

One issue that I ran into is making sure that images were the best size on all platforms. What may have looked okay on an e-ink reader looked way too small on the Kindle Fire. One way that Amazon suggests dealing with this is to use media queries.

Chapter 8 of the Amazon Kindle Publishing Guide talks about how to target the Kindle Fire KF8 or older e-ink MobiPocket files.

You can read Pandoc's comprehensive README file on GitHub to learn all the various options.

You can download Amazon's KindleGen tarball from here, unpack it, and place the KindleGen command somewhere where it's available in your shell's path. I placed it in /usr/local/bin.

