# Step-by-Step guide for Retrieval-Submissions to Milestone 2

Before you start this step-by-step guide for milestone 2, please do the dedicated [PyTerrier tutorial](../milestone-02-pyterrier-tutorial/notebook1.ipynb).

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

# Step 1: Implement baseline retrieval approaches

We will create a docker image that covers a bunch of simple baseline retrieval approaches in jupyter notebooks:

- Using the BM25 retrieval model in [pyterrier-bm25.ipynb](pyterrier-bm25.ipynb)
- Using BM25 with the query rewriting model SDM in [pyterrier-bm25-sdm.ipynb](pyterrier-bm25-sdm.ipynb)
- Using BM25 with the query expansion model RM3 in [pyterrier-bm25-rm3.ipynb](pyterrier-bm25-rm3.ipynb)
- Combining multiple retrieval scores on multiple retrieval fields in [pyterrier-multi-field.ipynb](pyterrier-multi-field.ipynb) (this might serve as starting point or inspiration for feature based learning-to-rank approaches).

Besides those traditional approaches, we have starters for other, more advanced baseline retrieval models available:

- [MonoT5](todo-add-link)
- [ColBERT](todo-add-link)
- [DPR](todo-add-link)

Those starters might serve as inspiration for your individual retrieval paradigms that you will implement later in milestone 3 (please note: for milestone 2 of the IR lab, it is completely sufficient if you use simple approaches covered in this step-by-step guide, but feel of course free to look into more advanced approaches).

To build the docker image with the traditional retrieval approaches, please execute:

```
docker build -t ir-lab-milestone-02 .
```

After building the image was succesfull, we can start a Jupyter notebook to develop our retrieval pipelines:

```
docker run --rm -ti -p 8888:8888 -v ${PWD}:/workspace ir-lab-milestone-02
```

If we build the image, it already comes with the four jupyter notebooks that we linked above, so lets run them locally.


# Step 2: Test your retrieval approach locally

The image that we have created already has a script `/workspace/run-pyterrier-notebook.py` embedded (see the [source if you want more information](https://github.com/tira-io/ir-experiment-platform/blob/main/tira-ir-starters/pyterrier/run-pyterrier-notebook.py)).
This script takes a jupyter notebook and the input and output directory as arguments and executes the notebook by injecting the input and output.

To execute the [pyterrier-bm25.ipynb](pyterrier-bm25.ipynb) notebook that we included in our docker image on your system as TIRA would execute it, the command is:

```
tira-run \
    --input-directory ${PWD}/iranthology-dataset-tira \
    --output-directory ${PWD}/bm25-output \
    --image ir-lab-milestone-02 \
    --command '/workspace/run-pyterrier-notebook.py --input $inputDataset --output $outputDir --notebook /workspace/pyterrier-bm25.ipynb'
```

In this `tira-run` example, we set the arguments as follows:

- `--command` specified the command that is to be executed in the container. Our command here is `/workspace/run-pyterrier-notebook.py --input $inputDataset --output $outputDir --notebook /workspace/full-rank-pipeline.ipynb` which specifies that we execute the mentioned `run-pyterrier-notebook.py` script that itself gets an input and output and the to-be-executed notebook as arguments. In this case, we specify to execute the notebook `/workspace/pyterrier-bm25.ipynb`, i.e., to execute a different notebook, please change this `--notebook` argument. (the other passed arguments `$outputDir` and `$inputDataset` are the input respectively output directories that are injected by TIRA.
- `--image` specifies the docker image in which `--command` is executed
- `--input-directory` specifies the directory with the input data.
- `--output-directory` specifies the directory to which the output data is written.

This should yield a run like (top 3 lines with `head -3 bm25-output/run.txt`):

```
1 0 2021.ipm_journal-ir0anthology0volumeA58A1.6 1 16.708092762527492 BM25
1 0 2011.spire_conference-2011.10 2 15.699445240396184 BM25
1 0 2019.cikm_conference-2019.346 3 15.507585799713157 BM25
```

After we have executed the command above, we can again render the results:

```
tira-run \
    --output-directory ${PWD}/bm25-output \
    --image registry.webis.de/code-research/tira/tira-user-ir-lab-sose-2023-<YOUR-GROUP-NAME>/ir-datasets:0.0.1 \
    --allow-network true \
    --command 'diffir --dataset iranthology-<YOUR-GROUP-NAME> --web $outputDir/run.txt > $outputDir/run.html'
```



This yields a file `bm25-output/run.html` that should look like this:


![Screenshot_20230502_195330](https://user-images.githubusercontent.com/10050886/235745769-48c5dfa4-0986-4ad5-93b4-1077b24839cd.png)



Similarly, we can execute other notebooks. For instance, we can execute the notebook `pyterrier-multi-field.ipynb` via:

```
tira-run \
    --input-directory ${PWD}/iranthology-dataset-tira \
    --output-directory ${PWD}/multi-field \
    --image ir-lab-milestone-02 \
    --command '/workspace/run-pyterrier-notebook.py --input $inputDataset --output $outputDir --notebook /workspace/pyterrier-multi-field.ipynb'
```

This should yield a run like (top 3 lines with `head -3 multi-field/run.txt`):

```
1 0 2010.cikm_conference-2010.284 1 41.0567764391176 BM25
1 0 2018.wwwconf_conference-2018.13 2 39.37674403947608 BM25
1 0 2015.tist_journal-ir0anthology0volumeA6A4.12 3 37.81074710961472 BM25
```

To render the results, we can again use:

```
tira-run \
    --output-directory ${PWD}/multi-field \
    --image registry.webis.de/code-research/tira/tira-user-ir-lab-sose-2023-<YOUR-GROUP-NAME>/ir-datasets:0.0.1 \
    --allow-network true \
    --command 'diffir --dataset iranthology-<YOUR-GROUP-NAME> --web $outputDir/run.txt > $outputDir/run.html'
```

This yields a file `multi-field/run.html` that should look like this:

![Screenshot_20230502_195048](https://user-images.githubusercontent.com/10050886/235745270-de591307-3ab5-40a5-8902-cf2442f10f06.png)


# Step 3: Submit tested retrieval approaches to TIRA

First, ensure that you are authenticated to your dedicated registry (via `docker login`, detailed instructions on how to access your credentials can be found [here](https://www.tira.io/t/how-to-make-a-software-submission-with-docker/1437)).

Second, please upload the docker image to your dedicated registry:

```
docker tag ir-lab-milestone-02 registry.webis.de/code-research/tira/tira-user-ir-lab-sose-2023-<YOUR-GROUP-NANME>/milestone-02:0.0.1
docker push registry.webis.de/code-research/tira/tira-user-ir-lab-sose-2023-<YOUR-GROUP-NANME>/milestone-02:0.0.1
```

Click on "Docker Submission" -> "Add Container" and specify the docker image and the to-be-executed command.
E.g., for the `pyterrier-bm25.ipynb` notebook, the output should form should look like this:

![Screenshot_20230502_200700](https://user-images.githubusercontent.com/10050886/235749533-d710cf36-c097-4c23-96de-56d746073ca8.png)

After you have added the software, you can run it by specifying the dataset on which it should run and the resources that it gets for its execution:

![Screenshot_20230502_201037](https://user-images.githubusercontent.com/10050886/235749854-262de14a-16ee-4d1e-9fb4-61fd90a943dd.png)

Thats it, congrats for finishin milestone 2 :)

