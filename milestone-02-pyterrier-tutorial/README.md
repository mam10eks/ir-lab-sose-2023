# PyTerrier Tutorial for Milestone 2

This is the PyTerrier tutorial that we will cover in the IR lab.
Everything is copied from the official tutorial for PyTerrier but adopted for use in the course.

We assume you have cloned the repository and have switched your directory to the directory where this notebook is located:

```
git clone git@github.com:mam10eks/ir-lab-sose-2023.git
cd ir-lab-sose-2023/tree/main/milestone-02-pyterrier-tutorial
```

In this directory, please start the Jupyter notebook on your system via:

```
docker run -p 8888:8888 --rm -ti -v ${PWD}:/workspace --entrypoint jupyter webis/tira-ir-starter-pyterrier:0.0.2-base  notebook --allow-root --ip 0.0.0.0
```

In the IR course, you could develop any document representation for the IR Anthology (the corpus of the lab) that you like (in milestone 1). To simplify this tutorial, we use here the document representation of the sample solution for milestone 1.

