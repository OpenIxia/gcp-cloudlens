# CloudLens Quick Deployment Guide for Google Cloud

## Workflow

The workflow for getting started with CloudLens Visibility Platform on
GCP is as follows:

1.  Deploy CloudLens Manager as a GCP Compute instance

2.  Log in to CloudLens Manager as an Operational user

3.  In CloudLens Manager, create a project

4.  Launch CloudLens agents on compute instances you want to use for
    monitoring and analysis (tool-hosting VMs) to receive mirrored
    traffic via encrypted tunnels. If your tool doesn’t support
    CloudLens agent deployment, use one of the CloudLens unencrypted
    tunneling options to forward mirrored traffic to tool-hosting VMs

5.  Deploy CloudLens Collector GCP Compute instances and configure GCP
    Packet Mirroring sessions for workloads you want to monitor (tapped
    VMs)

6.  On the CloudLens project page, define groups for the instances:
    Instance groups for the tapped VMs, Monitoring Tool groups for the
    tool-hosting VMs. Connect the Instance and Monitoring Tool groups to
    each other to create a monitoring policy. Configure the connection
    for the type of traffic you want to send

## Deploying CloudLens Manager

CloudLens Manager can be deployed on the following platforms:

-   Amazon AWS

-   Google Cloud

-   Microsoft Azure

-   VMware ESXi

-   Linux KVM

For deployment on Google Cloud Platform (GCP), CloudLens Manager is
supplied as a VMDK file.

To deploy CloudLens Manager on Google Cloud:

