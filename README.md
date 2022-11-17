# Guidance

Build *guidance prompts* to effectively control language models to accomplish specific tasks.

## Install

```python
pip install guidance
```

## Getting started

**Note the guidance package is in alpha, and so syntax may change.**

Guidance prompts allow you to specifcy the structure of your prompt through a simple templating syntax, then compile those into a form that can be executed via a large language model API. 

```python
import guidance

# define a guidance prompt
prompt = guidance.Prompt('''Use the following statements to answer the query "{{query}}".
STATEMENTS{{#each statements}}
- {{this}}{{/each}}
---
Now please answer the query "{{query}}":{{gen 'answer'}}''')

# execute the prompt
completion = prompt(
    query="What fruits does Joe like?",
    statements=["Joe likes Apples", "Joe dislikes Oranges", "Sue likes Bananas"]
)

# display the completion in a notebook environment
completion
```
<img width="567" src="https://raw.githubusercontent.com/slundberg/guidance/master/docs/artwork/demo_output.png" />

## Connecting a language model

Note that the example above uses the default language model generator as defined by the `GUIDANCE_LM_GENERATOR` envronment variable. (or will be when that is implemented) To explictly set the generator used by a prompt you can define it as

```python
generator = guidance.generators.OpenAI('text-davinci-002')
prompt = guidance.Prompt(template_string, generator)
```

## Template syntax

The guidance templating syntax should feel familar (it is an extension of Handlebars), but it is parsed differently than traditional templating libaries. Every guidance template has a linear executation order associated with it that corresponds to the order in which it is read and/or generated by the language model.

### {{variable}}
A variable name inside double curly braces is substituted directly into the prompt string.

### {{get variable}}
The get operator can read in a chunk of text generated by the language model into a variable.

### {{#if variable}} {{\if}}
Conditionals can be expressed with an opening `#if` tag and a closing `/if` tag.

### {{#unless variable}} {{\unless}}
Inverted conditionals can be expressed with an opening `#unless` tag and a closing `/unless` tag.

### {{#for item in list}} {{/for}}
Loops can be expressed with an opening `#for` tag and a closing `/for` tag. Inside a loop the special boolean variables `@first`, `@last`, and `@index` are automatically defined.