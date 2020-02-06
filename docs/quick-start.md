# Quick Start Guide: Rubrik Integration for ServiceNow

## Overview

This document details the post-installation configuration steps for the Rubrik ServiceNow Integration. It includes steps for configuration and connectivity, as well as some troubleshooting steps. Application dependencies are detailed in the ‘External systems connection’ section of the document.

## Pre-requisites

Here's what is required to get started:

* Integration components required: Rubrik CDM cluster, running RCDM version 4.0+ (**NOTE:** some elements require newer versions)
* It is recommended to use a floating IP address for management of the cluster in order that management and monitoring can continue in the event that a node in the cluster is unavailable (instructions for configuring Floating IPs can be found in the Rubrik User Guide, See Rubrik User Guide, References entry 1)
* Steps to create a dedicated integration user:

1. See Rubrik User Guide (References entry 1) for instructions on creating a user account.
1. The user account should be of the **Administrator** role type on the Rubrik system.
1. The user account can be a **local** or **Active Directory** type account

## Configuration Instructions

| **NOTE:** The following tasks should be carried out as someone with ServiceNow admin permissions. |
| --- |

The following steps are used to configure the application once it is installed:

1. Go to **Service Catalog** in the ServiceNow console

![Service Catalog](/docs/img/image1.png)

2. Click the plus button (**+**) in the top right to add a new category to your ServiceNow Service Catalog

![Add Category](/docs/img/image2.png)

3. Enter **Rubrik** in the middle search box, select ‘Category Details’, and click ‘Add here’ on the left-hand middle box in the layout view

![Add Rubrik category](/docs/img/image3.png)

4. You should now see the **Rubrik** category in your Service Catalog, click the **Rubrik** title to drill into the catalog services

![Rubrik category](/docs/img/image4.png)

5. Click the **Add a Rubrik Cluster** Catalog Item

![Add Cluster](/docs/img/image5.png)

6. Complete the fields in the table with the Rubrik connection details, note that the **Rubrik Site/Cluster Name** field is used as the display name for the Rubrik cluster when running reports or other catalog items.

| NOTE: It is suggested to use a Floating IP for the **Rubrik Cluster Hostname or IP address** value, instructions on configuring a Floating IP can be found in the Rubrik CDM User Guide. Use of a Floating IP will help ensure that the application continues to function in the event of a node loss. |
| --- |

Select the MID Server which is most appropriate for connecting to the Rubrik cluster on-premises

![MID Server](/docs/img/image6.png)

7. Once done click **Order Now**

![Order Now](/docs/img/image7.png)

![Review order](/docs/img/image8.png)

8. Click on the **REQ** request ID, and confirm that the request is in **Closed Complete** state. If something went wrong with connecting to the Rubrik cluster, or using the provided credentials then this will be in a **Closed Incomplete** status

9. Repeat this step with further requests for any additional Rubrik clusters to be managed through ServiceNow
There are a set of  scheduled workflows configured as part of the Rubrik ServiceNow Management Pack, these are as follows:

