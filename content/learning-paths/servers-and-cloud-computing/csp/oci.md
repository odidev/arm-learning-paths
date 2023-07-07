---
# User change
title: "Getting Started with Oracle OCI"

weight: 6 # 1 is first, 2 is second, etc.

# Do not modify these elements
layout: "learningpathall"
---
[Oracle Cloud Infrastructure (OCI)](https://oracle.com/cloud/) is a public cloud computing platform. 

As with most cloud service providers, OCI offers a pay-as-you-use [pricing policy](https://www.oracle.com/cloud/pricing/), including a number of [free](https://www.oracle.com/cloud/free/) services.

This guide is to help you get started with [compute services](https://www.oracle.com/cloud/compute/), using Arm-based [Ampere](https://www.oracle.com/cloud/compute/arm/) processors. This is a general-purpose compute platform, essentially your own personal computer in the cloud.

Detailed instructions are available in the Oracle [documentation](https://docs.oracle.com/en-us/iaas/Content/Compute/References/arm.htm#create-instances).

## Create an account

Before you start, creating an account. For a personal account, click on [Sign in to Oracle Cloud](https://www.oracle.com/cloud), and follow the on-screen instructions to log in or register as a new user. You will get immediate access to [OCI Cloud Free Tier](https://www.oracle.com/cloud/free) services.

If using an organization's account, you will likely need to consult with your internal administrator.

Once logged in, you will be presented with the [Oracle Cloud console](https://docs.oracle.com/en-us/iaas/Content/GSG/Concepts/console.htm). 

## Create compartment

If this is your first time logging in, it is recommended to create a `compartment` to store your compute instances. Search for `Compartments` or navigate to `Identity & Security` > `Compartments` from the menu.

![oci1 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/9c2af280-5b60-49d0-8b4b-8beb1f4adf87 "Navigate to the `Compartments` page")

Use the `Create Compartment` button to start configuring your compartment.

![oci2 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/5c42da46-1686-4ace-aba4-b54ae010d080 "Click on 'Create Compartment'")

Create a compartment with a meaningful name (and optional description), for example `Ampere_A1_instances`. When finished, click `Create Compartment`. Full details on using compartments are described in the Oracle [documentation](https://docs.oracle.com/en-us/iaas/Content/Identity/compartments/managingcompartments.htm).

![oci3 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/98fb388f-96d1-4e69-9c4b-ad32d8b89ff5 "Create a name and description for the compartment")

## Create your compute instance

Search for `Instances` or navigate the menu to `Compute` > `Instances`.

![alt-text #center](https://user-images.githubusercontent.com/97123064/244126707-4c184318-fc42-4906-955b-e9d0796eb269.png "Navigate to the 'Instances' page")

Use the `Create Instance` button to get to the `Create compute instance` dialog.

![alt-text #center](https://github.com/odidev/arm-learning-paths/assets/40816837/e01421dc-e397-4e84-929b-89971baf7534 "Click on 'Create instance'")

### Name your instance

Give your instance a meaningful, but arbitrary, name. This is particularly useful when creating multiple instances. Select the `compartment` you created above to be the location for this instance.

![oci6 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/bcb91210-c666-41e9-980f-20e8760d03bb "Specify a name for the instance and select your compartment")

### Placement

These settings are generally left as default. See the Oracle [documentation](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm) for a full description.

![oci7 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/1c6218ea-6d88-4410-bb3c-6de61783f4a7 "Choose availability domain placement")

### Select instance image and shape

Click the `Edit` button to configure which OS image and instance shape you will use.

![oci8 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/b901a900-ab76-40c9-876f-49b881911c7e "Click 'Edit' to change the image and shape")

Click `Change image` to select the operating system image. 

![oci9 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/83f28565-52c7-46e1-a057-2cabbffe89aa "Click `Change image'")

Several images are available from the [marketplace](https://cloudmarketplace.oracle.com/marketplace), but for now, select a standard image, such as `Canonical Ubuntu 22.04` from the list of `Platform images`. Click `Select image` to confirm.

![oci10 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/ad7f2637-1e66-47f2-965b-5a1d325389a7 "Choose a standard image")

Click `Change shape`, to select the instance type and processor series.

![oci11 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/62f68a19-ec5e-4e85-8ca1-fc5b4fa24247 "Click `Change shape'")

Select the `Ampere` Arm-based processor shape series. Select the `Shape name` (for example `VM.Standard.A1.Flex`), and use slider to select the number of processors you wish to use. At the time of writing, the free tier allows up to 4 Ampere processors (across all instances) to be used. Click `Select shape` to confirm.

![oci12 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/26c13dfa-f0ed-4b70-b945-7f21945b8e90 "Choose an Ampere Arm-based processor shape")

### Networking

These settings can likely be left as default. Ensure that a public IP address is assigned so that you can access the instance.

![oci13 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/17d18c57-8e5a-4298-87ba-2c31971b3184 "Configure network settings if necessary")

### Add SSH keys

To be able to access the instance, you must use a [key pair](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/managingkeypairs.htm). Use the dialog to generate a new key pair and download the private key to your local machine, or you can upload an existing public key if you have one.

Note that if you do not generate a key and have access to the private key, you will not be able to connect to the instance.

![oci14 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/b4cb04f7-7a99-439c-8c48-ec585209ae5f "Create or upload a key pair")

### Boot volume and other advanced options

These can likely be left as default. See the Oracle [documentation](https://docs.oracle.com/en-us/iaas/Content/Block/Concepts/bootvolumes.htm) for settings information if necessary.

![oci15 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/2eba50ca-a796-4264-9fb3-d95b9cb62b5f "Configure boot volume and advanced options if necessary")

### Launch instance

When all options are set, click `Create` to get started. 

![oci16 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/19da75be-305c-42a1-b4b7-320976270723 "Create the VM instance")

Your compute instance will be created and be available after initialization, when status is shown as `RUNNING`. Note the `Public IP address` and `Username` of your instance as you will need these to connect to your instance.

![oci17 #center](https://github.com/odidev/arm-learning-paths/assets/40816837/f6152167-c298-4aa9-8ab9-1488d60403f8 "Confirm the instance is running and note instance details")

## Connect to your instance

You can connect to the instance with your preferred SSH client. For example, if using `ubuntu` image:

```console
ssh -i <private_key> ubuntu@<public_ip_address>
```

{{% notice Note %}}
Replace `<private_key>` with the private key on your local machine and `<public_ip_address>` with the public IP of the target VM.
{{% /notice %}}

Terminal applications such as [PuTTY](https://www.putty.org/), [MobaXterm](https://mobaxterm.mobatek.net/) and similar can be used.

Detailed instructions are given in the Oracle [documentation](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/accessinginstance.htm).

Once connected, you are now ready to use your instance.

## Explore your instance

### Run uname

Use the [uname](https://en.wikipedia.org/wiki/Uname) utility to verify that you are using an Arm-based server. For example:
```console
uname -m
```
will identify the host machine as `aarch64`.

### Run hello world

Install the `gcc` compiler. Assuming you are using `Ubuntu`, use the following commands. If not, refer to the [GNU compiler install guide](/install-guides/gcc):

```console
sudo apt-get update
sudo apt install -y gcc
```

Using a text editor of your choice, create a file named `hello.c` with the contents below:

```C
#include <stdio.h>
int main(){
    printf("hello world\n");
    return 0;
}
```
Build and run the application:

```console
gcc hello.c -o hello
./hello
```

The output is shown below:

```output
hello world
```
