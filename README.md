Avaiable under Creative Commons Share Alike (CC SA) Licence 

### Usage    
    `python fontconv.py [-e encoding] -f fontname input_file output_file`
    default encoding is utf8

### INTRODUCTION

In India, a large number of data sources like vernacular newspapers, magazine,
loksabha website use proprietory fonts for displaying content in Indian
languages. The proprietory fonts overload normal unicode values with language
specific characters. 

While proprietory fonts enable content providers to display data in Indian
languages, they make document processing like extracting entities, searching
with in the document very difficult.

Unicode provides codepoints for all scripts and is accepted widely. Therefore,
documents in unicode can be easily processed, indexed and searched.


indic2unicode converts data in proprietory fonts to unicode. It currently
supports Aryan2, Divya and Surekh fonts for devanagari script that is used by
the LokSabha website for publishing debates.

###MAIN IDEA

The main idea is to tokenize the input text in a given font and then use the
unicode values of those tokens to get the unicode equivalent. 

Unicode values of tokens is specified in langs/* and the regular expression to
identify the tokens is specified in fonts/*.

For tokenization purpose, I use a pure python implementation of lex-yacc namely
[ply](http://www.dabeaz.com/ply/ply.html). I only use the lexing
facility provided by the module.

### RE-ORDERING TOKENS

Tokens obtained after tokenization may not be in the right order as there are
language specific rules. For example, in Aryan font Matra_I will appear before
KA because of visual correctness. However, unicode requires that KA should be
followed by MATRA_I rather than preceded by it.

So the tokens obtained after tokenization needs to be reordered.
fonts/basefont.py implements three ways of reordering tokens in the following
order:

1. Compose tokens: This is used for composing multiple tokens into one that
cannot be unambiguously composed during tokenization phase. For example,  In
font specific file, create a dictionary ans assign it to self.composeTokens.In
fonts/aryan2,py it is specified as  self.composeTokens = {('A', 'MATRA_AA') :
'AA'}. The key is a tuple of n-tokens and the output is one token.

2. Move a token before: A token can be moved backwards in the series of tokens.
For example, fonts/aryan2.py specifies self.jumpbefore = {'ADHA_RA': 1}
indicating the ADHA_RA should jump one place before.

3. Move a token ahead: A token can be moved ahead in the series of tokens. For
example, fonts/aryan2.py specifies self.waitdict = {'MATRA_I': 1, 'MATRAIBINDU'
: 1, 'MATRAIBINDU2': 1, 'MATRA_I2': 2} indicating tokens MATRA_I, MATRAIBINDU
and MATRAIBINDU2 should jump 1 step head and token MATRA_I2 should jump two
positions ahead.


