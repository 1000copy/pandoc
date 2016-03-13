# quick start 

run 
    $ cd sample && ./pan.sh && open my-ebook.epub

# Creating an ebook 

pandoc can produce output in the EPUB electronic book format. 

    ## A toy example

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

Note that if your markdown file contains links to local images, for example

    ![Juliet](images/sun.jpg)

pandoc will automatically include the images in the generated epub.


## Changing the format
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

## Metadata Files  

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


