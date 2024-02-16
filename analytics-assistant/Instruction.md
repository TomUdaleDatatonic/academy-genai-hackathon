# Gen AI Hackathon - Analytics Assistant Challenge

Welcome to the Gen AI hackathon, Analytics Assistant Challenge. Throughout this challenge you will learn how to use Langchain, PaLM and prompt engineering to query a SQL database using natural language. We will then use a frontend from Gradio to serve our solutions to users. 

The high-level instructions for the analytics assistant are found below, and you can find a more scaffolded version in `analytics_hackathon.ipynb` as well as a final solution in `answers_analytics_hackathon.ipynb`. 

You will have approximately 3 hours to complete the hackathon, and I encourage you to start by trying to do as much as you can without referencing the solutions! 


## Getting Started

Please complete the following to setup your environment for the hackathon. 

1. Create a new jupyter notebook
2. Install the requirements.txt
3. Change the google cloud account to the authorised servie account:

    ```
    !gcloud config set account dt-gen-ai-hackathon-sa@dt-gen-ai-hackathon-dev.iam.gserviceaccount.com
    !gcloud auth activate-service-account --key-file={GOOGLE_APPLICATION_CREDENTIALS}
    !gcloud config set project {PROJECT_ID}
    ```

4. Initialise aiplatform
5. Create the Vertex LLM with Langchain ([**Documentation**](https://python.langchain.com/docs/integrations/llms/google_vertex_ai_palm)). Check that your model is working using `llm(prompt)`. 


## Setup DB connection

We need to create a connection to BigQuery so that our model can send SQL queries to the database. 

Use `langchain.SQLDatabase` ([**Documentation**](https://python.langchain.com/docs/integrations/tools/sql_database#initialize-database)) to establish a connection to BigQuery, using `f"bigquery://{PROJECT_ID}/hackathon"` as your connection uri. 

test your connection by running the following:

```
langchain_db.run("SELECT count(*) FROM hackathon.orders WHERE order_date BETWEEN '2023-01-01' AND '2023-01-31'")
```

## Create Langchain SQL Chain

Now we are ready to tie an LLM to a SQL database, and Langchain gives us the tools to do so without having to handle the interface manually. You should now use the `SQLDatabaseSequentialChain.from_llm` from `langchain.chains` ([**Documentation**](https://api.python.langchain.com/en/latest/_modules/langchain_experimental/sql/base.html#SQLDatabaseSequentialChain.from_llm)). 
Make sure to set `verbosity` and `return_intermediate_steps` to true to see the full output. Test your chain using:

```
output = db_chain("how many items did I sell in January 2023")
output["result"]
```

You can find the intermediate SQL using:

```
output["intermediate_steps"][1]
```

## Improve the chain

Our current chain has a few issues we would like to address, and is missing some features we would like to include. We can resolve many of these issues using prompt engineering! Using a prompt template from langchain ([**Documentation**](https://python.langchain.com/docs/modules/model_io/prompts/quick_start)) provide these specifications to the model:

1. limit the number of results to a given parameter of `top_n`
2. limit the tables the query uses to a given parameter of `table_names`
3. provide the model with today's date so it can answer questions like 'in the last 3 months....
4. ask the model to format answers in natural language each time.

Test your model with:

```
output = db_chain(test_prompt.format(
    question = "how many items did I sell in January 2023",
    table_info = ["customers","employees","financial_goals","inventory","orders","product_reviews","supplier_orders"],
    today_date = datetime.now().strftime("%m/%d/%Y"),
    top_k=10
))
output["result"]
```

Finally wrap your chain and prompt template into one function, called `create_sql_chain` which should return the model response and sql as a tuple.

## Chaining Chains

Now we have a basic SQL chain up and running, with a custom prompt to improve the output of our model, we can add more advanced functionality by having multiple chains together. your first task will be to create a chain which generates an email based on the user's question and the SQL chain response. You should use the `LLMChain` ([**Documentation**](https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html#langchain.chains.llm.LLMChain)) and a prompt template. be sure to provide an `output_key` parameter to the chain to ensure that only the output is returned. 

Import the following to format your responses nicely:

``` from IPython.display import display, Markdown```

Test your chain using:

```
display(Markdown(email_chain.run(
    {
        'question':"what is the meaning of life?", 
        'answer': "42"
    }
)))
```

Once you have created this chain, create a wrapper function which combines your `create_sql_chain` and your email generation chain, called `combined_chain`. Test your chain using the following:

```
display(Markdown(combined_chain("how many items did I sell in January 2023")))
```

## Grad IO

## Grad IO chat

## Pandas Agent