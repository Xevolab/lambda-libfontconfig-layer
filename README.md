# lambda-libfontconfig-layer

> Huge credits to:
> [Alberto Cubeddu on Medium](https://medium.com/creditorwatch/aws-lambda-fonts-libfontconfig-5e837281a4ce).
> 
> Most of the code in this repo has been taken from his post, with a couple of adjustments.

This repo explains how to install and compile libfontconfig into a AWS Lambda layer to be used by your functions.

We'll be using a Dokcer image.

```bash
docker pull amazonlinux:2
docker run --name build-fontconfig -d -t amazonlinux:2 cat
docker exec -it build-fontconfig bash

yum install git freetype-devel gettext-devel make expat gperf python3-pip libxml2-devel libtool zip -y
python -m ensurepip --upgrade

cd /tmp
git clone http://anongit.freedesktop.org/git/fontconfig
cd fontconfig
git checkout -b 2.12.4 refs/tags/2.12.4

pip install lxml
pip install six

./autogen.sh --enable-libxml2
make
make install

mkdir -p package/lib
cp /usr/local/lib/{libfontconfig.so,libfontconfig.so.1,libfontconfig.so.1.9.4} /root/package/lib
cd /root/package/
zip -r9 ../package.zip *

exit

docker cp build-fontconfig:/root/package.zip package.zip
```

A _ready to be deployed_ version of the layer is included in this repo.

For more information on how to deploy layers in AWS Lambda, you can refer back to the [AWS documentation](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html).