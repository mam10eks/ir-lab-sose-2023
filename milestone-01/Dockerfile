FROM webis/tira-ir-datasets-starter:0.0.54

# Add additional dependencies: jupyter so that we can run a notebook directly in the container and jq that we can easily look into the data
RUN apk add jq libffi-dev && pip3 install jupyter 

COPY ir_anthology.py ir-anthology-processed.jsonl milestone-01.ipynb  topics.xml qrels.txt /usr/lib/python3.8/site-packages/ir_datasets/datasets_in_progress/

ENTRYPOINT [ "/irds_cli.sh" ]