| **Name** | **Default Frequency** | **Description** |
|---|---|---|
| Fetch Backup Histories | Every 4 hours | Gets backup history for all objects on the managed Rubrik clusters and stores them in the `Rubrik - Backup Histories` table |
| Import SLA Domains | Every 4 hours | Gets details of SLA Domains on the managed Rubrik clusters and stores them in the `Rubrik - SLA Domains` table |
| Import SQL Server Databases/Instances | Every 4 hours | Gets details for SQL Databases and Instances for Rubrik managed clusters and stores them in the `Rubrik – MSSQL Databases` and `Rubrik – MSSQL Instances` tables |
| Get Failed Backups | Every 4 hours | Gets details for failed backup jobs from Rubrik managed clusters and stores them in the `Rubrik – Failed Backup Tasks` table |
| Import VMware VMs | Every 4 hours | Gets details for VMware VMs for Rubrik managed clusters and stores them in the `Rubrik – VMware Virtual Machine` table |
| Import Hyper-V VMs | Every 4 hours | Gets details for Hyper-V VMs for Rubrik managed clusters and stores them in the `Rubrik – Hyper-V Virtual Machine` table |
| Import Filesets | Every 4 hours | Gets details for Filesets for Rubrik managed clusters and stores them in the `Rubrik – Filesets` table |
| Import AHV VMs | Every 4 hours | Gets details for Nutanix AHV VMs for Rubrik managed clusters and stores them in the `Rubrik – AHV Virtual Machine` table |
| Import EC2 Instances | Every 4 hours | Gets details for EC2 Instances for Rubrik managed clusters and stores them in the `Rubrik – EC2 Instances` table |
| Import Fileset Templates | Every 6 hours | Gets all Fileset Templates from managed clusters and stores them in the `Rubrik – Fileset Templates` table |
| Fetch Restore History | Every 24 hours | Gets details for recovery jobs from Rubrik managed clusters and stores them in the `Rubrik – Recovery Tasks` table |
| Import VMs | Every 4 hours | Gets details for Rubrik protected objects from managed clusters and stores them in the `Rubrik – VMs` table. These objects are VMs and Filesets |
| Import Capacity Stats | Every 15 minutes | Gets capacity statistics for the managed Rubrik clusters, and stores them in the `Rubrik – Capacity Stats` table |
| Import Stats | Every 15 minutes | Gets statistics for the managed Rubrik clusters, and stores them in the `Rubrik - Stats` table |

Snappable types not in use on the Rubrik managed clusters can have these Scheduled Workflows deactivated by an administrator to reduce load on the ServiceNow instance. For example, if no Hyper-V VMs are managed by Rubrik then disable the `Import Hyper-V VMs` Scheduled Workflow.

The default timings should be fine for most deployments, although these can be tuned through the **Scheduled Workflows** interface as shown below.

![Scheduled Workflows](/docs/img/image10.png)

![Repeat interval](/docs/img/image11.png)

On first installation, or on addition of a new Rubrik cluster, the 'Initial Configuration Workflow' should be run to gather data from the connected Rubrik clusters. In the case that this is after the addition of a new cluster, the 'Clear existing tables' checkbox should be unchecked.

The following roles provide access to the Rubrik application:

### General purpose roles

| **Role Name** | **Description** |
|---|---|
| app_user | Required for access to catalog items, modules, and read-only access to tables |
| app_admin | Provides modify access to all tables and portal pages |
| dashboard_admin | Provides modify access to portal pages included with the application |
| dashboard_user | Provides read-only access to portal pages included with the application |

**NOTE:** all roles are prefixed with `x_rubri_rubriksnow`.

### More specific roles

The below roles are not so broad, but rather can be used by customers to provide specific access to tables and catalog items within the Rubrik application.

