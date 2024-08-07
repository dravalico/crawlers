![Docker](https://img.shields.io/badge/Docker-2CA5E0?style=for-the-badge&logo=docker&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)

# CVE crawler

This is a web crawler that downloads all existing [CVE from MITRE](https://cve.mitre.org/). It can be used both as a
normal Python program and via Docker, whose container can be obtained
from [here](https://hub.docker.com/r/dravalico/cve-crawler).

## Parameters

When it starts downloading for the first time, it will start from the first existing CVEs, dating back to 1999. If its
execution is interrupted, at its next execution, if the output folder is the same, it will start again from the last
downloaded one. This functionality is implemented thanks to a hidden file, called `.index.txt` which stores the last
index used in the API query, so it is important not to delete it.
If all CVEs are downloaded completely, the script will download new data once every `update_interval`
seconds (default is 7200 seconds). To store the date of the last request made, there is another hidden file,
called `.timestamp.txt`, which stores the last timestamp when the API request was executed. Like the previous one, it is
important to maintain it.

it is also possible to configure the `path_storage` path (default is `/usr/src/data`) in which to save the downloaded
files, the timeout `request_timeout` for a single request (default is 60 seconds), the `retry_interval` time (default
is 300 seconds), in case the rate limit is reached and the `retries_for_request` (default is 9 but is from 0 to 9), for
the request that raises some errors but maybe can be obtained. Finally, there is also `interval_between_requests`
(default is 6 seconds), suggested by NIST as `update_interval`.

## Usage

Running without Docker can be done as a regular Python program (conda or venv). Simply install the requirements via `pip
install requirements.txt`.

Running via [Docker](https://www.docker.com/), however, obviously requires its installation. The build must then be
performed via:

```
docker build --tag <tag> .
```

Subsequently, you can run the application via:

```
docker run --restart unless-stopped -v <local output directory>:/usr/src/data <tag> python CVECrawler.py --mode <mode>
```

### Output folder

The data is saved in the specified folder and are arranged in a tree. One folder is created per year and the
subsequent folders are created within it starting from the first two digits of the CVE (to which two leading zeros must
be added). Within each of these folders, more are created using the second two digits. The data of each published CVE is
saved in a JSON file, which also presents the raw data of each reference page.