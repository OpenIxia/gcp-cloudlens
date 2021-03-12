# CloudLens Quick Deployment Guide for Google Cloud

## Workflow

The workflow for getting started with CloudLens Visibility Platform on
GCP is as follows:

1.  Deploy CloudLens Manager as a GCP Compute instance

2.  Log in to CloudLens Manager as an Operational user

3.  In CloudLens Manager, create a project

4.  Deploy Google CloudLens Collector instances and configure GCP Packet
    Mirroring sessions for workloads you want to monitor (tapped VMs)

5.  Launch CloudLens agents on compute instances you want to use for
    monitoring and analysis (tool-hosting VMs) to forward mirrored
    traffic via encrypted tunnel. If your tool doesn’t support CloudLens
    agent deployment, use one of the CloudLens tunneling options to send
    mirrored traffic to tool-hosting VMs without additional encryption

6.  On the project page, define groups for the instances: instance
    groups for the tapped VMs, monitoring tool groups for the
    tool-hosting VMs. Connect the instance and monitoring tool groups to
    each other to create a monitoring policy. Configure the connection
    for the type of traffic you want to send (the connection properties)

## Deploying CloudLens Manager

CloudLens Manager can be deployed on the following platforms.

-   VMware ESXi (standalone or vCenter)

-   KVM

-   Amazon AWS

-   Google Cloud

-   Microsoft Azure

For deployment on Google Cloud Platform (GCP), CloudLens Manager is
supplied as a VMDK file.

To deploy CloudLens Manager on Google Cloud:

1.  Download the VMDK file from the [Keysight support
    website](https://support.ixiacom.com/support-overview/product-support/downloads-updates),
    CloudLens / CloudLens Virtual TAP (vTAP) section. GCP deployment is
    supported starting with CloudLens version 6.0.0.

2.  Log in to Google Cloud.

3.  Import the file into Cloud Storage:

    1.  Open Storage.

    2.  Click Create Bucket.

    3.  Open the new bucket, click Upload files, and select the VMDK
        file.

> <img src="media/image1.jpg" style="width:6.30208in;height:1.75in" />

4.  Create an image from the VMDK file:

    1.  Click Compute Engine \| Storage \| Images.

    2.  Click Create Image.

    3.  Specify a name for your VM.

    4.  Select the Source Virtual Disk you want to use.

    5.  Specify the path to the storage file.

    6.  **Important!** Do not select an operating system.

    7.  Click Create.

> <img src="media/image2.jpg" style="width:5.0625in;height:4.95833in" />

5.  Create an instance based on the imported image:

    1.  Click Actions.

    2.  Click Create Instance.

> <img src="media/image3.jpg" style="width:6.30208in;height:1.91667in" />

3.  Select at least a 4GB CPU and 16GB of RAM.

4.  Click Create Instance.

<!-- -->

6.  To access CloudLens Manager, open a web browser and enter instance's
    IP address in the URL field (&gt;). It may take up some time for
    CloudLens Manager Web UI to initialize.

## Logging in

The default credentials for the CloudLens admin account are:

| username: | admin          |
|-----------|----------------|
| password: | Cl0udLens@dm!n |

After you login, the initial page that displays depends on whether you
logged in as an admin user or an operational user:

-   If you logged in as an admin user, the Cluster Statistics page
    displays as the initial page. You can view the statistics on this
    page or use the other pages to perform admin tasks in the CloudLens
    Manager deployment.

-   If you logged in as an operational user, the Project wizard
    displays.You can use the wizard to guide you in creating a project,
    or you can skip the wizard. If you decide to skip the wizard, you
    can create your project manually.

## CloudLens for operational users

When you login to CloudLens Manager for the first time, the project
wizard starts, and takes you through the process of creating a new
project. The wizard guides you in:

-   Setting up a sample tool (ntopng) that you can use to monitor
    traffic

-   Integrate the sensor into your application by using Docker

-   Creating a tool group that contains the ntopng instance

-   Creating an instance group that contains your application instances

You can run through the wizard, or skip it and re-run it at a later
time.

After you login and the project wizard displays, choose one:

-   Run the through the wizard and create your projects

-   Skip the wizard and create your project manually, or perform other
    tasks

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

<img src="media/image4.jpg" style="width:5.76042in;height:3.76042in" alt="Diagram Description automatically generated" />

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

> <img src="media/image5.jpg" style="width:6.63542in;height:3.55208in" />

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
