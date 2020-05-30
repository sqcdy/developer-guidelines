# Advanced Custom Fields

Almost all Square Candy WordPress projects rely heavily on [Advanced Custom Fields Pro](https://advancedcustomfields.com) version >= 5.

Below are some notes on standard procedures and unusual ways we leverage this powerful plugin.

## Version Control PHP code

When you are first getting started developing a new feature, you can use the ACF GUI in the dashboard to get your basic setup going. However - from that point forward, you should export the code as PHP and include it in repository code for the project. Once you commit the first version of a field group to the repo, there is no "going back" from there to the GUI. If you're used to working with ACF exclusively through the GUI, this might seem limiting and frustrating. Please give it a try anyways. This way of working opens up many doors to interesting and useful applications for ACF.

* Commiting all ACF field groups as code ensures that everyone is working with the exact same fields ecosystem while writing code even if their testing databases diverge.
* Keeps nosy Administrator level users from mucking around in ACF.
* We can take the `fields` array in a base theme or plugin and [run it through a filter](https://github.com/squarecandy/theoryone/blob/v1.1.0/inc/acf-blog-category.php#L71), allowing us to add, remove or edit particular fields in a child theme or second plugin.
* We can [add fields conditionally](https://github.com/squarecandy/squarecandy-acf-events/blob/v1.2.1/inc/acf.php#L198) based on the any php attribute we want.

Because you'll be working directly manipulating this code that generates the ACF fields, you will want to be familiar with the `acf_add_local_field_group` function, and not just rely on copy-and-pasting the export code. 
https://www.advancedcustomfields.com/resources/register-fields-via-php/  

## Boolean Results

There may be many instances where you want to make a single boolean "on/off" option that users can check. We usually prefer to use the stylized "switch" user interface (`ui = 1`), but in some cases a checkbox might be better for space or because of how the label/message displays.

The results that come back in your front end code can be challenging to deal with here are some best practices regarding them:

`$my_bool = get_field('my_bool');`

Note that if you `echo $my_bool` you will get what appears on the screen to be `1` or `0` but the variable type is actually boolean, not the intiger nor the string 1 or 0. Using strict comparison operators can therefore cause unexpected errors.

```php
// NOPE!!!
if ( $my_bool === 1 ) {
	// code here will never execute no matter if field is unset, checked or unchecked.
}
if ( $my_bool === '1' ) {
	// code here will never execute no matter if field is unset, checked or unchecked.
}

// FINE:
if ( $my_bool ) {
	// this works as expected - no execution if the field is unset or unchecked.
}
```

The other thing to watch out for is using the `get_fields()` function (note: we're talking about the plural field**s** here). It can be very handy to just load all of the fields for a particular post into one array that you can then manipulate in different ways, instead of calling each field with `get_field()` everywhere in your code. However, this can cause havoc in your boolean operations:

```php
$my_fields = get_fields();

// NOPE!!!
if ( $my_bool === 1 ) echo 'int strict comparison'; // fails on true
if ( $my_bool === '1' ) echo 'string strict comparison'; // fails on true

// ALSO NOPE!
// all of these work if the value is TRUE or FALSE, 
// but throws a Notice: Undefined index if it is unset.
if ( $my_fields['my_bool'] === (bool)1 ) echo 'BOOL 1';
if ( $my_fields['my_bool'] === TRUE ) echo 'TRUE';
if ( $my_fields['my_bool'] ) echo 'VANILLA BOOL TRUE';
if ( ! $my_fields['my_bool'] ) echo 'VANILLA BOOL FALSE<br>';

// MEH
// these work, but seem more cumbersome and confusing
if ( ! empty( $my_fields['my_bool'] ) ) echo 'using empty()';
if ( isset( $my_fields['my_bool'] ) && $my_fields['my_bool'] ) echo 'isset() and Boolean';

// BEST
// As long as we're not required to support old PHP versions,
// using the Null coalescing operator is the cleanest option
$all_day = $my_fields['my_bool'] ?? false;
if ( $all_day ) echo 'Null coalescing operator - TRUE';
if ( ! $all_day ) echo 'Null coalescing operator - FALSE';

// OR
// As long as you're writing an extra line of code, might as well do this,
// even if you already loaded `get_fields()`
$all_day = get_field('my_bool');
if ( $all_day ) echo 'Null coalescing operator - TRUE';
if ( ! $all_day ) echo 'Null coalescing operator - FALSE';
```
