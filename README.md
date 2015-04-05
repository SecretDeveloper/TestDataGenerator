

# ![TestDataGenerator](https://raw.githubusercontent.com/SecretDeveloper/TestDataGenerator/master/icon.png) TestDataGenerator
A library and command line tool that can be used to generate data for testing, templating and other uses.  You provide it with a pattern containing symbols defining the output you want to produce and it will create random data to match that pattern.

## Features
- Easy to use syntax.
- Regex-like expressions, familiar to many.
- Support for simple patterns or advanced multi pattern templates. 
- Random generation which can be seeded.
- Support for Named Patterns making reuse easy.
- Ability to add your own named pattern collection files.
- Fully featured library.
- Fully featured commandline tool.
- 100% code coverage!

## Installation
 There are a few ways you can intall tdg.
  - You can download the latest versions from the [releases](https://github.com/SecretDeveloper/TestDataGenerator/releases) which will contain all you need to run the command line tdg tool.
  - You can install using Nuget by executing `nuget install tdg` in a terminal which will download and tdg and each of its required libraries to the current folder.
  - You can run `install-package tdg` from the Nuget Package Management Console to add TDG to your project.

## Pattern Syntax
### Pattern Composition
If you are familiar with Regular Expressions then most of the syntax used will be familiar but there are significant differences in place given that regex is used to match a string against a pattern.  The generator instead uses simple patterns of symbols to produce strings, because of the difference in usage the syntaxes cannot match up entirely.  Patterns define what the generated values will be and can be composed using text and symbols.  Sections of the pattern can be repeated a specific number of times (they can also be repeated a random number of times by providing a min and max).  Patterns can also include alternate items that will be randomly selected, helping to produce relatively complicated outputs. 

### Symbols
The following symbols are shorthand tokens which you can use in your generation patterns:

Symbol|Description|Example
------|-----------|-------
`\.`|A single random character of any type.|y
`\W`|A single random character from the following list ' .,;:\"'!&?£€$%^<>{}[]()*+-=\@#\|~/'.|:
`\w`|A single random upper-case or lower-case letter.|D
`\L`|A single random upper-case Letter.|U
`\l`|A single random lower-case letter.|r
`\V`|A single random upper-case Vowel.|I
`\v`|A single random lower-case vowel.|e
`\C`| - A single random upper-case Consonant.|Y
`\c`|A single random lower-case consonant.|d
`\D`|A single random non number character.|:
`\d`|A single random number, 1-9.|0
`\S`|A single random non-whitespace character|x
`\s`|A whitespace character (Tab, New Line, Space, Carriage Return or Form Feed)|	
`\n`|A newline character.|
`\t`|A tab character.|

### Groups
Groups can contain multiple Symbols or characters and allows us to treat them as a single unit.  Their usage becomes apparent when using them with *repetitions* or *alternations*.  Groups are surrounded in () brackets.
#### Simple Groups

|Group|Description|Example|
|----|------|-----|
|`(\d)`|A number between 0 and 9.|'9'|
|`(ABC\d)`|'ABC' followed by a number between 0 and 9.|ABC6|
|`\L(\d)\L`|A upper-case letter, three digits between 0 and 9 and another upper-case letter.|R2P|

#### Alternations
When a group contains alternations the patterns are divided by the | character, during processing one of the alternated patterns is selected at random. 
Groups can contain several individual symbols or groups of symbols and randomly alternate between them when generating the output value.

**Alternatives must be contained within a Group**

|Alternation|Description|Example|
|----|------|-----|
|`(\L\L|\d\d)`|Either two upper-case letters OR two numbers.|'79'|
|`(\L\L|\d\d|[AEIOU]|[100-120])`|Either two upper-case letters OR two digits OR an upper-case vowel OR a number between 100 and 120.|'EJ'|
|`(\C|)`|Either a upper-case Consonant or nothing.||

### Ranges
Ranges can contain multiple characters or ranges of characters but no symbols (the items defined within the range will be what is used, no special symbols are allowed).  The item to be produced is selected at random.
#### Character ranges

|Character Range|Description|Example|
|----|------|-----|
|`[abc]`|Either 'a','b' or 'c'.|'c'|
|`[a-z]`|A single lower-case letter between a and z.|'u'|
|`[A-Z]`|A single upper-case letter between A and Z.|'V'|
|`[A-D]`|A single upper-case letter between A and D.|'C'|
|`[A-Da-z]`|A single character between A and D or between a and z.|'s'|

#### Numeric ranges

|Numeric Range|Description|Example|
|----|------|-----|
|`[1-5]`|A number between 1 and 5.|'5'|
|`[100-500]`|A number between 100 and 500.|'443'|
|`[1.25-5]`|A decimal number between 1.25 and 5. The scope or number of decimal places is taken from the first number defined (1.25 in this case) and the produced value will have the same number of decimal places.|'3.18'|

### Named Parameters
A named pattern is surrounded with @ characters and links to a predefined pattern loaded from a file. The `default.tdg-patterns` file located in the same directory as the tdg executable file contains a list of named patterns which can be used in other patterns you write.  For example to generate you could write something like `([1-9]\d\d-\d\d-\d\d\d\d)` or you can use the named parameter in the file `(@misc_ssn@)` to a similar value.  You can add more patterns to the file as you wish.  Named patterns can also include other named patterns if you so wish.  

Take a look at the `@address_us_type1@` pattern in the file as an example of a compound pattern than uses other patterns to produce an output e.g. '946 Olive Terrace, Putnam County, Louisiana, 51363'

### Repetition
Repetition is a powerful feature allowing for complicated data production. A Symbol, Group or Range can be repeated a set or random number of times by using the following syntax.

#### Quantity Repetition

|Repitition syntax|Description|Example|
|----|------|-----|
|`\d{5}`|Will generate 5 number characters.|'19094'|
|`(\L\d\L){5}`|Will generate 5 upper-case letter, number, upper-case letter items.|'Q0DF0IO3ZW7FC1L'|
|`[ABC]{5}`|Will generate 5 items where each item will be either 'A','B' or 'C'.|'CBCCA'|

#### Random Quantity

|Random syntax|Description|Example|
|----|------|-----|
|`\d{5,10}`|Will generate between 5 and 10 number characters.|'3043330'|
|`(\L\d\L){5,10}`|Will generate  between 5 and 10 upper-case letter, number, upper-case letter items.|'V8UC0XF8MH1DJ4JO3BE2LT1D'|
|`[ABC]{5,10}`|Will generate  between 5 and 10 items where each item will be either 'A','B' or 'C'.|'CACBCCCBCA'|


## Template Syntax

### Templates
You can create template documents that contain multiple pattern syntax placeholders.  TDG will then replace these placeholders with their appropriate random content.  Templates can also include a *configuration* directive that can be used to control the content production.  

### Placeholders
You can place your symbols and patterns within placeholders which will be replaced with the generated values.  These placeholders contain 1 or more symbols representing the desired output characters.  
Within a template all text not within a placeholder is treated as normal text with no special handling taking place.  Patterns that need to be randomly generated should be placed inside '<< >>' tokens e.g. 'This is a template <<\L\L>>'  produces 'This is a template AA'. You can also escape placeholders by placing a '\' before them which will prevent them from being processed.  To place a '\' before a placeholder in the generated content you need to place 2 backslashes before the placeholder. 

### Configuration
You can supply configuration values to the generator either as an additional parameter within the api or you can include it within the template string itself by wrapping it within '<# #>' tokens.  Configuration directives must appear as the first item with a template or else they will be ignored and removed.

#### Configuration items
- Seed - A Seed value to be used for the random data generation. By providing the same Seed value to a template, the same output values will be produced each time the template is handled.  When a Seed is not provided a random seed is used instead producing different results each time.
- PatternFiles - Files containing Named Patterns can be included here and the patterns they contain will be available during processing. An absolute or relative path can be provided.  For relative paths the current directory and any sub directory called "tdg-patterns" will be searched when trying to locate the file.

#### Configuration directive syntax
- `<#{"seed":1}#>` - This configuration provides the Seed value for the random data generation.
- `<#{ "patternfiles":["c:\mypatterns.tdg-patterns"], "seed":300 } #>` - This configuration provides a Seed value of 300 and a Pattern file that will include Named Patterns to be used by the template.

## CommandLine tool
You can use the `tdg.exe` application to generate test data from the command line.  It can handle provided templates directly from the command line or from a file. The tool also supports exporting the generated output to either the command line or another file.

### Arguments:

|Commandline argument |Description|
|------|-----|
|`-t, --template`|The template containing 1 or more patterns to use when producing data.|
|`-p  --pattern`|The pattern to use when producing data.|
|`-i  --inputfile`|The path of the input file.|
|`-o, --output`|The path of the output file.|
|`-c, --count`|The number of items to produce. Default is 1.|
|`-s, --seed`|Seed value for Random data generator, using the same seed value and pattern will produce the same output each time.|
|`-v, --verbose`|Verbose output including debug and performance information.|
|`-n, --namedpatterns`|A list of ';' seperated file paths containing named patterns to be used in addition to default.tdg-patterns.|
|`-l, --listpatterns`|Outputs a list of the named patterns from the default.tdg-patterns file.|
|`--help`|Display the help screen.|
  
### Pattern Files
Pattern files contain Named Patterns which can be used within Templates. TDG comes with a few pattern files contained within the 'tdg-patterns' folder and you can add your own if you wish.  The files are structured as simple xml documents that should be pretty self explanatory.  To use a pattern file you have created you need to add its path to a GenerationConfig object when using the library or as part of the configuration directive at the start of a template.  You can also include them from the command line using the `-n` or `--namedpatterns` argument.

**If you are adding TDG to a project using nuget and wish to use some of the named patterns defined in default.tdg-patterns you will need to change its build settings to "Content" and "Copy Always" to ensure it gets deployed with your application.**

### Commandline examples

|Command|Description|Example|
|--------|--------|-------|
|`tdg -t 'Letters <<\L{20}>> and Numbers <<\d{12}>>'`|Single repeating symbols using the following syntax. |*'Letters DBHFCYMVZDVSXBIFTECR and Numbers 620522661563'*|
|`tdg -t '<<(\L){5}>>'`|Repeating patterns containing multiple letters or numbers of random length. </br>Will generate 5 random upper-case characters.|*'JNEZW'*|
|`tdg -t '<<(\L\L\d){4}>>'`|Will generate 4 repeating letter-letter-number values.|*'BJ8SI8WM2TU3'*|
|`tdg -t '<<(\L){10,20}>>'`|Will generate a string containing between 10 and 20 random upper-case characters.|*'UNCIKXBSYWHWQKLNIJ'*|
|`tdg -t 'Letters <<\L{2,20}>> and Numbers <<\d{2,12}>>'`|Produces items like|*'Letters IIRVGZIJECYMVFLUEG and Numbers 12'*|
|`tdg -t 'Hi there <<\L\v{0,2}\l{0,2}\v \L\v{0,2}\l{0,2}\v{0,2}\l{0,2}\l>> how are you doing?  Your SSN is <<[1-9]\d\d-\d\d-\d\d\d\d>>.' -c 100`|Input can contain several placeholders. Produces 100 items like|*'Hi there Yi Noiyeuk how are you doing?  </br>Your SSN is 683-05-8603.'*|
|`tdg -t '<<[1-9]\d\d-\d\d-\d\d\d\d>>' -c 100`|Generate 100 SSN like values and output to console window. Produces 100 items like|*'626-22-3253'*|
|`tdg -t 'Hi there <<\L\v\l\v \L\v\l\l\v\v\l\l\v>> how are you doing?' -c 100 -o C:\test1.txt`|Generate 100 strings with random name like values and output to file. Produces 100 items like|*'Eiio Doysaadxa'*|
|`tdg -t '<<Letters \w{2,20} and Numbers \d{2,12}\n>>'`|Produces the following output:|*'Letters QE and Numbers 607050
'*|

## This README was generated using the generator.  See the unit tests for other examples.
