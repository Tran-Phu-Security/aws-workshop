---
title : "Prepare the source code"
date : 2026-11-27
weight : 1
chapter : false
pre : " <b> 5.4.1. </b> "
---

Before creating any AWS resources, confirm that the source code runs correctly on the local machine and build the deployment package for the two worker functions.

#### Run the test suite

Open PowerShell, change to the `backend` folder of the source code cloned in section 5.2, and run:

    cd AWS-Phishing-Detection\backend
    python -m unittest discover -s tests -t .

The test suite needs no network connection or AWS account. A successful run ends with the line `Ran ... tests` followed by `OK`.

![Test suite results](/aws-workshop/images/5-Workshop/5.4-Lab-Guide/5.4.1-source/unittest-ok.png)

The output may contain `Traceback` lines. These are logs from error cases that the tests trigger on purpose and are not real failures. The result is determined by the last line.

#### Build the deployment package

In the `backend` folder, run:

    python build_zip.py

The command creates `dist\detect-workers.zip` (about 33 KB). The package contains the two handlers `fast_detect_worker.py` and `file_detect_worker.py` at the root of the zip, together with the `common` and `detectors` folders.

`FastDetectWorker` and `FileDetectWorker` share this package. `IngestLambda` and `StatusLambda` are each a single Python file, `backend\ingest_lambda.py` and `backend\status_lambda.py`, and need no packaging.

#### Result

+ The test suite completes with status `OK`.
+ The file `backend\dist\detect-workers.zip` has been created.