1.  Download the VMDK file from the [Keysight support
    website](https://support.ixiacom.com/support-overview/product-support/downloads-updates),
    CloudLens / CloudLens Virtual TAP (vTAP) section. GCP deployment is
    supported starting with CloudLens version 6.0.0

2.  Log in to Google Cloud

3.  Import the file into Cloud Storage:

    1.  Open Storage

    2.  Click Create Bucket

    3.  Open the new bucket, click Upload files, and select the VMDK
        file

> <img src="media/image1.jpg" style="width:6.30208in;height:1.75in" />

4.  Create an image from the VMDK file:

    1.  Click Compute Engine \| Storage \| Images

    2.  Click Create Image

    3.  Specify a name for your Compute Image

    4.  Select Virtual Disk (VMDK, VHD) as Source

    5.  Specify the path to the storage file

    6.  **Important!** Do not select an operating system

    7.  Click Create

> <img src="media/image2.jpg" style="width:5.0625in;height:4.95833in" />

5.  Create a CloudLens Manager compute instance based on the imported
    image:

    1.  Click Actions

    2.  Click Create Instance

> <img src="media/image3.jpg" style="width:6.30208in;height:1.91667in" />

3.  Select at least a 4GB CPU and 16GB of RAM

4.  Select Allow HTTPS traffic in the Firewall section

5.  Click Create Instance

<!-- -->

6.  To access CloudLens Manager, open a web browser and enter instance's
    IP address in the URL field (&gt;). It may take up some time for
    CloudLens Manager Web UI to initialize

## Logging in

The default credentials for the CloudLens admin account are:

-   Username admin

-   Password Cl0udLens@dm!n

After you login, the initial page that displays depends on whether you
logged in as an admin user or an operational user:

-   If you logged in as an admin user, the Cluster Statistics page
    displays as the initial page. You can view the statistics on this
    page or use the other pages to perform admin tasks in the CloudLens
    Manager deployment. The first task is to create an account for an
    operational user

-   Once you logged in as an operational user, the Project wizard
    displays. You can use the wizard to guide you in creating a project,
    or you can skip the wizard. If you decide to skip the wizard, you
    can create your project manually

## CloudLens for operational users

When you login to CloudLens Manager for the first time, the project
wizard starts, and takes you through the process of creating a new
project. The wizard guides you in:

-   Setting up a sample tool (ntopng) that you can use to monitor
    traffic

-   Integrating CloudLens Agents into your application workloads

-   Creating a tool group that contains the ntopng instance

-   Creating an instance group that contains your application instances

-   Connecting the instance group to the tool group to establish a
    traffic monitoring policy

In this guide we are going to skip the wizard. For Google Cloud
deployments, it is recommended to leverage GCP Packet Mirror service
instead of integrating CloudLens Agents into application workloads.

1.  Create new, empty CloudLens project in CloudLens Manager

<img src="media/image4.png" style="width:3.56in;height:2.37345in" alt="Graphical user interface, application Description automatically generated" />

2.  Open the project, and click SHOW PROJECT KEY

<img src="media/image5.png" style="width:5.5767in;height:1.32101in" alt="A picture containing graphical user interface Description automatically generated" />

3.  Copy the project key, we will need it in the next sections, when
    launching CloudLens Agents

## Launching a tool

CloudLens supports the following methods of delivering mirrored traffic
to tool-hosting VMs:

-   VxLAN

-   GRE

-   ERSPAN

-   VLAN (not applicable for public cloud)

-   Encrypted overlay between CloudLens Agents

Configuration of tool-hosting VMs required to receive traffic from
CloudLens varies depending on the tool capabilities, as well as a
delivery method. If CloudLens Agents are used to receive traffic, a tool
side configuration can be greatly simplified. For the purposes of this
guide, we will use a sample tool with CloudLens Agent and tcpdump
utility for traffic capture.

To deploy the sample tool:

1.  Create a GCP Compute instance

2.  Go to "Boot disk" section, click change, then select Ubuntu 18.04
    LTS. If you need to use a different Linux distribution, please
    modify CloudLens Agent deployment script below according to
    Docker-CE installation instructions for your O/S choice.

<img src="media/image6.png" style="width:4.248in;height:1.27764in" alt="Graphical user interface, text, application, chat or text message Description automatically generated" />

3.  Go to "Identity and API access" section ,select "Set access for each
    API", then give "Read only" access to "Compute Engine"

<img src="media/image7.png" style="width:4.27518in;height:5.51944in" alt="Graphical user interface, text, application, email Description automatically generated" />

4.  Add CloudLens Agent deployment script to Startup section. Replace
    cl-ip-address with CloudLens Manager VM IP address or FQDN. Replace
    &lt;cl-project-key&gt; with CloudLens Project Key. This command
    ignores CloudLens TLS certificate validation. To enable validation,
    refer to CloudLens Manager User Guide.

sudo mkdir /etc/docker ; sudo touch /etc/docker/daemon.json; sudo bash
-c 'echo "{\\"insecure-registries\\":\[\\"&lt;cl-ip-address&gt;\\"\]}"
&gt;&gt; /etc/docker/daemon.json'; sudo apt-get update -y; sudo apt-get
install apt-transport-https ca-certificates curl gnupg-agent
software-properties-common -y; curl -fsSL
https://download.docker.com/linux/ubuntu/gpg \| sudo apt-key add - ;
sudo add-apt-repository "deb \[arch=amd64\]
https://download.docker.com/linux/ubuntu $(lsb\_release -cs) stable" ;
sudo apt-get update -y ; sudo apt-get install docker-ce docker-ce-cli
containerd.io -y; sudo docker run -v /var/log:/var/log/cloudlens -v
/:/host -v /var/run/docker.sock:/var/run/docker.sock -v
/lib/modules:/lib/modules --privileged --name cloudlens-agent -d
--restart=on-failure --net=host --log-opt max-size=50m --log-opt
max-file=3 &lt;cl-ip-address&gt;/sensor --accept\_eula yes --ssl\_verify
no --project\_key &lt;cl-project-key&gt; --server &lt;cl-ip-address&gt;

<img src="media/image8.png" style="width:4.2875in;height:4.18925in" alt="Graphical user interface, text, application, email Description automatically generated" />

5.  Fill in the rest of the form and then click Create

6.  Once the tool instance is up and running, the number of Instances
    under the CloudLens Project should increase

<img src="media/image9.png" style="width:5.32869in;height:1.15285in" alt="Graphical user interface Description automatically generated with medium confidence" />

## Launching sensors

You can launch new Linux or Windows sensors for tapping or for adding to
a tool group.

### Collector mode

In public cloud deployments, you can run sensors in collector mode. In
collector mode, the sensor receives all the traffic information for the
monitored instances from the cloud provider using vTap and forwards it
to another sensor running as a tool or to a static destination for
analysis.

### Google Cloud Packet Mirroring

To use the Google Cloud Packet Mirroring feature
([<u>https://cloud.google.com/vpc/docs/packetmirroring</u>](https://cloud.google.com/vpc/docs/packet-mirroring)),
you configure the sensor as a collector, and it then discovers all the
traffic mirror sessions and the sources attached to that target
instance.

You can add Google Cloud instances by their name, by a tag, or by a
subnet.

<img src="media/image10.jpg" style="width:5.76042in;height:3.76042in" alt="Diagram Description automatically generated" />

The collector instance is transparent and does not display in CloudLens
Manager.

However, all the instances that are forwarding traffic through the vnet
tap towards the collector are visible in CloudLens Manager, as if they
have sensors installed on them.

To function as a collector, the sensor must have the --runmode parameter
set to collector.

If --runmode is omitted or is set to a value other than collector, the
sensor functions as a standard (non-collector) instance.

The procedures for installing and configuring the sensor as a collector
are described in CloudLens Manager . To display them:

1.  Select the Settings icon.

2.  Select Deploy Guide.

3.  Select Install Google CloudLens Collector.

### Linux installation

To launch a Linux sensor:

1.  Open a console window to the Linux VM and access the command line.

2.  In CloudLens Manager, display the project that you want to add the
    sensor to.

3.  On the project's Configuration page, click Launch Agent.

4.  The Start new agents window displays.

> <img src="media/image11.jpg" style="width:6.63542in;height:3.55208in" />

5.  Copy the sample Docker command from the Start new agents window and
    paste it into the Linux VM's console window.

6.  Modify the command parameters as necessary.

7.  Execute the Docker command.

After a short delay, the new sensor will display in the project list of
sensors.

## Firewall ports

This page describes the TCP and UDP ports that must open for CloudLens
Manager.

### Sensors

The following ports must be open so that sensors can communicate with
CloudLens Manager:

| **Protocol** | **Port Number** | **Direction**        | **Host**                         |
|--------------|-----------------|----------------------|----------------------------------|
| TCP          | 443             | Inbound and Outbound | CloudLens Manager IP or hostname |

### Tap groups and tool groups

Depending on the encapsulation selected for the connection between a tap
group containing tap sensors and a tool group containing tool sensors or
static destinations, the following ports and protocols must be open:

| **Connection**             | **Protocol**      | **Port** | **Direction for Tap**      | **Direction for Tool / Static Destination** |
|----------------------------|-------------------|----------|----------------------------|---------------------------------------------|
| VXLAN                      | UDP               | 4789     | Outbound                   | Inbound                                     |
| GRE                        | GRE (IP Proto 47) | N/A      | Outbound                   | Inbound                                     |
| ERSPAN                     | GRE (IP Proto 47) | N/A      | Outbound                   | Inbound                                     |
| VLAN                       | 802.1Q            | N/A      | Outbound                   | Inbound                                     |
| Encrypted (see note below) | UDP               | 9993     | Outbound to Internet       | Outbound to Internet                        |
| Encrypted (see note below) | UDP               | 19993    | Inbound / Outbound to tool | Inbound / Outbound to tap                   |

> **Note:** For more information about the Encrypted connection, see
> [<u>https://zerotier.atlassian.net/wiki/spaces/SD/pages/6815768/Router+Configuration+Tips</u>](https://zerotier.atlassian.net/wiki/spaces/SD/pages/6815768/Router+Configuration+Tips)
