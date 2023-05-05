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

We will create a docker image that covers a simple baseline retrieval approach with BM25 in a jupyter notebook. The file [pyterrier-bm25.ipynb](pyterrier-bm25.ipynb) contains the retrieval approach with BM25.

To build the docker image with the [pyterrier-bm25.ipynb](pyterrier-bm25.ipynb) notebook, please execute:

```
docker build -t ir-lab-milestone-02 .
```

After building the image was succesful, we can start a Jupyter notebook to develop our retrieval pipelines:

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

- `--command` specified the command that is to be executed in the container. Our command here is `/workspace/run-pyterrier-notebook.py --input $inputDataset --output $outputDir --notebook /workspace/pyterrier-bm25.ipynb` which specifies that we execute the mentioned `run-pyterrier-notebook.py` script that itself gets an input and output and the to-be-executed notebook as arguments. In this case, we specify to execute the notebook `/workspace/pyterrier-bm25.ipynb`, i.e., to execute a different notebook, please change this `--notebook` argument. (the other passed arguments `$outputDir` and `$inputDataset` are the input respectively output directories that are injected by TIRA.
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

Similarly, you can execute other notebooks (change the argument for `--notebook`).


# Step 3: Submit tested retrieval approaches to TIRA

First, ensure that you are authenticated to your dedicated registry (via `docker login`, detailed instructions on how to access your credentials can be found [here](https://www.tira.io/t/how-to-make-a-software-submission-with-docker/1437)).

Second, please upload the docker image to your dedicated registry:

```
docker tag ir-lab-milestone-02 registry.webis.de/code-research/tira/tira-user-ir-lab-sose-2023-<YOUR-GROUP-NANME>/milestone-02:0.0.1
docker push registry.webis.de/code-research/tira/tira-user-ir-lab-sose-2023-<YOUR-GROUP-NANME>/milestone-02:0.0.1
```

Click on "Docker Submission" -> "Add Container" and specify the docker image and the to-be-executed command.
E.g., for the `pyterrier-bm25.ipynb` notebook, the output form should look like this:

![Screenshot_20230502_200700](https://user-images.githubusercontent.com/10050886/235749533-d710cf36-c097-4c23-96de-56d746073ca8.png)

After you have added the software, you can run it by specifying the dataset on which it should run (your dataset created for milestone 1) and the resources that it gets for its execution:

![Screenshot_20230502_201037](https://user-images.githubusercontent.com/10050886/235749854-262de14a-16ee-4d1e-9fb4-61fd90a943dd.png)

Thats it, congrats for finishing milestone 2 :)

