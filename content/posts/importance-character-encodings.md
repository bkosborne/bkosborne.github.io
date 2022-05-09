+++
author = "Brian Osborne"
title = "The Importance of Character Encodings"
date = 2013-12-06
tags = [
  "Drupal Planet",
]
+++

I recently started on a project that involves migrating some data from a legacy app & database into Drupal. The old application is a collection of PHP scripts that basically just generate forms, accept data, insert said data into the database, and output it on a website. Pretty simple stuff - there's not a whole lot going on. It was developed long before many of the popular CMS's and frameworks came to be, and probably before people really started paying attention to the character encoding of their data.

I initially began the data migration using Drupal's [Migrate](https://drupal.org/project/migrate) module, setting up my new content types and fields, and running through some test imports. Things seemed to go well, until I started scanning the imported data. I started to see some really strange characters like â€™ and Ã©

For the most part, it was pretty clear what these characters were supposed to represent, based on the context they were placed in. I knew right off the bat that it had _something_ to do with character encodings, something that I've never taken the time to truly understand. I made the mistake of thinking "oh this shouldn't take too long to fix", later unraveling my very own "character encoding hell". I'll talk more about that towards the bottom of the post, but let's first get an intro character encodings.

So what are character encodings exactly?
----------------------------------------

When textual data is stored and transmitted, it has to first be converted to binary just like everything else. So how is text converted? There needs to be a lookup table matching characters with binary representations. That's determined by the character encoding that is chosen - which can be one of many.

Whenever data is decoded, the character encoding must be known beforehand. Web browsers do this every time you view a website. How do they know what encoding the text is in? The web server tells it, or the browser has to make an educated guess (not what you want!).

