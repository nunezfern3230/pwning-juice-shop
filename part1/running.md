# Running OWASP Juice Shop

## System requirements

To run a single instance of Juice Shop the following memory and CPU
requirements apply. These resources are needed for the Juice Shop
application process itself, and they are based on
* a 30-day average
process CPU and RAM usage of the official public demo instance running `v14.0.1` of Juice Shop
* a training with ~30 participants on a [MultiJuicer](../appendix/trainers.md#hosting-individual-instances-for-multiple-users) cluster hosted on OpenShift with Juice Shop `v14.0.1`

Please note that any additional resources needed by your environment (e.g. Docker or Vagrant) come on top.

* **Minimum** system specification
  * `256 MB` RAM
  * `200 millicpu` CPU
  * `300 MB` free disk space
* **Recommended** system specification
  * `384 MB` RAM
  * `400 millicpu` CPU
  * `800 MB` free disk space

🗄️ _If installing [from sources](#from-sources) without the ` --depth 1` option, an additional `700
MB` free disk space are required for the Git history in both minimum and
recommended spec._

## Run options

In the following sections you find step-by-step instructions to deploy a
running instance of OWASP Juice Shop for your personal hacking
endeavours.

### Local installation

To run the Juice Shop locally you need to have
[Node.js](http://nodejs.org/) installed on your computer. The Juice Shop
officially runs on versions {{book.nodeVersions}} of Node.js, closely
following the official
[Node.js Long-term Support Release Schedule](https://github.com/nodejs/LTS).
During development and Continuous Integration (CI) the application is
automatically tested with these current versions of Node.js. The
officially recommended version to run Juice Shop is either the most
recent _Long-term Support (LTS)_ version or the _Current Release_
version. Therefore Juice Shop recommends Node.js
{{book.recommendedNodeVersion}} for its own {{book.juiceShopVersion}}
release.

#### From sources

1. Install [Node.js](http://nodejs.org/) on your computer.
2. On the command line run `git clone
   https://github.com/juice-shop/juice-shop.git --depth 1`.
3. Go into the cloned folder with `cd juice-shop`
4. Run `npm install`. This only has to be done before the first start or
   after you changed the source code.
5. Run `npm start` to launch the application.
6. Browse to <http://localhost:3000>

#### From pre-packaged distribution

1. Install a 64bit [Node.js](http://nodejs.org/) on your Windows, MacOS
   or Linux machine.
2. Download `juice-shop-<version>_<node-version>_<os>_x64.zip` (or
   `.tgz`) attached to the
   [latest release on GitHub](https://github.com/juice-shop/juice-shop/releases/latest).
3. Unpack the archive and run `npm start` in unpacked folder to launch
   the application
4. Browse to <http://localhost:3000>

### Docker image

You need to have [Docker](https://www.docker.com/) installed to run
Juice Shop as a container inside it. Following the instructions below
will download the current stable version (built from `master` branch on
GitHub) which internally runs the application on the currently
recommended Node.js version {{book.recommendedNodeVersion}}.

1. Install [Docker](https://www.docker.com/) on your computer.
2. On the command line run `docker pull bkimminich/juice-shop` to
   download the `latest` image described above.
3. Run `docker run -d -p 3000:3000 bkimminich/juice-shop` to launch the
   container with that image.
4. Browse to <http://localhost:3000>.

If you are using Docker on Windows - inside a VirtualBox VM - make sure
that you also enable port forwarding from host `127.0.0.1:3000` to
`0.0.0.0:3000` for TCP.

#### Supported architectures

The official Docker image is built automatically during CI/CD for
`linux/amd64` (based on a Node.js {{book.recommendedNodeVersion}} image) as well as `linux/arm64` and `linux/arm/v7` (based on a Node.js 14 image) and both are pushed to DockerHub. The following tagged images are available on DockerHub:

* Stable version from `master` branch: `latest` for AMD/ARM64 and `latest-arm` for ARMv7
* Development version from `develop` branch: `snapshot` for AMD/ARM64 and `snapshot-arm` for ARMv7
* Tagged versioned releases: `vX.Y.Z` for AMD/ARM64 and `vX.Y.Z-arm` for ARMv7

### Vagrant

Vagrant is an open-source solution for building and maintaining virtual
software development environments. It creates a Virtualbox VM that will
launch a Docker container instance of the `latest` Juice Shop image
{{book.juiceShopVersion}}.

1. Install [Vagrant](https://www.vagrantup.com/downloads.html) and
   [Virtualbox](https://www.virtualbox.org/wiki/Downloads)
2. Run `git clone https://github.com/juice-shop/juice-shop.git` (or
   clone [your own fork](https://github.com/juice-shop/juice-shop/fork)
   of the repository)
3. Run `cd vagrant && vagrant up`
4. Browse to [http://192.168.56.110](http://192.168.56.110)

### Amazon EC2 Instance

You need to have an account at
[Amazon Web Services](https://aws.amazon.com) in order to create a
server hosting the Juice Shop there.

1. In the _EC2_ sidenav select _Instances_ and click _Launch Instance_
2. In _Step 1: Choose an Amazon Machine Image (AMI)_ choose an _Amazon
   Linux AMI_ or _Amazon Linux 2 AMI_
3. In _Step 3: Configure Instance Details_ unfold _Advanced Details_ and
   copy the script below into _User Data_
4. In _Step 6: Configure Security Group_ add a _Rule_ that opens port 80
   for HTTP
5. Launch your instance
6. Browse to your instance's public DNS

```
#!/bin/bash
yum update -y
yum install -y docker
service docker start
docker pull bkimminich/juice-shop
docker run -d -p 80:3000 bkimminich/juice-shop
```

#### AWS EC2 Launch Template

1. In the _EC2_ sidenav select _Launch Templates_ and click _Create
   launch template_
2. Under _Launch template contents_ select as _AMI ID_ either _Amazon
   Linux AMI_ or _Amazon Linux 2 AMI_ (by using _Search for AMI_)
3. In the same section add a _Security Group_ that opens port 80 for
   HTTP
4. Unfold _Advanced details_ at the bottom of the screen and paste in
   the script above into _User Data_
5. Create your launch template
6. Launch one or multiple EC2 instances from your template
7. Browse to your instance's public DNS

### Azure Container Instance

1. Open and login (via `az login`) to your
   [Azure CLI](https://azure.github.io/projects/clis/) **or** login to
   the [Azure Portal](https://portal.azure.com), open the _CloudShell_
   and then choose _Bash_ (not PowerShell).
2. Create a resource group by running `az group create --name <group
   name> --location <location name, e.g. "centralus">`
3. Create a new container by running `az container create
   --resource-group <group name> --name <container name> --image
   bkimminich/juice-shop --dns-name-label <dns name label> --ports 3000
   --ip-address public`
4. Your container will be available at `http://<dns name
   label>.<location name>.azurecontainer.io:3000`

#### Azure Web App for Containers

1. Open your [Azure CLI](https://azure.github.io/projects/clis/) **or**
   login to the [Azure Portal](https://portal.azure.com), open the
   _CloudShell_ and then choose _Bash_ (not PowerShell).
2. Create a resource group by running `az group create --name <group
   name> --location <location name, e.g. "East US">`
3. Create an app service plan by running `az appservice plan create
   --name <plan name> --resource-group <group name> --sku S1 --is-linux`
4. Create a web app with the
   [Juice Shop Docker](https://hub.docker.com/r/bkimminich/juice-shop/)
   image by running the following (on one line in the bash shell) `az
   webapp create --resource-group <group name> --plan <plan name> `
   `--name <app name> --deployment-container-image-name
   bkimminich/juice-shop`

### Google Compute Engine Instance

1. Login to the Google Cloud Console and
   [open Cloud Shell](https://console.cloud.google.com/home/dashboard?cloudshell=true).
2. Launch a new GCE instance based on the juice-shop container. Take
   note of the `EXTERNAL_IP` provided in the output.

```
gcloud compute instances create-with-container owasp-juice-shop-app --container-image bkimminich/juice-shop
```

3. Create a firewall rule that allows inbound traffic to port 3000

```
gcloud compute firewall-rules create juice-rule --allow tcp:3000
```

4. Your container is now running and available at
   `http://<EXTERNAL_IP>:3000/`

### Heroku

!["Deploy to Heroku" button](img/deploy-to-heroku.svg)

1. Click the
   _Deploy to Heroku_ button in the
   [_Setup_ section of the `README.md` on GitHub](https://github.com/juice-shop/juice-shop#heroku)

If you have forked the Juice Shop repository on GitHub, the _Deploy to
Heroku_ button will deploy your forked version of the application. To
deploy the latest official version you must use the button of the
original repository at <https://github.com/juice-shop/juice-shop>.

_As a little related anecdote, the OWASP Juice Shop was crowned
[Heroku Button of the Month in November 2017](https://hello.heroku.com/webmail/36622/679286305/8049a634b1a01b0aa75c0966325856dc9a463b7f1beeb6a2f32cbb30248b5bc6)
and once more
[in March 2019](https://hello.heroku.com/webmail/36622/844098776/9fe33b8eda9eb79bca7ee569888b1874):_

!["Heroku Button of the Month" November 2017](img/heroku-button-of-the-month.png)

### Gitpod

1. Login to [gitpod.io](https://gitpod.io) and use <https://gitpod.io/#https://github.com/juice-shop/juice-shop/> to start a new workspace. If you want to spin up a forked repository, your URL needs to be adjusted accordingly.

2. After the Gitpod workspace is loaded, Gitpod tasks is still running to install `npm install`  and launch the website. Despite Gitpod showing your workspace state already as _Running_, you need to wait until the installation process is done, before the website becomes accessable. The _Open Preview Window (Internal Browser)_, will open automatically and refresh itself automatically when the server has started.

3. Your Juice Shop instance is now also available at `https://3000-<GITPOD_WORKSPACE_ID>.<GITPOD_HOSTING_ZONE>.gitpod.io`.

## Installing a specific release version

The installation instructions above will all give you the latest
official release version of the Juice Shop. If you want to install a
specific older version, you can easily do so by retrieving the
corresponding tag from GitHub or Docker. For release v7.5.1 - which was
the last version with the original AngularJS/Bootstrap frontend - for
example:

* [From sources](#from-sources) - Run `git fetch --tags` and then `git
  checkout v7.5.1` before running `npm install`
* [Docker image](#docker-image) - Run `docker pull
  bkimminich/juice-shop:v7.5.1` instead of the usual `docker pull
  bkimminich/juice-shop`
* [From pre-packaged distribution](#from-pre-packaged-distribution) -
  Just download the older release from
  <https://github.com/juice-shop/juice-shop/releases> or
  <https://sourceforge.net/projects/juice-shop/files/>

To experience a preview of the next upcoming Juice Shop version you can
do as follows:

* Simply visit <https://juice-shop-staging.herokuapp.com> and take a
  look
* [From sources](#from-sources) - Run `git fetch` and then `git checkout
  develop` before running `npm install`
* [Docker image](#docker-image) - Run `docker pull
  bkimminich/juice-shop:snapshot` instead of the usual `docker pull
  bkimminich/juice-shop`

ℹ️ Please be aware that support by the core team or community is
limited (at best) for outdated and unreleased versions alike. To fully
enjoy your OWASP Juice Shop experience, it is recommended to always use
the latest version.

## _Self-healing_-feature

OWASP Juice Shop was not exactly designed and built with a high
availability and reactive enterprise-scale architecture in mind. It runs
perfectly fine and fast when it is attacked via a browser by a human.
When under attack by an automated tool - especially aggressive brute
force scripts - the server might crash under the load. This could - in
theory - leave the database and file system in an unpredictable state
that prevents a restart of the application.

That is why - in practice - Juice Shop wipes the entire database and the
folder users might have modified during hacking. After performing this
_self-healing_ the application is supposed to be restartable, no matter
what kind of problem originally caused it to crash. For convenience the
_self-healing_ happens during the start-up (i.e. `npm start`) of the
server, so no extra command needs to be issued to trigger it.

## Single-user restriction

There is one fundamental restriction that needs to be taken into account
when working with the OWASP Juice Shop, especially in group trainings or
lectures:

**A server instance of OWASP Juice Shop is supposed to be used by only a
single-user!**

This restriction applies to all the [Run Options](#run-options)
explained above. It is technically necessary to make the
[_Self-healing_-feature](#self-healing-feature) work properly and
consistently. Furthermore, when multiple users would attack the same
instance of the Juice Shop all their progress tracking would be mixed
leading to inevitable confusion for the individual hacker. The upcoming
[Challenge tracking](challenges.md) chapter will illustrate this topic.

It should not go unmentioned that it is of course okay to have multiple
users hack the same instance from a shared machine in a kind of
_pair-hacking_-style.

If you want to centrally host Juice Shop instances for multiple users
you find more information in section
[Hosting individual instances for multiple users](../appendix/trainers.md#hosting-individual-instances-for-multiple-users)
of the trainer's guide.
