# LLMChain

LLMChain is a simple chain that adds functionality around language models. It is used widely throughout LangChain, including in other chains and agents. An LLMChain consists of a PromptTemplate and a language model (either an LLM or chat model). It formats the prompt template using the input key values provided (and also memory key values, if available), passes the formatted string to LLM, and returns the LLM output.

## Getting Started

To use LLMChain, follow the steps below:

1. Import the necessary classes:

```python
from langchain import PromptTemplate, OpenAI, LLMChain
```

2. Create a prompt template using a string with variables:

```python
prompt_template = "What is a good name for a company that makes {product}?"
```

3. Initialize an LLM object. You can specify parameters like temperature:

```python
llm = OpenAI(temperature=0)
```

4. Create an LLMChain object by passing the LLM and the prompt template:

```python
llm_chain = LLMChain(
    llm=llm,
    prompt=PromptTemplate.from_template(prompt_template)
)
```

5. Call the LLMChain with the desired input value:

```python
output = llm_chain("colorful socks")
```

The output will be a dictionary containing the formatted input values and the LLM output:

```python
{
    'product': 'colorful socks',
    'text': '\n\nSocktastic!'
}
```

## Additional Ways of Running LLMChain

In addition to the `__call__` and `run` methods shared by all Chain objects, LLMChain offers a few more ways of calling the chain logic.

### Apply

The `apply` method allows you to run the chain against a list of inputs. It returns a list of outputs corresponding to each input:

```python
input_list = [
    {"product": "socks"},
    {"product": "computer"},
    {"product": "shoes"}
]

outputs = llm_chain.apply(input_list)
```

The `outputs` list will contain the LLM outputs for each input:

```python
[
    {'text': '\n\nSocktastic!'},
    {'text': '\n\nTechCore Solutions.'},
    {'text': '\n\nFootwear Factory.'}
]
```

### Generate

The `generate` method is similar to `apply`, but it returns an LLMResult object instead of a string. An LLMResult often contains useful generation information such as token usages and finish reasons:

```python
result = llm_chain.generate(input_list)
```

The `result` object will contain the generated text and additional information:

```python
LLMResult(
    generations=[
        [Generation(text='\n\nSocktastic!', generation_info={'finish_reason': 'stop', 'logprobs': None})],
        [Generation(text='\n\nTechCore Solutions.', generation_info={'finish_reason': 'stop', 'logprobs': None})],
        [Generation(text='\n\nFootwear Factory.', generation_info={'finish_reason': 'stop', 'logprobs': None})]
    ],
    llm_output={
        'token_usage': {'prompt_tokens': 36, 'total_tokens': 55, 'completion_tokens': 19},
        'model_name': 'text-davinci-003'
    }
)
```

### Predict

The `predict` method is similar to the `run` method, except that the input keys are specified as keyword arguments instead of a Python dictionary. It returns the generated text for a single input:

```python
output = llm_chain.predict(product="colorful socks

output will be `'\n\nSocktastic!'`

You can also use the `predict` method with multiple inputs by specifying the keyword arguments:

```python
llm_chain.predict(adjective="sad", subject="ducks")
```

The output will be the generated text for the specified inputs.

## Parsing the Outputs

By default, LLMChain does not parse the output, even if the underlying prompt object has an output parser. However, if you would like to apply an output parser to the LLM output, you can use the `predict_and_parse` method instead of `predict` and `apply_and_parse` instead of `apply`.

With `predict_and_parse`:

```python
from langchain.output_parsers import CommaSeparatedListOutputParser

output_parser = CommaSeparatedListOutputParser()
template = """List all the colors in a rainbow"""
prompt = PromptTemplate(template=template, input_variables=[], output_parser=output_parser)
llm_chain = LLMChain(prompt=prompt, llm=llm)

llm_chain.predict_and_parse()
```

The output will be a list of parsed values:

```python
['Red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet']
```

## Initialize from String

You can also construct an LLMChain directly from a string template using the `from_string` method:

```python
template = """Tell me a {adjective} joke about {subject}."""
llm_chain = LLMChain.from_string(llm=llm, template=template)
```

Then, you can use the `predict` method as usual:

```python
llm_chain.predict(adjective="sad", subject="ducks")
```

The output will be the generated text based on the provided inputs.

This readme file provides an overview of how to use LLMChain and its different methods for generating text using language models. Feel free to explore and experiment with the LLMChain to create various applications powered by natural language processing.