[ASCII](http://en.wikipedia.org/wiki/ASCII) is a very basic character encoding that many are familiar with. It covers the English language along with common symbols and control characters, using only 7 bits to provide a maximum of 128 characters in the set. ASCII isn't really used anymore on the web because of the small number of characters it supports. There are many encodings that do use a full 8-bit byte for each character, re-claiming that wasted 8th bit from ASCII and bringing the total to 256 characters. In America, the most common single byte encodings are probably [Windows-1252](http://en.wikipedia.org/wiki/Windows-1252) and [ISO-8859-1](http://en.wikipedia.org/wiki/ISO/IEC_8859-1). However, no single-byte encoding can possibly hold all of the characters necessary to create one "universal" encoding that supports all known languages and symbols.

[UTF-8](http://en.wikipedia.org/wiki/UTF-8) is a [Unicode](http://en.wikipedia.org/wiki/Unicode) compliant character encoding that has become the dominant encoding on the web. One of the strongest properties of UTF-8 is that it's a _variable width byte encoding_ - meaning a single character can be represented with one or more bytes (more advanced, less used characters take up more bytes). Most importantly, UTF-8 supports just about every character in every language you can think of. This is **very important** for the web. It makes multilingual sites easier to manage since you don't have to worry about any localized character sets for each language. Everything uses the same character set.

Most developers should only be dealing with UTF-8 at this point (or another Unicode encoding) and should understand how character encodings are involved in every part of your website or application.

Where you need to worry about them
----------------------------------

Remember that any time textual data is transmitted, it needs to be encoded in a specific encoding, and decoded on the other end. The other end needs to know what encoding was used. There are at least 4 major areas where a web developer needs to be concerned with character encodings:

### Web pages

When a response w/ text in it is delivered from a server to a client, the server needs to tell the client about the encoding.

There are two opportunities to do this - one is the "Content-Type" HTTP response header which is typically set to `text/html; charset=utf-8` for standard HTML pages. Your application should set this before delivering the response to the browser. It also includes the MIME type of the response which tells the browser what type of document is being delivered (image, video, document, etc).

The other is a meta tag header `<meta charset="utf-8">` or `<meta http-equiv="Content-Type" content="text/html; charset=utf-8">`. The former is the newer HTML5 version. It's a bit confusing to indicate the character encoding _within_ the data that needs to be decoded, but this is allowed in HTML. Parsers will interpret everything as ASCII until it hits that header (which will work, since the HTML syntax is within ASCII and can be parsed that way) then re-parse the document with the new encoding. The reason it's supported in HTML is to account for any inability to set the HTTP response header which would otherwise provide the same info.

**You should be using both methods. Without setting this data, your browser will have to guess, and it may display "garbage" text.**

You can actually see what character encoding your browser chose to render the page in, and even force it to render it using a different encoding. It's a handy tool that can help diagnose if a page was meant to be rendered in some other encoding. Chrome, FireFox, and Safari all support this ability (IE probably does as well) in the "View" menu.

### Form submissions

When data in input in text boxes or text areas in an HTML form, the browser has to encode it before sending it to the server. What encoding does it use to do this? Again, this is up to you to decide. By default most browsers will just use the same encoding that the page was rendered with. However, you can specify this in the `<form>` tag: `<form action="process.php" accept-charset="UTF-8">`. So, while explicitly indicating the character set to encode the data with is not totally necessary, you should do it anyway just in case. If this is not set you risk the browser encoding the data in some random Encoding that your back-end is not anticipating.

### MySQL connections

Something that is often overlooked is that when you communicate with a database server and you send textual data, you need to indicate the character encodings when sending and receiving data between your back-end and the MySQL server. This makes sense once you understand that any time text is transferred from one place to another, you need to indicate what encoding the text is in. The text does not automatically have a way of indicating what encoding it's in (not universally anyway).

It get's pretty tricky here, at least with MySQL. There are [specific settings](http://dev.mysql.com/doc/refman/5.1/en/charset-connection.html) in MySQL that you can set after a connection has been established that dictate how characters are treated between the client and server. In particular, there are three things that are important to look at:

*   The encoding of data you send to the server from the client
*   What encoding the server should convert to after receiving the data
*   The encoding the server should return to the client when queries are run (a conversion if necessary).

Assuming the data you're sending MySQL actually IS in UTF-8 and the data in MySQL is stored as UTF-8, you'll want all three of those things to be UTF-8. No conversions will actually take place, and MySQL will just pass everything along as UTF-8. To set these values, you can simply execute the statement `SET NAMES utf8` after making a connection.

If you DON'T set these values, then MySQL will **more than likely default to latin1** (Windows-1252) which is just asking for trouble! A knowledgable developer may recognize that they need to set the character encoding for their web page and forms, and even their database fields (see below). However, if they have a backend script that accepts UTF-8 data from a form submission, but it doesn't _tell_ MySQL it's UTF-8, then MySQL will think it's latin1 when it's actually UTF-8.

### MySQL text field storage

Text fields in MySQL require you to indicate the character encoding of text fields. Defaults are set up on the server, database, and table level (each inheriting from the former). Since you're telling MySQL a field is a text field, it needs to know how to interpret the raw data it's storing as textual data. Without doing so, you wouldn't be able to query for text or have MySQL compare text fields with one another. You could alternatively just use `BLOB` fields instead, where the data is stored as binary data and not interpreted in any way. Just keep in mind you won't be able to search on these fields.

From what I can tell (as well as another blog author I reference below), MySQL doesn't change how the data is stored based on the encoding of a field. It just interprets the data differently when reading it. I could be wrong here (if I am, please let me know in the comments). However, you can [alter the encoding of an existing field](http://dev.mysql.com/doc/refman/5.0/en/charset-conversion.html) which will re-encode it (actually does change the data) as long as you recognized the limitations outlined in the that article.

For instance, if the string é was encoded as latin1 and stored in MySQL in a latin1 field, it will be stored as hex value `E9`. You can verify this yourself by running something like `SELECT hex(text_field_name) FROM table_name`. If you then run ``ALTER TABLE `table_name` CHANGE `text_field_name` `text` MEDIUMTEXT CHARACTER SET utf8 NULL;`` MySQL will convert that data from latin1 to UTF-8 for you. If you run the hex query again, you'll get back `C3 A9`.

In MySQL 5, all the text storage defaults are set to latin1 (Windows-1252) just like they are for the connection settings discussed above. While it's well known at this point that UTF-8 is preferred, I believe that the consensus was that the team behind MySQL didn't want to make the dramatic change to UTF-8 quite yet (though I think this is how it will be in MySQL 6).

Note: the collation of a field has nothing to do with how the data is stored, and instead effects how the data is compared and sorted.

What can go wrong
-----------------

One of the important things to understand is that **UTF-8 is a multibyte encoding**. This means that the majority of characters are represented with more than one byte (typically two or three), while a traditional character set just uses one byte per character. To really understand let's look at a practical example. Here's the word "Résumé" in two different common encodings:

|Encoded in|Bytes|Interpreted in Windows-1252|Interpreted in UTF-8|
|--- |--- |--- |--- |
|Windows-1252|52 E9 73 75 6D E9|Résumé|R�sum�|
|UTF-8|52 C3 A9 73 75 6D C3 A9|RÂ©sumÂ©|Résumé|
  

The normal letters in Résumé are actually the same in both Windows-1252 and UTF-8 (that's was an intentional design choice when creating UTF-8). However, once we get into the special accented e, it's actually represented with _two bytes_ in UTF-8 and just one in Windows-1252.

If your data is sent from the browser to the server as UTF-8 (this is something you as a web developer have control over), is then stored in your database as UTF-8, and finally pulled out of the database and displayed on your website as UTF-8, then will be all dandy. This is how things should be and you have little to worry about.

But let's say that you accidentally removed the charset meta tag from your page template and your web server and app don't set the "character-encoding" HTTP header. Your app will still be delivering UTF-8 encoded text to the browser, but the browser no longer _knows_ it's UTF-8. So the browser guesses. It's possible it will correctly guess that it's UTF-8, but it's also possibly it will guess Windows-1252 as the encoding. If that happens, Résumé will look like RÂ©sumÂ©. Why? Because the é was sent to the browser as `C3 A9`, which in Windows-1252 translates to the characters Â and ©. If it were correctly interpreted as UTF-8, the browser would correctly translate `C3 A9` to é.

Whoops! You catch the error a few hours later and make sure the character set is properly set to UTF-8. No harm done, right? Since the the data didn't actually change at all, there is no data corruption to worry about. The browser now interprets the text data properly as UTF-8. Well, not so fast. Let's say you have a form on your site that accepts comment submissions. Someone submitted the form when the browser rendered the page in Windows-1252. Since most browsers will encode data in form submissions using the same encoding that it used to render the page, the text may be encoded as Windows-1252 and sent along to your backend.

Now you have a problem. Your app is expecting the data to come in as UTF-8 when it's actually encoded as Windows-1252! If the comment contains with the word "Résumé", it will be encoded to `52 E9 73 75 6D E9` instead of `52 C3 A9 73 75 6D C3 A9` like it should be in UTF-8. You may have your database driver setup to indicate that the data you're sending it is UTF-8, and the database field may be set to store it as UTF-8. It's possible that your DB abstraction code throws an exception when it sees that `52 E9 73 75 6D E9` is not valid UTF-8 (which is correct, it's not valid), but it may let it slide and insert it anyway.

After you fixed the website to tell browsers you're sending it UTF-8, let's say someone goes to view the comment that was submitted earlier. Instead of displaying Résumé, the browser will actually display R�sum�. That funny question mark box is the Unicode "replacement character" - which is used when the byte sequence in the text is not supported in UTF-8. `E9`, the byte that Windows-1252 uses for é, is NOT a valid UTF-8 character. If you're curious why, [you need to read more about how UTF-8 encodes text](http://en.wikipedia.org/wiki/UTF-8).

You have data corruption now. To fix this, you'd need to find the data entries that were submitted with the wrong encoding and manually convert them to UTF-8. Not a fun task. Yo can't even assume that any text submitted after your bad commit would be encoded as Windows-1252 and is stored incorrectly. Some browsers may have properly send UTF-8 data and it was properly encoded. So if you tried to re-encode all the data after that commit to UTF-8, you may end up double-encoding the valid UTF-8 characters.

Conclusion and further reading
------------------------------

Character encodings are not trivial to deal with and it's not terribly fun trying to figure out why funny characters are being displayed on your app or website. Hopefully after reading this you'll take the subject seriously and really begin to understand what character encodings mean to your website and application. Generally **you should be using UTF-8 everywhere!**. If you're using Drupal - you don't need to worry about much, since Drupal already creates database fields with UTF-8 and sets the correct database connection settings for UTF-8. You should still take time to really understand as much as you can about encodings though.

The application I'm working on has been operating for years without a character encoding set in the HTML pages or form submissions. I've determined that form submission data has been encoded in UTF-8, Windows-1252, and ISO-8859-1, with no definitive way to figure out what data is what. The application does not set the character encoding for the MySQL connection at all, so it defaults to latin1 (Windows-1252). To top it all off, the database text fields are a mix of UTF-8 and latin1 (and the UTF-8 fields may have been "converted" from latin1). It's been frustrating trying to determine what is right and wrong in the database and how to properly fix it because there's both a mix of encodings on the field storage level as well as the input coming into the app. There's no sure fix for any of it.

If you're interested in learning more, these are some great starting points:

*   [What Every Programmer Absolutely, Positively Needs To Know About Encodings And Character Sets To Work With Text](http://kunststube.net/encoding/)
*   [Handling Unicode Front To Back In A Web App](http://kunststube.net/frontback/)
*   [UTF-8](http://en.wikipedia.org/wiki/UTF-8) and [Unicode Wikipedia](http://en.wikipedia.org/wiki/Unicode)
*   [Getting Out of MySQL Character Set Hell](https://www.bluebox.net/insight/blog-article/getting-out-of-mysql-character-set-hell)