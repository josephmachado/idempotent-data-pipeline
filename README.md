# Introduction

This is the code for blog at https://www.startdataengineering.com/post/why-how-idempotent-data-pipeline/

## Setup

We use a python 3.9 and pandas docker container to run our code. Please have [Docker](https://docs.docker.com/get-docker/) installed.

```bash
git clone https://github.com/josephmachado/idempotent-data-pipeline.git
cd idempotent-data-pipeline
docker pull amancevice/pandas
docker run -it -v $(pwd):/var/lib/pandas amancevice/pandas sh
pip install pyarrow
pip install fastparquet
```

`pyarrow` and `fastparquet` are required for writing to parquet files from pandas.

## Running the code

Once you are inside the docker container. You can run the following commands

```python
python parking_violation_data_pipeline.py --input-file ./data/pv_2018_sample.csv --output-loc ./data/out --run-id 20210519
ls -ltha data/out/20210519/ | grep 99 # data with registration state 99 present

# filter out registration state 99 and rerun the data pipeline
python parking_violation_data_pipeline_w_filter.py --input-file ./data/pv_2018_sample.csv --output-loc ./data/out --run-id 20210519
ls -ltha data/out/20210519/ | grep 99 # data with registration state 99 still present; this is from the previous run and is incorrect

# filter out registration state 99 and rerun the idempotent data pipeline
python parking_violation_data_pipeline_idempotent.py --input-file ./data/pv_2018_sample.csv --output-loc ./data/out --run-id 20210519
ls -ltha data/out/20210519/ | grep 99 # data with registration state 99 not present anymore; this is correct

exit
```

For more details please checkout the post here https://www.startdataengineering.com/post/why-how-idempotent-data-pipeline/

## References

> Pandas installed on Debian/Alpine Linux, GitHub repository, https://github.com/amancevice/docker-pandas

> Parking violation 2018 dataset, Kaggle, https://www.kaggle.com/new-york-city/ny-parking-violations-issued?select=parking-violations-issued-fiscal-year-2018.csv
