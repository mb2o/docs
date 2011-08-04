## Working With Text

- [The Basics](#basics)
- [Capitalization, Etc.](#capitalization)
- [Generating Random Strings](#random)
- [Singular & Plural](#inflector)

<a name="basics"></a>
### The Basics

**Laravel** provides two great classes for working with text and strings: **Str** and **Inflector**. Let's learn what each of them will do for us.

<a name="capitalization"></a>
### Capitalization, Etc.

The **Str** class also provides three convenient methods for manipulating string capitalization: **upper**, **lower**, and **title**. These are more intelligent versions of the PHP [strtoupper](http://php.net/manual/en/function.strtoupper.php), [strtolower](http://php.net/manual/en/function.strtolower.php), and [ucwords](http://php.net/manual/en/function.ucwords.php) methods. More intelligent because they can handle UTF-8 input if the [multi-byte string](http://php.net/manual/en/book.mbstring.php) PHP extension is installed on your web server. To use them, just pass a string to the method:

	echo Str::lower('I am a string.');

	echo Str::upper('I am a string.');

	echo Str::title('I am a string.');

<a name="random"></a>
### Generating Random Strings

Need a random string? Just tell the **random** method on the **Str** class how many characters you need and the method will return a random, alpha-numeric string:

	echo Str::random(32);

<a name="inflector"></a>
### Singular & Plural

The **Inflector** class provides simple methods for retrieving the singular and plural forms of words. It's no dummy, either. For example, "child" becomes "children" and "tooth" becomes "teeth". To get the plural form of a word, just mention the word to the **plural** method:

	echo Inflector::plural('friend');

To get the singular form of a word, use the **singular** method:

	echo Inflector::singular('friends');

But, what if you are doing something like displaying the total number of comments on a blog post? You only want the plural form of "comment" if there is more than one. **Laravel** has you covered with the **plural_if** method. If the count passed to the method is greater than one, the plural form of the word will be returned. Otherwise, the word will be returned unchanged.

	echo Inflector::plural_if('comment', $count);