# Template for Retrieval-Submissions to Milestone 2

## Step 0: Prerequisities

We assume you have a working version of the docker image for milestone 1 available with the name `registry.webis.de/code-research/tira/tira-user-ir-lab-sose-2023-<YOUR-GROUP-NAME>/ir-datasets:0.0.1`. (Please adopt the name if you named your docker image differently.)

### Create the retrieval dataset

Please use your docker image of milestone 1 to export your retrieval dataset to your filesystem. (Alternatively, you can download the dataset from TIRA.)

```
tira-run \
    --output-directory ${PWD}/iranthology-dataset-tira \
    --image registry.webis.de/code-research/tira/tira-user-ir-lab-sose-2023-<YOUR-GROUP-NAME>/ir-datasets:0.0.1 \
    --allow-network true \
    --command '/irds_cli.sh --ir_datasets_id iranthology-<YOUR-GROUP-NAME> --output_dataset_path $outputDir'
```

In the following, we assume that you have a local version of the dataset inside `iranthology-dataset-tira`.

# Step 1: Develop retrieval approaches

We will create a docker image that cover a bunch of traditional retrieval approaches in jupyter notebooks:

- Using the BM25 retrieval model in [pyterrier-bm25.ipynb](pyterrier-lexical-retrieval-bm25.ipynb)
- Using BM25 with the query rewriting model SDM in [pyterrier-bm25-sdm.ipynb](pyterrier-bm25-sdm.ipynb)
- Using BM25 with the query expansion model RM3 in [pyterrier-bm25-rm3.ipynb](pyterrier-bm25-rm3.ipynb)
- Combining multiple retrieval scores on multiple retrieval fields in [pyterrier-multi-field.ipynb](pyterrier-multi-field.ipynb) (this might serve as starting point or inspiration for feature based learning-to-rank approaches).

Besides those traditional approaches, we have starters for other, more advanced retrieval models available:

- MonoT5 (ToDo: Add link)
- ColBERT (ToDo: Add link)
- DPR( ToDo: Add link)

Those starters might serve as inspiration for more advanced retrieval paradigms (please note: for the IR lab, it is completely sufficient if you use the traditional approaches covered in this step-by-step guide, but feel of course free to look into more advanced approaches).

To build the docker image with the traditional retrieval approaches, please execute:

```
docker build -t registry.webis.de/code-research/tira/tira-user-ir-lab-sose-2023-<YOUR-GROUP-NAME>/milestone-02:0.0.1 .
```

After building the image was succesfull, we can start a Jupyter notebook to develop our retrieval pipelines:

```
docker run --rm -ti -p 8888:8888 -v ${PWD}:/workspace registry.webis.de/code-research/tira/tira-user-ir-lab-sose-2023-<YOUR-GROUP-NAME>/milestone-02:0.0.1
```



# Step 2: Test your retrieval approach locally


# Step 3: Submit tested retrieval approaches to TIRA