| **Role Name** | **Description** |
|---|---|
| rubrik_ahv_virtual_machine_admin | Allows modification of records in AHV VM table |
| rubrik_backup_history_admin | Allows modification of records in Backup History table |
| rubrik_capacity_stats_admin | Allows modification of records in Capacity Stats table |
| rubrik_configuration_sites_admin | Allows modification of records in Configuration table |
| rubrik_ec2_instances_admin | Allows modification of records in EC2 Instances table |
| rubrik_failed_backup_tasks_admin | Allows modification of records in Failed Backup Tasks table |
| rubrik_filesets_admin | Allows modification of records in Filesets table |
| rubrik_fileset_templates_admin | Allows modification of records in Fileset Templates table |
| rubrik_hosts_admin | Allows modification of records in Hosts table |
| rubrik_hyper_v_virtual_machine_admin | Allows modification of records in Hyper-V VM table |
| rubrik_mssql_database_admin | Allows modification of records in MSSQL DB table |
| rubrik_mssql_instance_admin | Allows modification of records in MSSQL instance table |
| rubrik_recovery_tasks_admin | Allows modification of records in Recovery Tasks table |
| rubrik_sla_domains_admin | Allows modification of records in SLA Domains table |
| rubrik_stats_admin | Allows modification of records in Stats table |
| rubrik_timeseries_admin | Allows modification of records in Timeseries table |
| rubrik_vmware_virtual_machine_admin | Allows modification of records in VMware VM table |
| rubrik_vm_admin | Allows modification of records in legacy VMs table |
| rubrik_volume_group_admin | Allows modification of records in Volume Group table |
| rubrik_sc_category_enable | Enables the category view for the Rubrik Service Catalog category |
| rubrik_ci_add_host_enable | Enables the Add a Linux or Windows host Catalog Item |
| rubrik_ci_add_cluster_enable | Enables the Add a Rubrik Cluster Catalog Item |
| rubrik_ci_assign_sla_enable | Enables the Assign SLA Catalog Item |
| rubrik_ci_create_vm_enable | Enables the Create VM and Assign SLA Catalog Item |
| rubrik_ci_delete_cluster_enable | Enables the Delete a Rubrik Cluster Catalog Item |
| rubrik_ci_export_ec2_snapshot_enable | Enables the Export an EC2 Snapshot Catalog Item |
| rubrik_ci_initial_config_enable | Enables the Initial Configuration Workflow Catalog Item |
| rubrik_ci_live_mount_sqldb_enable | Enables the Live Mount SQL Server Database Catalog Item |
| rubrik_ci_reconfig_cluster_enable | Enables the Reconfigure an Existing Rubrik Cluster Catalog Item |
| rubrik_ci_restore_file_enable | Enables the Recover a File/Folder Catalog Item |
| rubrik_ci_live_mount_vm_enable | Enables the Request a Live Mount Catalog Item |
| rubrik_ci_restore_ec2_instance_enable | Enables the Restore an EC2 Instance Catalog Item |
| rubrik_ci_take_ec2_snapshot_enable | Enables the Take an EC2 Instance Snapshot Catalog Item |
| rubrik_ci_take_snapshot_enable | Enables the Take an On-Demand Snapshot Catalog Item |

**NOTE:** all roles are prefixed with `x_rubri_rubriksnow`.

Note that adding a Rubrik Cluster relies on having access to the MID servers table, this typically requires the ‘admin’ role in ServiceNow. This can be removed once all clusters have been added.

## Testing the Configuration

Connectivity to the on-premises Rubrik cluster(s) will be via one or more ServiceNow MID Servers. These MID servers will require HTTPS (TCP/443) access to the Rubrik cluster in order to communicate with the Rubrik CDM REST API.

Once the application is installed, testing for connectivity to added Rubrik clusters will be done as they are added via the included **Add a Rubrik Cluster** Catalog Item.

## System Properties

When collecting backup history, the number of rows in the `Rubrik - Backup Histories` table in large environments can become prohibitively large. To help prevent this, the `x_rubri_rubriksnow.retain_backup_history_days` System Property has been added to allow the retention of backup history records to be tuned by the customer. This defaults to 30 days, but the value of this property can be amended as required to increase/reduce the number of records kept.

## Troubleshooting

Issues with the Rubrik application are likely a result of communication issues between the MID Server and the Rubrik CDM cluster. These can be investigated through network troubleshooting from the MID Server:

* Windows – can you access the Rubrik CDM Web UI via Chrome browser on the MID Server?
* Linux – can you connect to port 443? (`nc <rubrik_cluster_ip> 443`):

```none
HAL:~ user$ nc rubrik.demo.com 443 -v
found 0 associations
found 1 connections:
     1: flags=82<CONNECTED,PREFERRED>
    outif utun4
    src 192.168.1.101 port 55434
    dst 192.168.0.100 port 443
    rank info not available
    TCP aux info available
Connection to rubrik.demo.com port 443 [tcp/https] succeeded!
```

If tables are not populating via the Scheduled Workflows then the System Log in ServiceNow can be used to check for error messages (**System Logs** > **System Log** > **All**).
