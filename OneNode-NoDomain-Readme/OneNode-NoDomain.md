# Azure Stack HCI, Single Node Deployment Guide # 

This guide describes how to deploy Azure Stack HCI, version 21H2 on a Single Node with no domain requirment via the included Powershell Script and Configuration file.

The target audience for this guide is IT administrators familiar with the existing Azure Stack HCI solution or Developers who wish to have a small on premises environment.

Important:  Please review the terms of use for the preview and sign up before you deploy this solution.

## Prerequisites ##
Here you will find the software, hardware, and networking prerequisites to deploy Azure Stack HCI, version 21H2: 

### Software requirements ###
Before you begin, make sure that the following software requirement is met:

|Component | |  
--| --|
|Operating System |You must install and set up the Azure Stack HCI, version 21H2 OS to boot using the instructions in this [link.](https://docs.microsoft.com/en-us/azure-stack/hci/deploy/operating-system)|  |

### Hardware requirements ### 
Before you begin, make sure that the physical hardware used to deploy the solution meets the following requirements:

|Component | Minimum |  
--| --|
|CPU | A 64-bit Intel Nehalem grade or AMD EPYC or later compatible processor with second-level address translation (SLAT). |
|Memory | A minimum of 32 GB RAM.|
|Host network adapters|At least one network adapter listed in the Windows Server Catalog.|
|BIOS|Intel VT or AMD-V must be turned on.|
|Boot Drive| A minimum size of 128 GB size. |
|Data Drives|At least 1 disks with a minimum capacity of 500 GB(SSD or NVME). |
|TPM |TPM 2.0 hardware must be present and turned on. |
|Secure Boot | Secure boot must be present and enabled|



### Network requirements ###  

Before you begin, make sure that the physical network and the host network where the solution is deployed meet the requirements described in:

•	[Physical network requirements](https://docs.microsoft.com/azure-stack/hci/concepts/physical-network-requirements)

•	[Host network requirements](https://docs.microsoft.com/azure-stack/hci/concepts/host-network-requirements)

Note: Advanced settings within storage network configuration like iWarp or MTU changes are not supported in this release.

## Deploy Azure Stack HCI; Single Node ##

|Step # | Description|
--| --|
|Prerequisites|1. [Software Requirments]() 2.[Hardware requirments]() 3. [Network Requirments]() |
|Step 0| |
|Step 1||
|Step 2||
|Step 3||


## Step-by-step deployment ##

### Step 0 Pre-Deployment Checklist ###
Use the following check list to gather this information ahead of the actual deployment of your Azure Stack HCI cluster. This information will be used to configure the single node and also prepare the Configuration Deployment File, which is named Config.txt. 

### Step 1 Install the Operating System ###

The first step in deploying Azure Stack HCI is to [download Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) and install the operating system on each server that you want to cluster. This article discusses different ways to deploy the operating system, and using Windows Admin Center to connect to the servers.

## Determine hardware and network requirements

Microsoft recommends purchasing a validated Azure Stack HCI hardware/software solution from our partners. These solutions are designed, assembled, and validated against our reference architecture to ensure compatibility and reliability, so you get up and running quickly. Check that the systems, components, devices, and drivers you are using are certified for use with Azure Stack HCI. Visit the [Azure Stack HCI solutions](https://azure.microsoft.com/overview/azure-stack/hci) website for validated solutions.

At minimum, you need one server, a reliable high-bandwidth, low-latency network connection between servers, and SATA, SAS, NVMe, or persistent memory drives that are physically attached to just one server each. However, your hardware requirements may vary depending on the size and configuration of the cluster(s) you wish to deploy. To make sure your deployment is successful, review the Azure Stack HCI [system requirements](../concepts/system-requirements.md).

Before you deploy the Azure Stack HCI operating system:

- Plan your [physical network requirements](../concepts/physical-network-requirements.md) and [host network requirements](../concepts/host-network-requirements.md).
- If your deployment will stretch across multiple sites, determine how many servers you will need at each site, and whether the cluster configuration will be active/passive or active/active. For more information, see [Stretched clusters overview](../concepts/stretched-clusters.md).
- Carefully [choose drives](../concepts/choose-drives.md) and [plan volumes](../concepts/plan-volumes.md) to meet your storage performance and capacity requirements.

For Azure Kubernetes Service on Azure Stack HCI and Windows Server requirements, see [AKS requirements on Azure Stack HCI](../../aks-hci/overview.md#what-you-need-to-get-started).

## Gather information

To prepare for deployment, you'll need to take note of the server names, domain names*, and versions, and VLAN ID for your deployment. Gather the following details about your environment:

- **Server name:** Get familiar with your organization's naming policies for computers, files, paths, and other resources. If you need to provision several servers, each should have a unique name.
- **Domain name:** Get familiar with your organization's policies for domain naming and domain joining. You'll be joining the servers to your domain, and you'll need to specify the domain name.
- **Static IP addresses:** Azure Stack HCI requires static IP addresses for storage and workload (VM) traffic and doesn't support dynamic IP address assignment through DHCP for this high-speed network. You can use DHCP for the management network adapter unless you're using two in a team, in which case again you need to use static IPs. Consult your network administrator about the IP address you should use for each server in the cluster.
- **VLAN ID:** Note the VLAN ID to be used for the network adapters on the servers, if any. You should be able to obtain this from your network administrator.

> [!NOTE]
>  This deployment script can operate in 2 Modes, Domain Required or No Domain Required. This is because of the nature of Single Node Azure Stack HCI, the technology will allow for a No Domain option. This is in now way a SUPPORTED operation mode for Azure Stack HCI and is NOT recomended for Production workloads. This is simply to support a quick development environment for testing Azure Stack HCI and it's features including Azure Kubernetes Solution on Azure Stack HCI, Ifrastructure as a Service, Azure Virtual Desktop on Azure Stack HCI (Currently in Preview) and more. Please use this deployment method only for Development and Concept Testing only.  

## Prepare hardware for deployment

After you've acquired the server hardware for your Azure Stack HCI solution, it's time to rack and cable it. Use the following steps to prepare the server hardware for deployment of the operating system.

1. Rack all server nodes that you want to use in your server cluster.
1. Connect the server nodes to your network switches.
1. Configure the BIOS or the Unified Extensible Firmware Interface (UEFI) of your servers as recommended by your Azure Stack HCI hardware vendor to maximize performance and reliability.

> [!NOTE]
> If you are installing Azure Stack HCI on to a Virtual Machine, please mount the ISO image to the Virtual Machine, and proceed to Boot to VHD (or VMDX, etc) and continue the installation process. 

## Operating system deployment options

You can deploy the Azure Stack HCI operating system in the same ways that you're used to deploying other Microsoft operating systems:

- Server manufacturer pre-installation.
- Headless deployment using an answer file.
- System Center Virtual Machine Manager (VMM).
- Network deployment.
- Manual deployment by connecting either a keyboard and monitor directly to the server hardware in your datacenter, or by connecting a KVM hardware device to the server hardware.

### Headless deployment

You can use an answer file to do a headless deployment of the operating system. The answer file uses an XML format to define configuration settings and values during an unattended installation of the operating system.

For this deployment option, you can use Windows System Image Manager to create an unattend.xml answer file to deploy the operating system on your servers. Windows System Image Manager creates your unattend answer file through a graphical tool with component sections to define the "answers" to the configuration questions, and then ensure the correct format and syntax in the file.
The Windows System Image Manager tool is available in the Windows Assessment and Deployment Kit (Windows ADK). To get started: [Download and install the Windows ADK](/windows-hardware/get-started/adk-install).

### System Center Virtual Machine Manager (VMM) deployment

You can use [System Center 2022](/system-center/) to deploy the Azure Stack HCI, version 21H2 operating system on bare-metal hardware, as well as to cluster and manage the servers. For more information about using VMM to do a bare-metal deployment of the operating system, see [Provision a Hyper-V host or cluster from bare metal computers](/system-center/vmm/hyper-v-bare-metal).

> [!IMPORTANT]
> You can't use Microsoft System Center Virtual Machine Manager 2019 to deploy or manage clusters running Azure Stack HCI, version 21H2. If you're using VMM 2019 to manage your Azure Stack HCI, version 20H2 cluster, don't attempt to upgrade the cluster to version 21H2 without first installing [System Center 2022](/system-center/).

### Network deployment

Another option is to install the Azure Stack HCI operating system over the network using [Windows Deployment Services](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831764(v=ws.11)).

### Manual deployment

To manually deploy the Azure Stack HCI operating system on the system drive of each server to be clustered, install the operating system via your preferred method, such as booting from a DVD or USB drive. Complete the installation process using the Server Configuration tool (Sconfig) to prepare the server or servers for clustering. To learn more about the tool, see [Configure a Server Core installation with Sconfig](/windows-server/windows-server-2022/get-started/sconfig-on-ws2022).

To manually install the Azure Stack HCI operating system:

1. Start the Install Azure Stack HCI wizard on the system drive of the server where you want to install the operating system.
1. Choose the language to install or accept the default language settings, select **Next**, and then on next page of the wizard, select **Install now**.

![alt text](OneNode-NoDomain-Readme\Media\azure-stack-hci-install-language.png "The language page of the Install Azure Stack HCI wizard")

  

1. On the Applicable notices and license terms page, review the license terms, select the **I accept the license terms** checkbox, and then select **Next**.
1. On the Which type of installation do you want? page, select **Custom: Install the newer version of Azure Stack HCI only (advanced)**.

    > [!NOTE]
    > Upgrade installations are not supported in this release of the operating system.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-which-type.png" alt-text="The installation type option page of the Install Azure Stack HCI wizard.":::

1. On the Where do you want to install Azure Stack HCI? page, either confirm the drive location where you want to install the operating system or update it, and then select **Next**.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-where.png" alt-text="The drive location page of the Install Azure Stack HCI wizard.":::

1. The Installing Azure Stack HCI page displays to show status on the process.

    :::image type="content" source="../media/operating-system/azure-stack-hci-installing.png" alt-text="The status page of the Install Azure Stack HCI wizard.":::

    > [!NOTE]
    > The installation process restarts the operating system twice to complete the process, and displays notices on starting services before opening an Administrator command prompt.

1. At the Administrator command prompt, select **Ok** to change the user's password before signing in to the operating system, and press Enter.

    :::image type="content" source="../media/operating-system/azure-stack-hci-change-admin-password.png" alt-text="The change password prompt.":::

1. At the Enter new credential for Administrator prompt, enter a new password, enter it again to confirm it, and then press Enter.
1. At the Your password has been changed confirmation prompt, press Enter.

    :::image type="content" source="../media/operating-system/azure-stack-hci-admin-password-changed.png" alt-text="The changed password confirmation prompt":::

Now you're ready to use the Server Configuration tool (Sconfig) to perform important tasks. To use Sconfig, log on to the server running the Azure Stack HCI operating system. This could be locally via a keyboard and monitor, or using a remote management (headless or BMC) controller, or Remote Desktop. The Sconfig tool opens automatically when you log on to the server.

:::image type="content" source="../media/operating-system/azure-stack-hci-sconfig-screen.png" alt-text="The Server Configuration tool interface." lightbox="../media/operating-system/azure-stack-hci-sconfig-screen.png":::

From the Welcome to Azure Stack HCI window (Sconfig tool), you can perform the following initial configuration tasks:

- Configure networking or confirm that the network was configured automatically using Dynamic Host Configuration Protocol (DHCP).
- Rename the server if the default automatically generated server name does not suit you.
- Join the server to an Active Directory domain.
- Add your domain user account or designated domain group to local administrators.
- Enable access to Windows Remote Management (WinRM) if you plan to manage the server from outside the local subnet and decided not to join domain yet. (The default Firewall rules allow management both from local subnet and from any subnet within your Active Directory domain services.)

For more detail, see [Server Configuration Tool (SConfig)](/windows-server/administration/server-core/server-core-sconfig).

After configuring the operating system as ne


