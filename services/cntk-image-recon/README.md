[issue-template]: ../../../../../issues/new?template=BUG_REPORT.md
[feature-template]: ../../../../../issues/new?template=FEATURE_REQUEST.md

![singnetlogo](../../docs/assets/singnet-logo.jpg 'SingularityNET')

# CNTK Image Recognition

This service uses [CNTK Image Recognition](https://cntk.ai/pythondocs/CNTK_301_Image_Recognition_with_Deep_Transfer_Learning.html) to perform image recognition on photos.

It is part of our third party [DNN Model Services](https://github.com/singnet/dnn-model-services).

## Getting Started

### Requirements

- [Python 3.6.5](https://www.python.org/downloads/release/python-365/)
- [SNET CLI](https://github.com/singnet/snet-cli)
- Pre-trained models (dogs and flowers)

### Development

Clone this repository and download the models using the `get_cntk_models.sh` script:

```
$ git clone https://github.com/singnet/dnn-model-services.git
$ cd dnn-model-services/utils
$ ./get_cntk_models.sh
$ ls -la Resources/Models
total 458416
drwxrwxr-x 2 user user      4096 Nov  8 08:49 .
drwxrwxr-x 3 user user      4096 Nov  8 08:49 ..
-rw-rw-r-- 1 user user 234830033 Ago 28 17:28 dogs_ResNet152_20.model
-rw-rw-r-- 1 user user 234574954 Ago 28 15:57 flowers_ResNet152_20.model
$ cd ../services/cntk-image-recon
```

### Running the service:

To get the `ORGANIZATION_ID` and `SERVICE_ID` you must have already published a service (check this [link](https://dev.singularitynet.io/tutorials/publish/)).

Create the `SNET Daemon`'s config JSON file (`snetd.config.json`).

```
{
   "DAEMON_END_POINT": "DAEMON_HOST:DAEMON_PORT",
   "IPFS_END_POINT": "http://ipfs.singularitynet.io:80",
   "BLOCKCHAIN_NETWORK_SELECTED": "BLOCKCHAIN_NETWORK",
   "PASSTHROUGH_ENABLED": true,
   "PASSTHROUGH_ENDPOINT": "http://SERVICE_GRPC_HOST:SERVICE_GRPC_PORT",  
   "ORGANIZATION_ID": "ORGANIZATION_ID",
   "SERVICE_ID": "SERVICE_ID",
   "LOG": {
       "LEVEL": "debug",
       "OUTPUT": {
            "TYPE": "stdout"
           }
   }
}
```

For example (using the Ropsten testnet):

```
$ cat snetd.config.json
{
   "DAEMON_END_POINT": "0.0.0.0:7054",
   "IPFS_END_POINT": "http://ipfs.singularitynet.io:80",
   "BLOCKCHAIN_NETWORK_SELECTED": "ropsten",
   "PASSTHROUGH_ENABLED": true,
   "PASSTHROUGH_ENDPOINT": "http://localhost:7003",
   "ORGANIZATION_ID": "snet",
   "SERVICE_ID": "cntk-image-recon",
   "LOG": {
       "LEVEL": "debug",
       "OUTPUT": {
           "TYPE": "stdout"
           }
   }
}
```

Note that we set `DAEMON_HOST = 0.0.0.0` because this service will run inside a Docker container.

Install all dependencies:
```
$ pip3 install -r requirements.txt
```
Generate the gRPC codes:
```
$ sh buildproto.sh
```
Start the service and `SNET Daemon`:
```
$ python3 run_image_recon_service.py
```

### Calling the service:

Inputs:
  - `gRPC method`: flowers or dogs.
  - `model`: DNN Model ("ResNet152").
  - `img_path`: An image URL.

Local (testing purpose):

```
$ python3 test_image_recon_service.py 
Endpoint (localhost:7003): 
Method (flowers|dogs): flowers
Model (ResNet152): <ENTER>
Image (Link): https://www.fiftyflowers.com/site_files/FiftyFlowers/Image/Product/Mini-Black-Eye-bloom-350_c7d02e72.jpg
1.8751
{1: '98.93%: sunflower', 2: '00.64%: black-eyed susan', 3: '00.16%: barbeton daisy', 4: '00.14%: oxeye daisy', 5: '00.03%: daffodil'}

$ python3 test_image_recon_service.py 
Endpoint (localhost:7003): 
Method (flowers|dogs): dogs
Model (ResNet152): <ENTER>
Image (Link): https://cdn2-www.dogtime.com/assets/uploads/2011/01/file_22950_standard-schnauzer-460x290.jpg
1.5280
{1: '99.83%: Miniature_schnauzer', 2: '00.09%: Alaskan_malamute', 3: '00.05%: Giant_schnauzer', 4: '00.01%: Bouvier_des_flandres', 5: '00.01%: Lowchen'}
```

Through SingularityNET (follow this [link](https://dev.singularitynet.io/tutorials/publish/) to learn how to publish a service and open a payment channel to be able to call it):

Assuming that you have an open channel to this service:

```
$ snet client call snet cntk-image-recon default_group flowers '{"model": "ResNet152", "img_path": "https://www.fiftyflowers.com/site_files/FiftyFlowers/Image/Product/Mini-Black-Eye-bloom-350_c7d02e72.jpg"}'
Price for this call will be 0.00000001 AGI (use -y to remove this warning). Proceed? (y/n): y
delta_time: "2.5509"
top_5: "{1: \'98.93%: sunflower\', 2: \'00.64%: black-eyed susan\', 3: \'00.16%: barbeton daisy\', 4: \'00.14%: oxeye daisy\', 5: \'00.03%: daffodil\'}"
```

## Contributing and Reporting Issues

Please read our [guidelines](https://dev.singularitynet.io/docs/contribute/contribution-guidelines/#submitting-an-issue) before submitting an issue. If your issue is a bug, please use the bug template pre-populated [here][issue-template]. For feature requests and queries you can use [this template][feature-template].

## Authors

* **Artur Gontijo** - *Maintainer* - [SingularityNET](https://www.singularitynet.io)