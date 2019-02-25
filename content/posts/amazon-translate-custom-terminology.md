+++
Description = "A look at how you can use custom terminologies with Amazon Translate."
Tags = [
  "Boto3",
  "Demos",
  "Python",
  "AWS",
  "Amazon Translate",
  "Translation",
  "Serverless"
]
Categories = [
  "AWS",
  "Tutorials"
]
title = "Amazon Translate Custom Terminologies"
publishdate = "2019-03-10T17:36:44-08:00"
date = "2019-03-10T17:36:44-08:00"
[image]
    feature = "/images/amazon-translate/custom-terminology-example.png"
+++

Let's take a look at how we can use Amazon Translate, including using custom terminologies to customize our translations with specific vocabulary between languages.

In this case, let's imagine I'm translating marketing speak between languages for my cloud consulting company Stormlight Consulting. I'd like to translate the text I've already written in English for a Spanish speaking audience. 

<!--more-->

Let's start with a single sentence:

"Stormlight Consulting offers worry free cloud consulting services in our CloudFree plan."

In order to translate this with Amazon Translate it is actually incredibly simple. In the Python example below I use the AWS SDK for Python, boto3, to translate this phrase from English to Spanish:

```python
import boto3

translate = boto3.client('translate')

EN_TEXT = ("Stormlight Consulting offers worry free cloud"
           " consulting services in our CloudFree plan.")

# The Spanish language code:
OUTPUT_LANG_CODE = 'es'

result = translate.translate_text(
    Text=EN_TEXT,
    SourceLanguageCode='auto',
    TargetLanguageCode=OUTPUT_LANG_CODE
)

print(result['TranslatedText'])
# Outputs --> "Stormlight Consulting ofrece servicios de consultoría en la nube sin preocupaciones en nuestro plan CloudFree."
```

We can then use the result as part of a website build process to create multilingual pages or maybe inside a live translation application. Or basically anywhere else we'd like.

## Potential Issues with Direct Translations

But what if Stormlight Consulting wanted the translation for the word "CloudFree" to be different based on the language of the customer? We could try to literally translate cloud and free and make a compound. But the specific context of what kind of free (the worry free sense of freedom) might get lost.

For example, we might risk convincing our customers we offer "nube gratis". In Spanish, this might sound like we're giving away cloud services at no charge - not at all what we want to imply.

Also, maybe in Germany the word "Cloud" is already spoken enough in technical communities to be kept in English and we can bring it in directly without translation.

Each of these lingual quirks might make us want to translate our plan's title in specific ways that we can't expect Amazon Translate to do for us. Because of this, we need Amazon Translate custom terminologies.

## Custom Terminologies

[Custom terminologies](https://docs.aws.amazon.com/translate/latest/dg/creating-custom-terminology.html) will allow us to do the custom modification we need to translations.

For example, we might want these translations for "CloudFree":
- "NubeLibre" (Spanish)
- "NuvemLivre" (Portuguese)
- "CloudFreilassen" (German)

In order to make sure that each time we translate the word CloudFree we get these customized results we have to provide a custom terminology file. This file can be either a CSV or TMX format and must include the source term (the word `CloudFree` in our case) and the translations. It also requires the language codes for each translation to differentiate them. Let's look at a CSV example of what this files looks like:

```csv
en,es,pt,de
CloudFree,NubeLibre,NuvemLivre,CloudFreilassen
```

Now we could either upload this custom terminology CSV inside of the AWS console or run some Python code like this to upload it for us.

```python
import boto3

translate = boto3.client('translate')

with open('./customterminology.csv', 'rb') as ct_file:
    translate.import_terminology(
        Name='CloudFreeTerms',
        MergeStrategy='OVERWRITE',
        Description='Terminology for CloudFree custom plans',
        TerminologyData={
            'File': ct_file.read(),
            'Format': 'CSV'
        }
    )

```

After this, we run effectively the same initial code snippet with one additional big of configuration for the new custom terminology we want to use.

```python
import boto3

translate = boto3.client('translate')

EN_TEXT = ("Stormlight Consulting offers worry free cloud"
           " consulting services in our CloudFree plan.")

# The Spanish language code:
OUTPUT_LANG_CODE = 'es'

result = translate.translate_text(
    Text=EN_TEXT,
    TerminologyNames=['CloudFreeTerms'], # The only thing we added is this line
    SourceLanguageCode='auto',
    TargetLanguageCode=OUTPUT_LANG_CODE
)

print(result['TranslatedText'])
# Outputs --> "Stormlight Consulting ofrece servicios de consultoría en la nube sin preocupaciones en nuestro plan NubeLibre."
```

That's it! Now we get the updated translation which is identical with the exception of the NubeLibre replacing the CloudFree. If we decided to do other language translation we would also see the other custom terminologies being brought into the translated sentences instead of the word "CloudFree".

Here's the highlights on what happens here:

First, the words are reviewed by Amazon Translate to see if they contain custom terms. After that, the sentence is translated using some neural networks and encoder-decoder architectures in the background to give us the English --> Spanish output. Then the words that were marked as custom terms are evaluated and looked up in the custom terminology files.

<center>![Example of Amazon Translate translating a sentence referencing custom terminologies](/images/amazon-translate/pre-lookup.png)</center>

After that, these terms are then replaces with their output-language counterparts:

<center>![Example of Amazon Translate translating a sentence referencing custom terminologies](/images/amazon-translate/post-lookup.png)</center>

## Caveats

Now, using custom terminologies is not always recommended. In some languages the grammatical implications of the language make the way custom terminologies work irrelevant or even harmful to the translation process.

So keep that in mind in case you're translating to Russian, Arabic, or any of the other [non-recommended languages](https://docs.aws.amazon.com/translate/latest/dg/permissible-language-pairs.html) any time soon!

Have any questions about Amazon Translate? Feel free to leave comments below or check out my [Pluralsight course](https://app.pluralsight.com/library/courses/aws-translate-text) to learn how to use it effectively! (If you don't have Pluralsight just [give me a shout]({{% my_twitter %}}) and I'll get you a free trial.)