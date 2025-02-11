---

copyright:
  years: 2022, 2023

lastupdated: "2023-09-11"

keywords: Shared processor pool, SPP, pool placement group, create SPP, SPP PG, 

subcollection: power-iaas

---

{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:preview: .preview}
{:important: .important}
{:deprecated: .deprecated}
{:external: target="_blank" .external}

# Managing shared processor pool
{: #manage-SPP}

A shared processor Pool (SPP) is a pool of processor capacity that is shared between a group of virtual server instances. Unlike a virtual server instance that has a dedicated and defined maximum amount of processing capacity, you can set the reserved cores in SPP that are guaranteed to be available at the pool level.
{: shortdesc}

The following table shows how an SPP is used to reduce the licensing cost when you pay per core:
|Use of SPP|VM 1|VM 2|Reserved cores in Pool (User defined)|License requirement per core|
|-----|-----------|-----------|-----------|-----------|
|No|Maximum cores = 5 \n Mode = Dedicated|Maximum cores = 6 \n Mode = Dedicated|NA|5+6 = 11|
|Yes|Maximum cores = 5 \n Mode = Shared/Uncapped \n Entitled capacity = 4.25|Maximum cores = 6 \n Mode = Shared/Uncapped \n Entitled capacity = 5.25|Maximum cores = 10|10, Determined by reserved cores in pool|
{: class="simple-table"}
{: caption="Table 1. SPP helps to reduce the licensing cost" caption-side="bottom"}

The benefits of using an SPP are as follows:

* Provides control over licensing costs by limiting the number of processors an uncapped partition can use, which reduces the number of software licenses.
* Provides a better overall ability to manage processor resources.

The {{site.data.keyword.powerSys_notm}} always has at least one defined SPP as the default pool. You can add up to 63 more SPPs to a single {{site.data.keyword.powerSys_notm}} workspace. The SPP is used and shared by a set of virtual server instances of the same machine type (host).

You can specify the host affinity and anti-affinity between two or more SPPs with shared processor pool placement groups. For more information, see [Configuring shared processor pool placement group](/docs/power-iaas?topic=power-iaas-manage-SPP#configure-SPP-PG).

## Pricing for shared processor pool
{: #price-spp}

When you use SPP, you pay for the following items:

* SPP reserved cores that use the shared capped part number.
* Virtual server instance cores that are deployed into the SPP that use shared uncapped part number.

The SPP helps you to manage CPU cores only. Pricing for memory and storage remains the same as earlier. The total estimated cost page does not show the SPP reserved cores-related costs because of service-level estimator limitations. For more information about pricing, see [Pricing for Power Systems Virtual Servers](/docs/power-iaas?topic=power-iaas-pricing-virtual-server).
{: note}

## Configuring shared processor pool
{: #configure-spp}

SPP is a separate resource within a {{site.data.keyword.powerSys_notm}} workspace. You can manage the entire SPP lifecycle with the {{site.data.keyword.powerSys_notm}} user interface.

The SPP and your {{site.data.keyword.powerSys_notm}} workspace are linked and thus shared resource pools are not visible from different accounts. You can create one or more SPPs per workspace.

Create an SPP by specifying the following parameters:

* A unique name
* Host group or the machine type
* Number of processors to reserve.

When you define these parameters, a backend process determines the best host for the new SPP.

When the SPP you create is not configured successfully on the host, the SPP will not have any allocated processing cores. You need to delete such SPPs manually as they do not get cleaned up automatically.
{:note}

### Creating a shared processor pool
{: #create-spp}

To create an SPP, complete the following steps:

1. Go to **Shared processor pools** in the {{site.data.keyword.powerSys_notm}} user interface under **Compute**.
2. Click **Create pool**.
3. In the **Create new shared processor pool** window, define the following preferences based on your requirements:
  |Field|Description|
  |----|----|
  |Name|Enter a name that is unique within your cloud account.\n Use a name of minimum 2 characters and a maximum of 12 characters. Alphanumeric characters are not allowed and underscore (‘_’) is only allowed as a special character.|
  |Add to a pool placement group|Select the checkbox if you want to deploy the SPP directly into an existing pool placement group. \n If the pool has the required affinity relation with other pools, the best practice is to deploy the pool directly into the placement group. You must create the pool placement group first. It prevents the pool from being deployed on a host that does not satisfy the affinity requirements, and having to move it later.|
  |Select machine type|Specify the machine type. For more information about hardware specifications, see [S922](https://www.ibm.com/downloads/cas/KQ4BOJ3N){: external}, and [E980 (Data centers other than Dallas and Washington)](http://www-01.ibm.com/support/docview.wss?uid=ssm1platformaix9080-M9S-vios-only){: external}.|
  |Reserved processing cores|The core-to-vCPU ratio is 1:1 by default. |
  {: caption="Table 2. Creating a new SPP fields and descriptions" caption-side="bottom"}

1. Click **Create**.

### Updating or deleting a shared processor pool
{: #update-delete-spp}

To update or delete an SPP, navigate to **Compute** > **Shared processor pools**. Then select the SPP that you want to edit and click on the edit icon. You will get a notification after the SPP is deleted successfully.

You can update or delete the following details of an existing SPP:

* Name of the SPP - Follow the same naming conventions that you used while creating an SPP. For more information, see [Creating a shared processor pool](/docs/power-iaas?topic=power-iaas-managing-shared-processor-pool#create-spp).
* Number of cores - You can update the number of reserved cores based on resource availability and allocation.
* Delete an existing SPP - You can delete any existing SPP. Before deleting, ensure that no virtual server instances exist in the SPP. If virtual server instances are present, it must be deleted or moved with a support ticket.

## Managing a virtual server instance of a shared processor pool
{: #manage-vm-inside-spp}

When you deploy a virtual server instance, you can choose an SPP instead of a host. As you cannot move a virtual server instance into or out of an SPP, you can deploy a virtual server instance directly into an SPP as a best practice. You can also deploy a virtual server instance into a server placement group; the selected SPP host must be compatible with the affinity policy of the placement group that you selected.

When you deploy multiple virtual server instances simultaneously, a new server placement group is created automatically.
{:note}

During any planned maintenance activity or when you want to perform a remote restart operation, ensure that the virtual server instances are linked to an SPP based on your requirements.

### Deploying a virtual server instance into a shared processor pool
{: #deploy-pvm-in-spp}

To add virtual server instances to an exsiting SPP, complete the following steps:

1. Go to **Virtual server instances** in the {{site.data.keyword.powerSys_notm}} user interface under **Compute**.
2. Click **Create instance**.
3. Complete the input fields under the **General** tile based on your requirement.
4. Select the checkbox **Add to a Shared processor pool**.
5. Select an existing shared processor pool.
6. Continue with the with the process of creating a virtual server instance. For more information, see [Configuring a Power Systems Virtual Server instance](/docs/power-iaas?topic=power-iaas-creating-power-virtual-server#configuring-instance).

## Configuring a shared processor pool placement group
{: #configure-SPP-PG}

You can configure an SPP with the shared processor pool placement group (SPP PG) to control the host in which SPPs are deployed.

SPP PGs are different from server placement groups. They serve the same purpose but cannot combine resource types.
{:Note}

You must define the following parameters to define an SPP PG:

* A unique name - The SPP PG name must be unique in your cloud instance.
* Policy - You can set the colocation policy of the SPP PG as the same server (affinity) or a different server (anti-affinity).

The following table explains how the host selection is determined based on the different options of SPP PG policy selection:

|SPP PG Policy|Host selection|Result|
|-------------|--------------|------|
|Same server (Affinity)|New SPP is configured on same host as determined by the existing PG SPP members.|The new SPP is automatically added as a member of the PG policy.|
|Different server (Anti-affinity) |Configure the new SPP on a different host than all host identified by all the existing PG members.|The new SPP is automatically added as a member of the PG policy.|
{: class="simple-tab-table"}
{: tab-group="host_selection"}
{: caption="Table 3. Host selection when SPP PG is empty and non-empty" caption-side="top"}
{: #host_selection-1}
{: tab-title="When SPP PG is non-empty"}

|SPP PG Policy|Host selection|Result|
|-------------|--------------|------|
|Same server (Affinity)|New SPP is configured on a host that is selected based on the backend processing|The new SPP is automatically added as a member of the PG policy|
|Different server (Anti-affinity) |New SPP is configured on a host that is selected based on the backend processing|The new SPP is automatically added as a member of the PG policy |
{: class="simple-tab-table"}
{: tab-group="host_selection"}
{: caption="Table 3. Host selection when SPP PG is empty and non-empty" caption-side="top"}
{: #host_selection-2}
{: tab-title="When SPP PG is empty"}

### Creating a shared processor pool placement group
{: #create-new-pg}

To create an SPP PG, complete the following steps:

1. Go to **Shared processor pools** in the {{site.data.keyword.powerSys_notm}} user interface under **Compute**.
2. Click the **Pool placement groups** tab.
3. Click **Create group**.
4. In the **Create new pool placement group** window, enter the following details:
  |Field|Description                                             |
  |-----|--------------------------------------------------------|
  |Name |Enter a name that is unique within your cloud account. \n Use a minimum of 2 characters and a maximum of 12 characters. Alphanumeric characters are not allowed and underscore (‘_’) is only allowed as a special character.|
  |Policy|Same Server (Affinity) \n Different server (Anti-affinity)|
  {: caption="Table 4. Create a new SPP PG field description" caption-side="top"}

5. Click **Create**.

You get notified when the new SPP PG is created.

### Adding a shared processor pool to a the placement group
{: #add-spp-inside-spp-pg}

You can add an SPP as a member, to an existing SPP PG if the host of the SPP follows the affinity policy of the group. If the PG policy is set to **Same server** (affinity), all SPPs that are added as a member of the placement group must remain on the same host. If the PG policy is set to **Different server** (anti-affinity), the SPP is deployed on a different host other than all its group members.

To add an SPP to an existing SPP PG, complete the following steps:

1. Go to **Shared processor pools** in the {{site.data.keyword.powerSys_notm}} user interface under **Compute**.
2. Click the **Pool placement groups** tab.
3. Select an existing pool placement group to which you want to add an   SPP.
4. Select one of the following options:

|    Field         |                 Description                           |
|------------------|-------------------------------------------------------|
|Add existing      |You can choose from existing SPPs.     |
|Create new        |You can create an SPP from scratch by defining pool name, machine type, and number of cores. For more information, see [Create a new SPP](/docs/power-iaas?topic=power-iaas-managing-shared-processor-pool#create-a-new-spp)|
{: caption="Table 5. Add SPP to an SPP PG: field description" caption-side="top"}

You will be notified after the new SPP PG is created.

### Deleting a shared processor pool placement group
{: #delete-spp-pg}

To delete an SPP PG, complete the following steps:

1. Go to **Shared processor pools** in the {{site.data.keyword.powerSys_notm}} user interface under **Compute**.
2. Click the **Pool placement groups** tab.
3. Click the SPP PG that you want to delete.
4. In the details page, click the delete icon.

Deleting a placement group does not delete its members or change the host in which the placement groups are deployed.
{:Note}
