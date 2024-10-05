
VMware Cloud Foundation 解决方案中有一个叫 Supportability and Serviceability（[VMware Cloud Foundation Part 03：准备 Excel 参数表。](https://github.com)”。同样，这个 SoS 程序也可以在 SDDC Manager 虚拟机中使用，并且具有更多实用的功能，比如在 VCF 环境中运行状态检查以及收集相关组件的日志等，下面一起来看看如何使用它。


 



## 一、SoS 实用程序使用选项



通过 SSH 连接到 SDDC Manager 虚拟机，SoS 实用程序可以在如下图中的位置（/opt/vmware/sddc\-support）找到它，你可以使用 `--version` 或 `-v` 命令选项查看当前 SoS 实用程序的版本。注意，使用 SoS 程序在执行某些功能选项的时候，可能需要 root 用户才能运行。



```
sudo ./sos --version
```

[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241004124115345-243615381.png)](https://github.com)


使用 `--help` 或 `-h` 命令查看 [SoS 程序使用选项](https://github.com)帮助。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --help
[sudo] password for vcf
usage: 
        ./sos [-h] [options]
        #######################################################
        #      Supportability and Serviceability Utility      #
        #######################################################
        # specify options in the conventional GNU/POSIX syntax,
         " --" for long option and "-" for short option
        

options:
  -h, --help            show this help message and exit
  -v, --version         Displays SoS version
  --history             Displays last 20 SoS log locations
  --force               Allows performing SoS operations on SDDC Manager while workflows are running (not recommended)
  --configure-sftp      Configure SFTP for logs
  --setup-json SETUPJSON
                        Custom setup-json file for log collection, Please check setup.sample.json for reference
  --log-folder LOGFOLDER
                        Name of the directory
  --log-dir LOGDIR      Dump logs to given directory
  --enable-stats        Enable SoS execution stats collection
  --debug-mode          Debug mode
  --zip                 Create a zipped tar file
  --short               Display health results only for failures/warning
  --domain-name DOMAINNAME
                        Domain name on which SoS operation needs to be executed, use '--domain-name ALL' for all the
                        domains (Default: MANAGEMENT). (Add --skip-cert-check options to include health-check for
                        free/unassigned hosts)
  --clusternames CLUSTERNAMES
                        Cluster names associated with a domain which WCP/ESX logs needs to be collectedPass cluster names
                        with comma seperated like '--clusternames cluster1,cluster2 ' for all the specific domains. If
                        domain-name is ALL will ignore this option
  --skip-known-host-check
                        skip check for ssl thumbprint for host in known host
  --include-precheck-report
                        Includes pre-check report
  --include-free-hosts  Collect ESX free hosts log bundle

VCF Summary Options:
  --get-vcf-summary     Display VCF Summary
  --get-vcf-tasks-summary
                        Display VCF Tasks Summary
  --get-vcf-services-summary
                        Display VCF Services Details

Log Collections Options:
  --esx-logs            Collect ESXi logs
  --vc-logs             Collect vCenter Server logs
  --sddc-manager-logs   Collect SDDC-Manager logs
  --vxrail-manager-logs
                        Collect VxRail-Manager logs(Applicable for VxRail)
  --psc-logs            Collect PSC logs
  --nsx-logs            Collect NSX logs
  --wcp-logs            Collect Log WCP cluster logs
  --vrealize-logs       [DEPRECATED] Collect VMware Aria (formerly vRealize) components logs
  --automation-logs     Collect VMware Aria Automation support logs
  --operations-for-logs
                        Collect VMware Aria Operations for Logs support logs
  --operations-logs     Collect VMware Aria Automation Operations support logs
  --lifecycle-logs      Collect VMware Aria Suite Lifecycle support logs
  --no-clean-old-logs   Do not clean old log folder
  --test                Sanity test logs collected by verifying the files
  --no-health-check     Skip Health Check executed as part of SoS log collection
  --api-logs            Collect output from APIs
  --vm-screenshots      Collect all VM screenshots
  --system-debug-logs   Collect System logs to help debug in abnormal scenarios like memory leak
  --collect-all-logs    Collect logs for all components, excluding WCP and system-debug-logs pass --domain-name
                        DOMAINNAME, For all domains pass --domain-name ALL (Default: MANAGEMENT)

Fix-It-Up Options:
  --enable-ssh-esxi     Enable ssh on all esxi nodes in a domain, pass --domain-name DOMAINNAME, For all domains pass
                        --domain-name ALL (Default: MANAGEMENT)
  --disable-ssh-esxi    Disable ssh on all esxi nodes in a domain, pass --domain-name DOMAINNAME, For all domains pass
                        --domain-name ALL , (Default: MANAGEMENT)
  --enable-ssh-vc       Enable ssh on Vcenter in a domain, pass --domain-name DOMAINNAME, For all domains pass --domain-
                        name ALL (Default: MANAGEMENT)
  --disable-ssh-vc      Enable ssh on Vcenter in a domain, pass --domain-name DOMAINNAME, For all domains pass --domain-
                        name ALL (Default: MANAGEMENT)
  --enable-lockdown-esxi
                        Enables Normal lockdown mode on all esxi nodes in a domain (Default Domain: MANAGEMENT domain), to
                        enable pass --domain-name  (or '--domain-name ALL' to include all the domains). Users
                        can also perform this operation at cluster level per domain, pass --domain-name DOMAINNAME with
                        --clusternames cluster1, cluster2
  --disable-lockdown-esxi
                        Disables Normal lockdown mode on all esxi nodes in a domain (Default Domain: MANAGEMENT domain),
                        to disable pass --domain-name  (or '--domain-name ALL' to include all the domains).
                        Users can also perform this operation at cluster level per domain, pass --domain-name DOMAINNAME
                        with --clusternames cluster1, cluster2
  --ondemand-service ONDEMANDSERVICE
                        Execute commands on ESXIs/VCENTERs/SDDC-Manager entities for a given domain, use --domain-name
                        option(Default: MANAGEMENT). It requires .yml file as input (Refer sample json:
                        /opt/vmware/sddc-support/ondemand_command_sample.yml) (WARNING: Use this option cautiously as it
                        can alter the state of SDDC and its components.)
  --refresh-ssh-keys    Refresh SSH keys

Health Check:
  --health-check        Perform all available Health Checks
  --connectivity-health
                        Perform Connectivity Health Check
  --services-health     Perform Services Health Check
  --compute-health      Perform Compute Health Check
  --storage-health      Perform Storage Health Check
  --run-vsan-checks     Perform Storage Proactive Checks
  --ntp-health          Perform NTP Health Check
  --dns-health          Perform Forward and Reverse DNS Health Check
  --general-health      Perform General Health Check
  --certificate-health  Perform Certificate Health Check
  --composability-infra-health
                        Performs Composability infra Api connectivity check(if Composability infra found else skipped)
  --get-host-ips        Get Server Information
  --get-inventory-info  Get Inventory details for SDDC
  --password-health     Check password expiry status
  --hardware-compatibility-report
                        Validates hosts and vsan devices and export the compatibility report
  --version-health      This is an informational check. It shows version info of VCF BOM components in SDDC.
  --json-output-dir JSONDIR
                        Outputs health check results JSON file to given directory
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ 
```

可以在 `sudo ./sos --` 后面使用 TAB 补全命令选项。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241004151117845-71209416.png)](https://github.com):[milou加速器](https://xinminxuehui.org)


 



## 二、查看 VCF 环境摘要信息



使用 `--get-vcf-summary` 选项可查看 VCF 环境中的“配置”拽要信息，包括 CEIP、工作负载域、vSphere 集群、ESXi 主机、许可、网络池、SDDC Manager 和 VCF 服务等。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --get-vcf-summary
[sudo] password for vcf
Welcome to Supportability and Serviceability(SoS) utility!
Summary : /var/log/vmware/vcf/sddc-support/summary-2024-10-04-04-57-36-8455
Summary log : /var/log/vmware/vcf/sddc-support/summary-2024-10-04-04-57-36-8455/sos.log
SDDC Manager%, Initiated SoS operation
╠══ SDDC Manager Status: ACTIVE
╠══ SDDC Version: 5.1.0.0-22688368
╠══ SDDC Manager Id: d1b827d1-f6fd-49d5-9c5c-7b21ffaa5222
╠══ Total No of Domains: 1
╠══ Total No of Clusters: 1
╠══ CEIP
║   ╠══ Status: UNKNOWN
║   ╚══ Id: d1b827d1-f6fd-49d5-9c5c-7b21ffaa5222
╠══ AVN
║   ╚══ Status: DISABLED
╠══ Domains
║   ╚══ vcf-mgmt01
║       ╠══ Name: vcf-mgmt01
║       ╠══ Domain Type: MANAGEMENT
║       ╠══ Status: ACTIVE
║       ╠══ Domain Id: 09230fea-bcbe-42e4-8eb6-414dcf728d5b
║       ╠══ No of Clusters: 1
║       ╠══ Clusters
║       ║   ╚══ vcf-mgmt01-cluster01
║       ║       ╠══ Datacenter: vcf-mgmt01-datacenter01
║       ║       ╠══ Datastore: VSAN
║       ║       ╠══ Primary Datastore Type: VSAN_ESA
║       ║       ╠══ Cluster Id: 4a4200e9-05bf-4e34-bfe5-8984284d85bb
║       ║       ╠══ Status: ACTIVE
║       ║       ╠══ FTT: 1
║       ║       ╠══ Stretched Cluster: False
║       ║       ╠══ # of Hosts: 4
║       ║       ╠══ Resource Utilization
║       ║       ║   ╠══ CPU usage in GHz: 12 %
║       ║       ║   ╠══ Memory usage in GB: 55 %
║       ║       ║   ╚══ Storage usage in TB: 14 %
║       ║       ╠══ WCP Status: DISABLED
║       ║       ╚══ vLCM Status: ENABLED
║       ╠══ vCENTER Version: 8.0.2.00100-22617221
║       ╠══ vCENTER Id: 86109735-258c-437e-b004-7193d41cbc97
║       ╠══ Esxi Version: 8.0.2-22380479
║       ╠══ NSX-Type: NSX
║       ╚══ NSX Version: 4.1.2.1.0-22667789
╠══ Host Details
║   ╠══ Free Hosts: 0
║   ╠══ Active Hosts: 4
║   ╚══ Dirty Hosts: 0
╠══ Licensing Details
║   ╠══ VMware vCenter Server License
║   ║   ╠══ Product Type: VCENTER
║   ║   ╠══ Total: Unlimited
║   ║   ╠══ Used: 1
║   ║   ╠══ license Unit: Server
║   ║   ╚══ Key: XXXXX-XXXXX-XXXXX-XXXXX-XXXXX
║   ╠══ VMware vSAN License
║   ║   ╠══ Product Type: VSAN
║   ║   ╠══ Total: Unlimited
║   ║   ╠══ Used: 8
║   ║   ╠══ license Unit: CPU Packages
║   ║   ╚══ Key: XXXXX-XXXXX-XXXXX-XXXXX-XXXXX
║   ╠══ VMware NSX Data Center License
║   ║   ╠══ Product Type: NSXT
║   ║   ╠══ Total: 65535
║   ║   ╠══ Used: None
║   ║   ╠══ license Unit: Virtual machine
║   ║   ╚══ Key: XXXXX-XXXXX-XXXXX-XXXXX-XXXXX
║   ╚══ VMware vSphere License
║       ╠══ Product Type: ESXI
║       ╠══ Total: Unlimited
║       ╠══ Used: 8
║       ╠══ license Unit: CPU Packages
║       ╚══ Key: XXXXX-XXXXX-XXXXX-XXXXX-XXXXX
╠══ Network Pool Details
║   ╠══ vcf-mgmt01-np01
║   ║   ╠══ Network Pool ID: b018fb05-98df-42a6-a0af-57894bcda6af
║   ║   ╠══ Hosts: 4
║   ║   ╚══ Network Type: VMOTION, VSAN
║   ╚══ vcf-vi01-np01
║       ╠══ Network Pool ID: a161d1ed-6d88-4697-bb26-f7c1d66255a9
║       ╠══ Hosts: 0
║       ╚══ Network Type: VMOTION, VSAN
╚══ VCF Services
    ╠══ COMMON_SERVICES
    ║   ╠══ Version: 5.1.0-vcf5100RELEASE-22688321
    ║   ╚══ Memory Utilized: 580.0K
    ╠══ LCM
    ║   ╠══ Version: 5.1.0-vcf5100RELEASE-22688321
    ║   ╚══ Memory Utilized: 1.3M
    ╠══ SDDC_MANAGER_UI
    ║   ╠══ Version: 5.1.0-vcf5100RELEASE-22688320
    ║   ╚══ Memory Utilized: 141.9M
    ╠══ OPERATIONS_MANAGER
    ║   ╠══ Version: 5.1.0-vcf5100RELEASE-22688321
    ║   ╚══ Memory Utilized: 556.0K
    ╚══ DOMAIN_MANAGER
        ╠══ Version: 5.1.0-vcf5100RELEASE-22688321
        ╚══ Memory Utilized: 1.2M

Summary Collection completed successfully for : [VCF-SUMMARY]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--get-vcf-tasks-summary` 选项可查看 VCF 环境中的“任务”摘要信息，包括任务的创建时间和任务的状态。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --get-vcf-tasks-summary
[sudo] password for vcf
Welcome to Supportability and Serviceability(SoS) utility!
Summary : /var/log/vmware/vcf/sddc-support/summary-2024-10-04-05-02-43-9874
Summary log : /var/log/vmware/vcf/sddc-support/summary-2024-10-04-05-02-43-9874/sos.log
WorkFlow Tasks Initiated SoS operation
╠══ a20c81d6-68a7-446c-9a1f-1459cf7c7065
║   ╠══ Status: SUCCESSFUL
║   ╠══ Creation Time: Thu, 25 Jul 2024 09:02:42 UTC
║   ╠══ Id: a20c81d6-68a7-446c-9a1f-1459cf7c7065
║   ╚══ name: vSphere Lifecycle Manager Image Upload Management-Domain-Personality
╠══ a4aaaffd-29d3-4282-8390-d33bf241cb0a
║   ╠══ Status: Successful
║   ╠══ Creation Time: Tue, 30 Jul 2024 14:04:25 UTC
║   ╠══ Id: a4aaaffd-29d3-4282-8390-d33bf241cb0a
║   ╚══ name: Renaming cluster cluster01 to vcf-vi01-cluster01
╠══ f2a4685f-e89b-405a-97a2-662d4ef3accd
║   ╠══ Status: Failed
║   ╠══ Creation Time: Thu, 01 Aug 2024 07:01:23 UTC
║   ╠══ Id: f2a4685f-e89b-405a-97a2-662d4ef3accd
║   ╠══ Failed Task: Acquire Lock
║   ╚══ name: Removing domain vcf-vi01
╠══ c8ab51a0-730b-40e6-938c-59f85aee3689
║   ╠══ Status: Successful
║   ╠══ Creation Time: Thu, 01 Aug 2024 10:47:46 UTC
║   ╠══ Id: c8ab51a0-730b-40e6-938c-59f85aee3689
║   ╚══ name: Commissioning host(s) vcf-vi01-esxi02.mulab.local,vcf-vi01-esxi03.mulab.local,vcf-vi01-esxi01.mulab.local to VMware Cloud Foundation
╠══ 67d8caf5-e3b9-436e-adbb-d9e9cb868074
║   ╠══ Status: Successful
║   ╠══ Creation Time: Thu, 01 Aug 2024 11:16:48 UTC
║   ╠══ Id: 67d8caf5-e3b9-436e-adbb-d9e9cb868074
║   ╚══ name: Creating Workload Domain: vcf-vi01
╠══ d480da6b-ac70-4289-9425-dc2df6a0cc15
║   ╠══ Status: Successful
║   ╠══ Creation Time: Tue, 30 Jul 2024 09:25:39 UTC
║   ╠══ Id: d480da6b-ac70-4289-9425-dc2df6a0cc15
║   ╚══ name: Commissioning host(s) vcf-vi01-esxi02.mulab.local,vcf-vi01-esxi03.mulab.local,vcf-vi01-esxi01.mulab.local to VMware Cloud Foundation
╠══ 1553cab1-a4e2-4e2e-b411-79d97800745c
║   ╠══ Status: Successful
║   ╠══ Creation Time: Tue, 30 Jul 2024 10:25:00 UTC
║   ╠══ Id: 1553cab1-a4e2-4e2e-b411-79d97800745c
║   ╚══ name: Creating Workload Domain: vcf-vi01
╠══ 33c51b38-f8e1-40cf-94e9-e0a591cef0af
║   ╠══ Status: Successful
║   ╠══ Creation Time: Thu, 01 Aug 2024 07:01:51 UTC
║   ╠══ Id: 33c51b38-f8e1-40cf-94e9-e0a591cef0af
║   ╚══ name: Removing domain vcf-vi01
╠══ 52c7ac89-a0ee-4022-ade7-2548903d6426
║   ╠══ Status: Successful
║   ╠══ Creation Time: Thu, 01 Aug 2024 07:28:50 UTC
║   ╠══ Id: 52c7ac89-a0ee-4022-ade7-2548903d6426
║   ╚══ name: Decommissioning host(s) vcf-vi01-esxi02.mulab.local,vcf-vi01-esxi03.mulab.local,vcf-vi01-esxi01.mulab.local from VMware Cloud Foundation
╠══ 34373385-451b-4dd6-b9a7-9cae68f3e3cc
║   ╠══ Status: Successful
║   ╠══ Creation Time: Thu, 01 Aug 2024 08:42:28 UTC
║   ╠══ Id: 34373385-451b-4dd6-b9a7-9cae68f3e3cc
║   ╚══ name: Commissioning host(s) vcf-vi01-esxi02.mulab.local,vcf-vi01-esxi03.mulab.local,vcf-vi01-esxi01.mulab.local to VMware Cloud Foundation
╠══ e8ba4f1d-f20f-4f26-9a15-300e423916fb
║   ╠══ Status: Successful
║   ╠══ Creation Time: Thu, 01 Aug 2024 10:29:45 UTC
║   ╠══ Id: e8ba4f1d-f20f-4f26-9a15-300e423916fb
║   ╚══ name: Decommissioning host(s) vcf-vi01-esxi02.mulab.local,vcf-vi01-esxi03.mulab.local,vcf-vi01-esxi01.mulab.local from VMware Cloud Foundation
╠══ dacc2534-53cf-4476-9708-cdc2cf34b1fe
║   ╠══ Status: Successful
║   ╠══ Creation Time: Sat, 17 Aug 2024 07:41:34 UTC
║   ╠══ Id: dacc2534-53cf-4476-9708-cdc2cf34b1fe
║   ╚══ name: Commissioning host(s) vcf-mgmt01-esxi05.mulab.local to VMware Cloud Foundation
╠══ 6b4a9fbf-8171-4745-bea2-f88bd5252564
║   ╠══ Status: Failed
║   ╠══ Creation Time: Sat, 17 Aug 2024 07:53:59 UTC
║   ╠══ Id: 6b4a9fbf-8171-4745-bea2-f88bd5252564
║   ╠══ Failed Task: Prepare ESXi Host
║   ╚══ name: Adding new host(s) to cluster
╠══ f4020d23-961f-425e-8525-be6d3e89eef1
║   ╠══ Status: Successful
║   ╠══ Creation Time: Sat, 17 Aug 2024 08:23:58 UTC
║   ╠══ Id: f4020d23-961f-425e-8525-be6d3e89eef1
║   ╚══ name: Removing host(s) from cluster
╠══ 48f4c75e-d7c6-4188-98b0-5ceb95b4c9c2
║   ╠══ Status: Successful
║   ╠══ Creation Time: Sat, 17 Aug 2024 08:26:41 UTC
║   ╠══ Id: 48f4c75e-d7c6-4188-98b0-5ceb95b4c9c2
║   ╚══ name: Decommissioning host(s) vcf-mgmt01-esxi05.mulab.local from VMware Cloud Foundation
╠══ 638363b1-d62c-4d4b-b010-310618e71db7
║   ╠══ Status: Successful
║   ╠══ Creation Time: Sat, 17 Aug 2024 08:35:28 UTC
║   ╠══ Id: 638363b1-d62c-4d4b-b010-310618e71db7
║   ╚══ name: Commissioning host(s) vcf-mgmt01-esxi05.mulab.local to VMware Cloud Foundation
╠══ d8a56718-ea0f-4c6d-bff1-763c116ae576
║   ╠══ Status: Successful
║   ╠══ Creation Time: Sat, 17 Aug 2024 08:41:57 UTC
║   ╠══ Id: d8a56718-ea0f-4c6d-bff1-763c116ae576
║   ╚══ name: Adding new host(s) to cluster
╠══ a9bdd48d-f328-425b-a157-660832361294
║   ╠══ Status: Successful
║   ╠══ Creation Time: Sat, 17 Aug 2024 13:06:16 UTC
║   ╠══ Id: a9bdd48d-f328-425b-a157-660832361294
║   ╚══ name: Removing host(s) from cluster
╠══ 1e05d31d-403b-43a1-9567-a310088d5189
║   ╠══ Status: Successful
║   ╠══ Creation Time: Sat, 17 Aug 2024 13:13:57 UTC
║   ╠══ Id: 1e05d31d-403b-43a1-9567-a310088d5189
║   ╚══ name: Decommissioning host(s) vcf-mgmt01-esxi05.mulab.local from VMware Cloud Foundation
╠══ 1a80b550-cd5a-4bac-91eb-9449977f9965
║   ╠══ Status: Successful
║   ╠══ Creation Time: Sat, 17 Aug 2024 13:32:15 UTC
║   ╠══ Id: 1a80b550-cd5a-4bac-91eb-9449977f9965
║   ╚══ name: Commissioning host(s) vcf-mgmt01-esxi05.mulab.local,vcf-mgmt01-esxi06.mulab.local,vcf-mgmt01-esxi07.mulab.local to VMware Cloud Foundation
╠══ 35edcf0f-53b4-4e70-ad3e-26f834eab05a
║   ╠══ Status: Successful
║   ╠══ Creation Time: Sat, 17 Aug 2024 14:11:02 UTC
║   ╠══ Id: 35edcf0f-53b4-4e70-ad3e-26f834eab05a
║   ╚══ name: Adding cluster vcf-mgmt01-cluster02 to domain vcf-mgmt01
╠══ 59c0bf41-943c-40be-abf9-03c542a93ffa
║   ╠══ Status: Successful
║   ╠══ Creation Time: Sat, 17 Aug 2024 15:10:06 UTC
║   ╠══ Id: 59c0bf41-943c-40be-abf9-03c542a93ffa
║   ╚══ name: Removing cluster vcf-mgmt01-cluster02 from domain vcf-mgmt01
╠══ ab80e678-31a5-4826-9414-5823190c7230
║   ╠══ Status: Successful
║   ╠══ Creation Time: Sat, 17 Aug 2024 15:31:44 UTC
║   ╠══ Id: ab80e678-31a5-4826-9414-5823190c7230
║   ╚══ name: Decommissioning host(s) vcf-mgmt01-esxi06.mulab.local,vcf-mgmt01-esxi05.mulab.local,vcf-mgmt01-esxi07.mulab.local from VMware Cloud Foundation
╠══ 29c13c00-b730-465c-a605-d9b9c3a9f28b
║   ╠══ Status: Successful
║   ╠══ Creation Time: Wed, 02 Oct 2024 11:52:30 UTC
║   ╠══ Id: 29c13c00-b730-465c-a605-d9b9c3a9f28b
║   ╚══ name: Removing domain vcf-vi01
╚══ 0b5b00e3-3ed3-43ca-b2d7-466e3c3cdbac
    ╠══ Status: Successful
    ╠══ Creation Time: Wed, 02 Oct 2024 12:16:52 UTC
    ╠══ Id: 0b5b00e3-3ed3-43ca-b2d7-466e3c3cdbac
    ╚══ name: Decommissioning host(s) vcf-vi01-esxi01.mulab.local,vcf-vi01-esxi03.mulab.local,vcf-vi01-esxi02.mulab.local from VMware Cloud Foundation

Summary Collection completed successfully for : [VCF-SUMMARY]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--get-vcf-services-summary` 选项可查看 VCF 环境中的“服务”摘要信息，包括 SDDC Manager 正常运行的时间和相关服务（例如 LCM）启动和停止的时间。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --get-vcf-services-summary
Welcome to Supportability and Serviceability(SoS) utility!
Summary : /var/log/vmware/vcf/sddc-support/summary-2024-10-04-05-06-38-10894
Summary log : /var/log/vmware/vcf/sddc-support/summary-2024-10-04-05-06-38-10894/sos.log
VCF ServiceTask VCF-SUMMARY is in progress...
╠══ COMMON_SERVICES
║   ╠══ Service Started
║   ║   ╠══ 1: Aug-17-04:43:46
║   ║   ╠══ 2: Aug-17-10:32:33
║   ║   ╠══ 3: Aug-20-14:28:57
║   ║   ╠══ 4: Oct-02-11:41:33
║   ║   ╚══ 5: Oct-04-04:31:02
║   ╚══ Service Stopped
║       ╠══ 1: Aug-17-15:39:44
║       ╠══ 2: Aug-20-15:23:06
║       ╚══ 3: Oct-02-13:43:56
╠══ DOMAIN_MANAGER
║   ╠══ Service Started
║   ║   ╠══ 1: Aug-17-04:43:46
║   ║   ╠══ 2: Aug-17-10:32:33
║   ║   ╠══ 3: Aug-20-14:28:56
║   ║   ╠══ 4: Oct-02-11:41:33
║   ║   ╚══ 5: Oct-04-04:31:01
║   ╚══ Service Stopped
║       ╠══ 1: Jul-30-09:35:18
║       ╠══ 2: Aug-01-07:43:25
║       ╠══ 3: Aug-17-15:39:43
║       ╠══ 4: Aug-20-15:23:05
║       ╚══ 5: Oct-02-13:43:55
╠══ LCM
║   ╠══ Service Started
║   ║   ╠══ 1: Aug-17-04:43:46
║   ║   ╠══ 2: Aug-17-10:32:33
║   ║   ╠══ 3: Aug-20-14:28:56
║   ║   ╠══ 4: Oct-02-11:41:33
║   ║   ╚══ 5: Oct-04-04:31:01
║   ╚══ Service Stopped
║       ╠══ 1: Aug-17-15:39:43
║       ╠══ 2: Aug-20-15:23:05
║       ╚══ 3: Oct-02-13:43:55
╠══ OPERATIONS_MANAGER
║   ╠══ Service Started
║   ║   ╠══ 1: Aug-17-04:43:47
║   ║   ╠══ 2: Aug-17-10:32:34
║   ║   ╠══ 3: Aug-20-14:28:57
║   ║   ╠══ 4: Oct-02-11:41:34
║   ║   ╚══ 5: Oct-04-04:31:03
║   ╚══ Service Stopped
║       ╠══ 1: Aug-17-15:39:43
║       ╠══ 2: Aug-20-15:23:05
║       ╚══ 3: Oct-02-13:43:55
╠══ SDDC_MANAGER_UI
║   ╠══ Service Started
║   ║   ╠══ 1: Aug-17-04:43:48
║   ║   ╠══ 2: Aug-17-10:32:35
║   ║   ╠══ 3: Aug-20-14:28:58
║   ║   ╠══ 4: Oct-02-11:41:35
║   ║   ╚══ 5: Oct-04-04:31:03
║   ╚══ Service Stopped
║       ╠══ 1: Aug-20-14:32:20
║       ╠══ 2: Aug-20-15:23:05
║       ╠══ 3: Oct-02-11:44:58
║       ╠══ 4: Oct-02-13:43:55
║       ╚══ 5: Oct-04-04:34:30
╚══ VCF SDDC Manager Uptime: up 37 minutes


Summary Collection completed successfully for : [VCF-SUMMARY]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

 



## 三、管理 ESXi 和 vCenter Server



SoS 实用程序可以用于管理 VCF 环境中的 ESXi 主机和 vCenter Server，这需要切换到 root 用户才能执行相关命令选项。注意，对于 `Fix-It-Up` 选项，如果未指定 `--doamin-name` 工作负载域，则命令将应用于管理域；如果使用 `--domain-name ALL` 选项，则命令将应用于所有工作负载域。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241004132117351-1572780753.png)](https://github.com)


使用 `--enable-ssh-esxi` 选项“开启”指定工作负载域中所有 ESXi 主机的 SSH 服务。



```
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]# ./sos --enable-ssh-esxi --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Logs : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-31-24-17182
Fix-It-Up log : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-31-24-17182/sos.log
Fix It Up completed successfully for : [ESX_SSH_ENABLEMENT]                                                                                
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]#
```

使用 `--disable-ssh-esxi` 选项“关闭”指定工作负载域中所有 ESXi 主机的 SSH 服务。



```
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]# ./sos --disable-ssh-esxi --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Logs : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-33-07-17702
Fix-It-Up log : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-33-07-17702/sos.log
Fix It Up completed successfully for : [ESX_SSH_DISABLEMENT]                                                                                
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]#
```

使用 `--enable-ssh-vc` 选项“开启”指定工作负载域中 vCenter Server 的 SSH 服务。



```
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]# ./sos --enable-ssh-vc --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Logs : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-34-24-18099
Fix-It-Up log : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-34-24-18099/sos.log
Fix It Up completed successfully for : [SSH_VC_ENABLEMENT]                                                                                
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]# 
```

使用 `--disable-ssh-vc` 选项“关闭”指定工作负载域中 vCenter Server 的 SSH 服务。



```
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]# ./sos --disable-ssh-vc --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Logs : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-35-17-18350
Fix-It-Up log : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-35-17-18350/sos.log
Fix It Up completed successfully for : [SSH_VC_DISABLEMENT]                                                                                
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]#
```

使用 `--enable-lockdown-esxi` 选项“开启”指定工作负载域中所有 ESXi 主机的锁定模式。



```
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]# ./sos --enable-lockdown-esxi --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Logs : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-37-30-18951
Fix-It-Up log : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-37-30-18951/sos.log
Fix It Up completed successfully for : [ESX_LOCKDOWN_ENABLEMENT]                                                                                
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]#
```

使用 `--disable-lockdown-esxi` 选项“关闭”指定工作负载域中所有 ESXi 主机的锁定模式。



```
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]# ./sos --disable-lockdown-esxi --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Logs : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-38-38-19260
Fix-It-Up log : /var/log/vmware/vcf/sddc-support/fix-it-up-2024-10-04-05-38-38-19260/sos.log
Fix It Up completed successfully for : [ESX_LOCKDOWN_DISABLEMENT]                                                                                
root@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]#
```

 



## 四、检查 VCF 环境运行状态



SoS 实用程序可用于检查 VCF 环境中各种组件或服务的运行状态，包括工作负载域中的连接配置状态、计算和存储状态以及网络配置状态等。使用 `--health-check` 选项可以一次性检查 VCF 环境中的所有状态，使用 `--json-output-dir` 选项可以将检查的结果以 JSON 格式输出到指定目录。   


注：如果检查项结果是 GREEN，则表示该项健康状态为正常（NORMAL）；如果检查项结果是 YELLOW，则表示该项健康状态为警告（WARNING）；如果检查项结果是 RED，则表示该项健康状态为严重（CRITICAL）。


使用 `--get-inventory-info` 选项获取指定工作负载域中相关组件的“清单”信息。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --get-inventory-info --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-22-05-45907
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-22-05-45907/sos.log
SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
+-----+-------------+---------------+-------------------------------+---------------+
| SL# | Domain Name |   Component   |         Host Node Name        |    USED IP    |
+-----+-------------+---------------+-------------------------------+---------------+
|  1  |  vcf-mgmt01 |      ESX      | vcf-mgmt01-esxi01.mulab.local | 192.168.32.61 |
|     |             |               | vcf-mgmt01-esxi02.mulab.local | 192.168.32.62 |
|     |             |               | vcf-mgmt01-esxi03.mulab.local | 192.168.32.63 |
|     |             |               | vcf-mgmt01-esxi04.mulab.local | 192.168.32.64 |
|     |             |  SDDC Manager | vcf-mgmt01-sddc01.mulab.local | 192.168.32.70 |
|     |             |    VCENTER    | vcf-mgmt01-vcsa01.mulab.local | 192.168.32.65 |
|     |             | PSC(EMBEDDED) |              N/A              | 192.168.32.65 |
|     |             |      NSX      |  vcf-mgmt01-nsx01.mulab.local | 192.168.32.66 |
|     |             |    NSX MGR    | vcf-mgmt01-nsx01a.mulab.local | 192.168.32.67 |
+-----+-------------+---------------+-------------------------------+---------------+
Progress : 100%, Completed tasks : [VCF-SUMMARY]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [VCF-SUMMARY]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--connectivity-health` 选项检查指定工作负载域中相关组件的“连接”状态。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --connectivity-health --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-23-59-46467
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-23-59-46467/sos.log
NOTE : The Health check operation was invoked without --skip-known-host-check, additional identity checks will be included for Connectivity Health, Password Health and Certificate Health Checks because of security reasons.

SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
Connectivity : GREEN                                                                                 
+-----+-----------------------------------------+----------------------------+-------+
| SL# |                   Area                  |           Title            | State |
+-----+-----------------------------------------+----------------------------+-------+
|  1  |   ESXi : vcf-mgmt01-esxi01.mulab.local  |        Ping status         | GREEN |
|     |                                         |  API Connectivity status   | GREEN |
|     |                                         | ** SSH status is disabled. |       |
|  2  |   ESXi : vcf-mgmt01-esxi02.mulab.local  |        Ping status         | GREEN |
|     |                                         |  API Connectivity status   | GREEN |
|     |                                         | ** SSH status is disabled. |       |
|  3  |   ESXi : vcf-mgmt01-esxi03.mulab.local  |        Ping status         | GREEN |
|     |                                         |  API Connectivity status   | GREEN |
|     |                                         | ** SSH status is disabled. |       |
|  4  |   ESXi : vcf-mgmt01-esxi04.mulab.local  |        Ping status         | GREEN |
|     |                                         |  API Connectivity status   | GREEN |
|     |                                         | ** SSH status is disabled. |       |
|  5  |    NSX: vcf-mgmt01-nsx01.mulab.local    |        Ping status         | GREEN |
|     |                                         |  API Connectivity status   | GREEN |
|  6  | vCenter : vcf-mgmt01-vcsa01.mulab.local |        Ping status         | GREEN |
|     |                                         | ** SSH status is disabled. |       |
+-----+-----------------------------------------+----------------------------+-------+
Progress : 100%, Completed tasks : [VCF-SUMMARY, CONNECTIVITY-CHECK]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [VCF-SUMMARY, CONNECTIVITY-CHECK]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--version-health` 选项检查指定工作负载域中相关组件的“版本”信息。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --version-health --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-28-04-47736
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-28-04-47736/sos.log
SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
Version Check Status : GREEN                                                                                 
+-----+-------------------------------------------+---------------------------+----------------------+-----------------------+-------+
| SL# |                 Component                 | BOM Version (lcmManifest) |   Running version    | VCF Inventory Version | State |
+-----+-------------------------------------------+---------------------------+----------------------+-----------------------+-------+
|  1  |    ESXI: vcf-mgmt01-esxi01.mulab.local    |       8.0.2-22380479      |    8.0.2-22380479    |     8.0.2-22380479    | GREEN |
|  2  |    ESXI: vcf-mgmt01-esxi02.mulab.local    |       8.0.2-22380479      |    8.0.2-22380479    |     8.0.2-22380479    | GREEN |
|  3  |    ESXI: vcf-mgmt01-esxi03.mulab.local    |       8.0.2-22380479      |    8.0.2-22380479    |     8.0.2-22380479    | GREEN |
|  4  |    ESXI: vcf-mgmt01-esxi04.mulab.local    |       8.0.2-22380479      |    8.0.2-22380479    |     8.0.2-22380479    | GREEN |
|  5  | NSX_MANAGER: vcf-mgmt01-nsx01.mulab.local |     4.1.2.1.0-22667789    |  4.1.2.1.0-22667789  |   4.1.2.1.0-22667789  | GREEN |
|  6  |    SDDC: vcf-mgmt01-sddc01.mulab.local    |          5.1.0.0          |       5.1.0.0        |        5.1.0.0        | GREEN |
|  7  |   VCENTER: vcf-mgmt01-vcsa01.mulab.local  |    8.0.2.00100-22617221   | 8.0.2.00100-22617221 |  8.0.2.00100-22617221 | GREEN |
+-----+-------------------------------------------+---------------------------+----------------------+-----------------------+-------+
Progress : 100%, Completed tasks : [VCF-SUMMARY, VERSION-CHECK]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [VERSION-CHECK, VCF-SUMMARY]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--services-health` 选项检查指定工作负载域中相关组件的“服务”状态。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --services-health --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-30-33-48554
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-30-33-48554/sos.log
SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
Services : GREEN                                                                                 
+-----+-----------------------------------------+--------------------+-------+
| SL# |                   Area                  |       Title        | State |
+-----+-----------------------------------------+--------------------+-------+
|  1  |   SDDC : vcf-mgmt01-sddc01.mulab.local  |        NTP         | GREEN |
|     |                                         |      SOSREST       | GREEN |
|     |                                         |      POSTGRES      | GREEN |
|     |                                         |  COMMON_SERVICES   | GREEN |
|     |                                         |  SDDC_MANAGER_UI   | GREEN |
|     |                                         |   DOMAIN_MANAGER   | GREEN |
|     |                                         | OPERATIONS_MANAGER | GREEN |
|     |                                         |  VIP-MANAGER-i18n  | GREEN |
|     |                                         |        LCM         | GREEN |
|  2  | VCENTER : vcf-mgmt01-vcsa01.mulab.local |      applmgmt      | GREEN |
|     |                                         |      rsyslog       | GREEN |
+-----+-----------------------------------------+--------------------+-------+
Progress : 100%, Completed tasks : [SERVICES-CHECK, VCF-SUMMARY]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [SERVICES-CHECK, VCF-SUMMARY]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ 
```

使用 `--compute-health` 选项检查指定工作负载域中“计算”相关的运行状态。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --compute-health --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-34-15-49636
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-34-15-49636/sos.log
SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
Compute : GREEN                                                                                 
+-----+----------------------------------------+----------------------------+-------+
| SL# |                  Area                  |           Title            | State |
+-----+----------------------------------------+----------------------------+-------+
|  1  |  ESXi : vcf-mgmt01-esxi01.mulab.local  |       License check        | GREEN |
|     |                                        |     ESXi Health Status     | GREEN |
|     |                                        | Check disks for partitions | GREEN |
|  2  |  ESXi : vcf-mgmt01-esxi02.mulab.local  |       License check        | GREEN |
|     |                                        |     ESXi Health Status     | GREEN |
|     |                                        | Check disks for partitions | GREEN |
|  3  |  ESXi : vcf-mgmt01-esxi03.mulab.local  |       License check        | GREEN |
|     |                                        |     ESXi Health Status     | GREEN |
|     |                                        | Check disks for partitions | GREEN |
|  4  |  ESXi : vcf-mgmt01-esxi04.mulab.local  |       License check        | GREEN |
|     |                                        |     ESXi Health Status     | GREEN |
|     |                                        | Check disks for partitions | GREEN |
|  5  | vCenter: vcf-mgmt01-vcsa01.mulab.local |    Check Health Status     | GREEN |
+-----+----------------------------------------+----------------------------+-------+
Progress : 100%, Completed tasks : [ALARM-CHECK, COMPUTE-CHECK, VCF-SUMMARY]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [COMPUTE-CHECK, ALARM-CHECK, VCF-SUMMARY]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--storage-health` 选项检查指定工作负载域中“存储”相关的运行状态。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --storage-health --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-36-29-50334
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-36-29-50334/sos.log
SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
vSAN : GREEN                                                                                 
+-----+--------------------------------------------------------------------------+---------------------------------------------------------+-------+
| SL# |                                   Area                                   |                          Title                          | State |
+-----+--------------------------------------------------------------------------+---------------------------------------------------------+-------+
|  1  | vCenter : vcf-mgmt01-vcsa01.mulab.local - Cluster : vcf-mgmt01-cluster01 |            Overall Cluster vSAN Health Status           | GREEN |
|     |                                                                          | vSAN Capacity is 14.36%, utilized 574.38GB of 3999.46GB | GREEN |
|     |                                                                          |            Total no. of hosts in cluster : 4            | GREEN |
|     |                                                                          |               Active ReSyncing Objects : 0              | GREEN |
|     |                                                                          |         ** Compression disabled on this cluster.        |       |
|     |                                                                          |         ** Encryption disabled on this cluster.         |       |
|     |                                                                          |       **  Deduplication disabled on this cluster.       |       |
|     |                                                                          |            ** Stretched cluster is disabled.            |       |
|  2  |   vCenter : vcf-mgmt01-vcsa01.mulab.local VM storage policy compliance   |                    vcf-mgmt01-nsx01a                    | GREEN |
|     |                                                                          |                    vcf-mgmt01-vcsa01                    | GREEN |
|     |                                                                          |                    vcf-mgmt01-sddc01                    | GREEN |
|     |                                                                          |        vCLS-9a2850fd-331a-4861-a3c0-7896c9697f50        | GREEN |
|     |                                                                          |        vCLS-6a301117-c407-4aef-ad92-97de5fd22d71        | GREEN |
|     |                                                                          |        vCLS-d6383bf5-a37e-487f-83a6-89a2ff9014f7        | GREEN |
+-----+--------------------------------------------------------------------------+---------------------------------------------------------+-------+
Progress : 100%, Completed tasks : [VSAN-CHECK, VCF-SUMMARY]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [VSAN-CHECK, VCF-SUMMARY]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ 
```

使用 `--storage-health` 和 `--run-vsan-checks` 选项检查指定工作负载域中“存储”相关的运行状态并同时运行虚拟机创建测试。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --storage-health --run-vsan-checks --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-38-59-51057
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-38-59-51057/sos.log
SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
vSAN : GREEN                                                                                 
+-----+--------------------------------------------------------------------------+---------------------------------------------------------+-------+
| SL# |                                   Area                                   |                          Title                          | State |
+-----+--------------------------------------------------------------------------+---------------------------------------------------------+-------+
|  1  | vCenter : vcf-mgmt01-vcsa01.mulab.local - Cluster : vcf-mgmt01-cluster01 |            Overall Cluster vSAN Health Status           | GREEN |
|     |                                                                          | vSAN Capacity is 14.36%, utilized 574.39GB of 3999.46GB | GREEN |
|     |                                                                          |            Total no. of hosts in cluster : 4            | GREEN |
|     |                                                                          |               Active ReSyncing Objects : 0              | GREEN |
|     |                                                                          |         ** Compression disabled on this cluster.        |       |
|     |                                                                          |         ** Encryption disabled on this cluster.         |       |
|     |                                                                          |       **  Deduplication disabled on this cluster.       |       |
|     |                                                                          |            ** Stretched cluster is disabled.            |       |
|     |                                                                          |             Proactive Cluster VM Create Test            | GREEN |
|  2  |   vCenter : vcf-mgmt01-vcsa01.mulab.local VM storage policy compliance   |                    vcf-mgmt01-nsx01a                    | GREEN |
|     |                                                                          |                    vcf-mgmt01-vcsa01                    | GREEN |
|     |                                                                          |                    vcf-mgmt01-sddc01                    | GREEN |
|     |                                                                          |        vCLS-9a2850fd-331a-4861-a3c0-7896c9697f50        | GREEN |
|     |                                                                          |        vCLS-6a301117-c407-4aef-ad92-97de5fd22d71        | GREEN |
|     |                                                                          |        vCLS-d6383bf5-a37e-487f-83a6-89a2ff9014f7        | GREEN |
+-----+--------------------------------------------------------------------------+---------------------------------------------------------+-------+
Progress : 100%, Completed tasks : [VSAN-CHECK, VCF-SUMMARY]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [VSAN-CHECK, VCF-SUMMARY]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--dns-health` 选项检查指定工作负载域中相关组件“ DNS” 的运行状态。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --dns-health --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-42-55-52110
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-42-55-52110/sos.log
SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
DNS lookup Status : GREEN                                                                                 
+-----+--------------------------------------------+--------------------------+-------+
| SL# |                    Area                    |          Title           | State |
+-----+--------------------------------------------+--------------------------+-------+
|  1  |    ESXi: vcf-mgmt01-esxi01.mulab.local     | Forward DNS lookup check | GREEN |
|     |                                            | Reverse DNS lookup check | GREEN |
|  2  |    ESXi: vcf-mgmt01-esxi02.mulab.local     | Forward DNS lookup check | GREEN |
|     |                                            | Reverse DNS lookup check | GREEN |
|  3  |    ESXi: vcf-mgmt01-esxi03.mulab.local     | Forward DNS lookup check | GREEN |
|     |                                            | Reverse DNS lookup check | GREEN |
|  4  |    ESXi: vcf-mgmt01-esxi04.mulab.local     | Forward DNS lookup check | GREEN |
|     |                                            | Reverse DNS lookup check | GREEN |
|  5  |     NSX: vcf-mgmt01-nsx01.mulab.local      | Forward DNS lookup check | GREEN |
|     |                                            | Reverse DNS lookup check | GREEN |
|  6  |     NSX: vcf-mgmt01-nsx01a.mulab.local     | Forward DNS lookup check | GREEN |
|     |                                            | Reverse DNS lookup check | GREEN |
|  7  | SDDCMANAGER: vcf-mgmt01-sddc01.mulab.local | Forward DNS lookup check | GREEN |
|     |                                            | Reverse DNS lookup check | GREEN |
|  8  |   vCenter: vcf-mgmt01-vcsa01.mulab.local   | Forward DNS lookup check | GREEN |
|     |                                            | Reverse DNS lookup check | GREEN |
+-----+--------------------------------------------+--------------------------+-------+
Progress : 100%, Completed tasks : [VCF-SUMMARY, DNS-CHECK]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [VCF-SUMMARY, DNS-CHECK]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--ntp-health` 选项检查指定工作负载域中相关组件 “NTP” 的运行状态。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --ntp-health --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-44-45-52672
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-44-45-52672/sos.log
SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
Progress : 100%, Completed tasks : [VCF-SUMMARY, NTP-CHECK]
NTP : GREEN                                                                                 
+-----+---------------------------------------------+------------+-------+
| SL# |                     Area                    |   Title    | State |
+-----+---------------------------------------------+------------+-------+
|  1  |     ESXi : vcf-mgmt01-esxi01.mulab.local    | NTP Status | GREEN |
|     |                                             |  ESX Time  | GREEN |
|  2  |     ESXi : vcf-mgmt01-esxi02.mulab.local    | NTP Status | GREEN |
|     |                                             |  ESX Time  | GREEN |
|  3  |     ESXi : vcf-mgmt01-esxi03.mulab.local    | NTP Status | GREEN |
|     |                                             |  ESX Time  | GREEN |
|  4  |     ESXi : vcf-mgmt01-esxi04.mulab.local    | NTP Status | GREEN |
|     |                                             |  ESX Time  | GREEN |
|  5  |      NSX : vcf-mgmt01-nsx01.mulab.local     | NTP Status | GREEN |
|  6  | SDDC-Manager: vcf-mgmt01-sddc01.mulab.local | NTP Status | GREEN |
|  7  |   vCenter : vcf-mgmt01-vcsa01.mulab.local   | NTP Status | GREEN |
+-----+---------------------------------------------+------------+-------+

Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [NTP-CHECK, VCF-SUMMARY]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ 
```

使用 `--certificate-health` 选项检查指定工作负载域中相关组件“证书”的运行状态。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --certificate-health --domain-name vcf-mgmt01
[sudo] password for vcf
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-50-45-54307
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-50-45-54307/sos.log
NOTE : The Health check operation was invoked without --skip-known-host-check, additional identity checks will be included for Connectivity Health, Password Health and Certificate Health Checks because of security reasons.

SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
Certificates : GREEN                                                                                 
+-----+--------------------------------------------+-------------------------------+--------------------------+--------------------------+-------+
| SL# |                   Entity                   |            Cert CN            |     Cert Start Date      |      Cert End Date       | State |
+-----+--------------------------------------------+-------------------------------+--------------------------+--------------------------+-------+
|  1  |    ESXi: vcf-mgmt01-esxi01.mulab.local     | vcf-mgmt01-esxi01.mulab.local | Jul 24 07:27:44 2024 GMT | Jul 24 07:27:44 2029 GMT | GREEN |
|  2  |    ESXi: vcf-mgmt01-esxi02.mulab.local     | vcf-mgmt01-esxi02.mulab.local | Jul 24 07:29:01 2024 GMT | Jul 24 07:29:01 2029 GMT | GREEN |
|  3  |    ESXi: vcf-mgmt01-esxi03.mulab.local     | vcf-mgmt01-esxi03.mulab.local | Jul 24 07:29:30 2024 GMT | Jul 24 07:29:30 2029 GMT | GREEN |
|  4  |    ESXi: vcf-mgmt01-esxi04.mulab.local     | vcf-mgmt01-esxi04.mulab.local | Jul 24 07:29:59 2024 GMT | Jul 24 07:29:59 2029 GMT | GREEN |
|  5  |     NSX: vcf-mgmt01-nsx01.mulab.local      |  vcf-mgmt01-nsx01.mulab.local | Jul 25 08:10:40 2024 GMT | Oct 28 08:10:40 2026 GMT | GREEN |
|  6  | SDDCMANAGER: vcf-mgmt01-sddc01.mulab.local | vcf-mgmt01-sddc01.mulab.local | Jul 25 08:52:44 2024 GMT | Oct 28 08:52:44 2026 GMT | GREEN |
|  7  |     VC: vcf-mgmt01-vcsa01.mulab.local      | vcf-mgmt01-vcsa01.mulab.local | Jul 25 07:13:25 2024 GMT | Jul 25 19:13:25 2026 GMT | GREEN |
+-----+--------------------------------------------+-------------------------------+--------------------------+--------------------------+-------+
Progress : 100%, Completed tasks : [CERTIFICATE-CHECK, VCF-SUMMARY]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [VCF-SUMMARY, CERTIFICATE-CHECK]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--password-health` 选项检查指定工作负载域中相关组件“密码”的运行状态。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --password-health --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-52-48-55037
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-52-48-55037/sos.log
NOTE : The Health check operation was invoked without --skip-known-host-check, additional identity checks will be included for Connectivity Health, Password Health and Certificate Health Checks because of security reasons.

SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
Password Expiry Status : GREEN                                                                                 
+-----+-----------------------------------------+---------------------------+-------------------+--------------+-----------------+-------+
| SL# |                Component                |            User           | Last Changed Date | Expiry Date  | Expires in Days | State |
+-----+-----------------------------------------+---------------------------+-------------------+--------------+-----------------+-------+
|  1  |   ESXI : vcf-mgmt01-esxi01.mulab.local  | svc-vcf-vcf-mgmt01-esxi01 |    Sep 29, 2024   |    Never     |      Never      | GREEN |
|     |                                         |            root           |    Sep 29, 2024   |    Never     |      Never      | GREEN |
|  2  |   ESXI : vcf-mgmt01-esxi02.mulab.local  | svc-vcf-vcf-mgmt01-esxi02 |    Sep 29, 2024   |    Never     |      Never      | GREEN |
|     |                                         |            root           |    Sep 29, 2024   |    Never     |      Never      | GREEN |
|  3  |   ESXI : vcf-mgmt01-esxi03.mulab.local  | svc-vcf-vcf-mgmt01-esxi03 |    Sep 29, 2024   |    Never     |      Never      | GREEN |
|     |                                         |            root           |    Sep 29, 2024   |    Never     |      Never      | GREEN |
|  4  |   ESXI : vcf-mgmt01-esxi04.mulab.local  | svc-vcf-vcf-mgmt01-esxi04 |    Sep 29, 2024   |    Never     |      Never      | GREEN |
|     |                                         |            root           |    Sep 29, 2024   |    Never     |      Never      | GREEN |
|  5  |    NSX : vcf-mgmt01-nsx01.mulab.local   |            root           |    Jul 25, 2024   | Oct 23, 2024 |     19 days     | GREEN |
|     |                                         |           admin           |    Jul 25, 2024   | Oct 23, 2024 |     19 days     | GREEN |
|     |                                         |           audit           |    Jul 25, 2024   | Oct 23, 2024 |     19 days     | GREEN |
|  6  |   SDDC : vcf-mgmt01-sddc01.mulab.local  |            vcf            |    Jul 25, 2024   | Jul 25, 2025 |     294 days    | GREEN |
|     |                                         |            root           |    Jul 25, 2024   | Oct 23, 2024 |     19 days     | GREEN |
|     |                                         |           backup          |    Jul 25, 2024   | Jul 25, 2025 |     294 days    | GREEN |
|  7  | vCenter : vcf-mgmt01-vcsa01.mulab.local |            root           |    Jul 25, 2024   | Oct 23, 2024 |     18 days     | GREEN |
+-----+-----------------------------------------+---------------------------+-------------------+--------------+-----------------+-------+
Progress : 100%, Completed tasks : [VCF-SUMMARY, PASSWORD-CHECK]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [PASSWORD-CHECK, VCF-SUMMARY]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--get-host-ips` 选项获取指定工作负载域中 ESXi 主机的 “IP” 地址信息。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --get-host-ips --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-56-27-56060
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-56-27-56060/sos.log
SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
Server Information : GREEN                                                                                 
+-----+-------------------------------+------------------+------------------+-------------------------------+
| SL# |         Host Node Name        | Host IP (Public) | Host IP (Inband) |           Host Name           |
+-----+-------------------------------+------------------+------------------+-------------------------------+
|  1  | VCF-MGMT01-ESXI01.MULAB.LOCAL |  192.168.32.61   |  192.168.32.61   | vcf-mgmt01-esxi01.mulab.local |
|  2  | VCF-MGMT01-ESXI02.MULAB.LOCAL |  192.168.32.62   |  192.168.32.62   | vcf-mgmt01-esxi02.mulab.local |
|  3  | VCF-MGMT01-ESXI03.MULAB.LOCAL |  192.168.32.63   |  192.168.32.63   | vcf-mgmt01-esxi03.mulab.local |
|  4  | VCF-MGMT01-ESXI04.MULAB.LOCAL |  192.168.32.64   |  192.168.32.64   | vcf-mgmt01-esxi04.mulab.local |
+-----+-------------------------------+------------------+------------------+-------------------------------+
Progress : 100%, Completed tasks : [GET-SERVER-DETAILS, VCF-SUMMARY]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [VCF-SUMMARY, GET-SERVER-DETAILS]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--general-health` 选项检查指定工作负载域中 ESXi 主机是否存在错误转储并获取 NSX Manager 和集群的状态。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --general-health --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-58-40-56718
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-58-40-56718/sos.log
SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
General : YELLOW                                                                                 
+-----+--------------------------------------+---------------------------------------------------------------+--------+
| SL# |                 Area                 |                             Title                             | State  |
+-----+--------------------------------------+---------------------------------------------------------------+--------+
|  1  |  ** SDDC: PSC Ring Topology Status   |                       Ring Health Status                      | GREEN  |
|  2  | ESXi : vcf-mgmt01-esxi01.mulab.local | vcf-mgmt01-esxi01.mulab.local - Check for error dumps in ESXi | GREEN  |
|  3  | ESXi : vcf-mgmt01-esxi02.mulab.local | vcf-mgmt01-esxi02.mulab.local - Check for error dumps in ESXi | YELLOW |
|  4  | ESXi : vcf-mgmt01-esxi03.mulab.local | vcf-mgmt01-esxi03.mulab.local - Check for error dumps in ESXi | YELLOW |
|  5  | ESXi : vcf-mgmt01-esxi04.mulab.local | vcf-mgmt01-esxi04.mulab.local - Check for error dumps in ESXi | GREEN  |
|  6  |     vcf-mgmt01-nsx01.mulab.local     |                       NSX Manager Status                      | GREEN  |
|     |                                      |                 No NSX Edge Cluster available                 | GREEN  |
|     |                                      |                Controller Cluster Health status               | GREEN  |
|     |                                      |                   Mgmt Cluster Health status                  | GREEN  |
|     |                                      |            ** Container Clusters are not available            | GREEN  |
+-----+--------------------------------------+---------------------------------------------------------------+--------+
Progress : 100%, Completed tasks : [VCF-SUMMARY]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [VCF-SUMMARY]                                                                                
Operation failed for : [GENERAL-CHECK(2/10 Failed)]                                                                                
For detailed report please refer : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-07-58-40-56718/report.json
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--hardware-compatibility-report` 选项检查指定工作负载域中 ESXi 主机 vSAN 设备的硬件兼容性状态。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --hardware-compatibility-report --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Health Check : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-08-00-47-57477
Health Check log : /var/log/vmware/vcf/sddc-support/healthcheck-2024-10-04-08-00-47-57477/sos.log
SDDC Manager : vcf-mgmt01-sddc01.mulab.local                                                                                
+-------------------------+-----------+
|          Stage          |   Status  |
+-------------------------+-----------+
|         Bringup         | Completed |
| Management Domain State | Completed |
+-------------------------+-----------+
+--------------------+---------------+
|     Component      |    Identity   |
+--------------------+---------------+
|    SDDC-Manager    | 192.168.32.70 |
| Number of Servers  |       4       |
+--------------------+---------------+
Hardware Compatibility : GREEN                                                                                 
+-----+-------------------------------+-------------------------------------------------+--------+---------+
| SL# |              Area             |                      Title                      | Entity |  State  |
+-----+-------------------------------+-------------------------------------------------+--------+---------+
|  1  | Cluster: vcf-mgmt01-cluster01 |              vSAN HCL DB up-to-date             |        |  GREEN  |
|     |                               |             vSAN HCL DB Auto Update             |        | SKIPPED |
|     |                               |       SCSI controller is VMware certified       |        |  GREEN  |
|     |                               |          NVMe device can be identified          |        |  GREEN  |
|     |                               |         NVMe device is VMware certified         |        | SKIPPED |
|     |                               | Controller is VMware certified for ESXi release |        |  GREEN  |
|     |                               |      Controller driver is VMware certified      |        |  GREEN  |
|     |                               |     Controller firmware is VMware certified     |        |  GREEN  |
|     |                               |    Physical NIC link speed meets requirements   |        | SKIPPED |
|     |                               |      Host physical memory compliance check      |        |  GREEN  |
|     |                               |                                                 |        |         |
+-----+-------------------------------+-------------------------------------------------+--------+---------+
Progress : 100%, Completed tasks : [VCF-SUMMARY, HARDWARE-COMPATIBILITY-CHECK]
Legend:

 GREEN - No attention required, health status is NORMAL
 YELLOW - May require attention, health status is WARNING
 RED - Requires immediate attention, health status is CRITICAL


Health Check completed successfully for : [VCF-SUMMARY, HARDWARE-COMPATIBILITY-CHECK]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

 



## 五、收集 VCF 相关组件日志



SoS 实用程序可用于[收集 VCF 环境中相关组件的日志](https://github.com)信息，包括部署的 ESXi、vCenter Server、NSX Manager 以及 SDDC Manager 等。这些日志可以用于解决 VCF 中遇到的相关问题，或者用于提供给 VMware 技术支持以协助你解决问题。


可以通过指定特定组件选项以执行特定组件日志的收集，如果运行 SoS 实用程序（如 `sudo ./sos` ）而不指定任何特定组件的选项，则 SoS 工具将收集 SDDC Manager、API 和 VMware Cloud Foundation 摘要日志。可以使用 `--collect-all-logs` 选项收集所有组件的日志信息，不过这需要很长的时间。SoS 日志收集可能会在 60 分钟后超时，如果 SoS 实用程序超时，请使用特定选项收集特定于组件的日志或将日志收集限制为特定集群。


注意：默认情况下，SoS 实用程序会将输出的日志存放在 SDDC Manager 中的 /var/log/vmware/vcf/sddc\-support 目录，如果需要输出到指定目录，可以使用 `--logs-dir` 选项。在将输出日志保存到相关目录之前，该实用程序会删除可能存在的先前运行的输出文件，如果要保留较旧的输出文件，请使用 `--no-clean-old-logs` 选项，或者使用 `--logs-folder` 选项设置一个新的输出目录名称。


使用 `--sddc-manager-logs` 选项收集 SDDC Manager 组件的日志信息。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --sddc-manager-logs --log-dir /tmp/sddc-manager --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Logs : /tmp/sddc-manager/sos-2024-10-04-08-19-05-62274
Log file : /tmp/sddc-manager/sos-2024-10-04-08-19-05-62274/sos.log
Log Collection completed successfully for : [SDDC-MANAGER, VCF-SUMMARY, API-LOGS]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ ls -la /tmp/sddc-manager/sos-2024-10-04-08-19-05-62274/
total 84780
drwxr-x--- 3 vcf  vcf       300 Oct  4 08:20 .
drwxr-xr-x 3 root root       60 Oct  4 08:19 ..
drwxr-x--- 6 vcf  vcf       120 Oct  4 08:19 api-logs
-rw-r----- 1 vcf  vcf   3930006 Oct  4 08:19 postgres-db-dump.gz
-rw-r----- 1 vcf  vcf       472 Oct  4 08:20 report.json
-rw-r----- 1 vcf  vcf  82471358 Oct  4 08:20 sddc-202410040819.tgz
-rw-r----- 1 vcf  vcf       435 Oct  4 08:20 sha256_checksum.json
-rw-r----- 1 vcf  vcf    359167 Oct  4 08:20 sos.log
-rw-r----- 1 vcf  vcf       320 Oct  4 08:19 sqlite-sos-db-dump.gz
-rw-r----- 1 vcf  vcf      2277 Oct  4 08:20 vcf-service-report.log
-rw-r----- 1 vcf  vcf      2287 Oct  4 08:20 vcf-service-result.json
-rw-r----- 1 vcf  vcf      3985 Oct  4 08:19 vcf-summary-report.log
-rw-r----- 1 vcf  vcf      4866 Oct  4 08:19 vcf-summary-result.json
-rw-r----- 1 vcf  vcf      7555 Oct  4 08:19 vcf-tasks-summary-report.log
-rw-r----- 1 vcf  vcf      8007 Oct  4 08:19 vcf-tasks-summary-result.json
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--esx-logs` 选项收集指定工作负载域中 ESXi 主机的日志信息。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --esx-logs --log-dir /tmp/esx --domain-name vcf-mgmt01
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Logs : /tmp/esx/sos-2024-10-04-08-23-34-63683
Log file : /tmp/esx/sos-2024-10-04-08-23-34-63683/sos.log
Log Collection completed successfully for : [ESX]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ 
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ ls -la /tmp/esx/sos-2024-10-04-08-23-34-63683/
total 88
drwxr-x--- 3 vcf  vcf    120 Oct  4 08:53 .
drwxr-xr-x 3 root root    60 Oct  4 08:23 ..
drwxr-x--- 2 vcf  vcf    120 Oct  4 08:23 esx
-rw-r----- 1 vcf  vcf   1166 Oct  4 08:53 report.json
-rw-r----- 1 vcf  vcf    626 Oct  4 08:53 sha256_checksum.json
-rw-r----- 1 vcf  vcf  80122 Oct  4 08:53 sos.log
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ ls -la /tmp/esx/sos-2024-10-04-08-23-34-63683/esx/
total 1157700
drwxr-x--- 2 vcf vcf       120 Oct  4 08:23 .
drwxr-x--- 3 vcf vcf       120 Oct  4 08:53 ..
-rw-r----- 1 vcf vcf 301465704 Oct  4 08:53 esx-vcf-mgmt01-esxi01.mulab.local.tgz
-rw-r----- 1 vcf vcf 233610808 Oct  4 08:37 esx-vcf-mgmt01-esxi02.mulab.local.tgz
-rw-r----- 1 vcf vcf 408513043 Oct  4 08:36 esx-vcf-mgmt01-esxi03.mulab.local.tgz
-rw-r----- 1 vcf vcf 241886064 Oct  4 08:39 esx-vcf-mgmt01-esxi04.mulab.local.tgz
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--vc-logs` 选项收集指定工作负载域中 vCenter Server 的日志信息。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --vc-logs --log-dir /tmp/vc --domain-name vcf-mgmt01
[sudo] password for vcf
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Logs : /tmp/vc/sos-2024-10-04-08-56-17-72012
Log file : /tmp/vc/sos-2024-10-04-08-56-17-72012/sos.log
Log Collection completed successfully for : [VCENTER-SERVER]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ ls -la /tmp/vc/sos-2024-10-04-08-56-17-72012/
total 72
drwxr-x--- 3 vcf  vcf    120 Oct  4 09:04 .
drwxr-xr-x 3 root root    60 Oct  4 08:56 ..
-rw-r----- 1 vcf  vcf    443 Oct  4 09:04 report.json
-rw-r----- 1 vcf  vcf    166 Oct  4 09:04 sha256_checksum.json
-rw-r----- 1 vcf  vcf  62354 Oct  4 09:04 sos.log
drwxr-x--- 2 vcf  vcf     60 Oct  4 09:04 vc
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ ls -la /tmp/vc/sos-2024-10-04-08-56-17-72012/vc
total 234824
drwxr-x--- 2 vcf vcf        60 Oct  4 09:04 .
drwxr-x--- 3 vcf vcf       120 Oct  4 09:04 ..
-rw-r----- 1 vcf vcf 240456932 Oct  4 09:04 vc-vcf-mgmt01-vcsa01.mulab.local-vm-support.tgz
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

使用 `--nsx-logs` 选项收集指定工作负载域中 NSX 的日志信息。



```
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ sudo ./sos --nsx-logs --log-dir /tmp/nsx --domain-name vcf-mgmt01
[sudo] password for vcf
Welcome to Supportability and Serviceability(SoS) utility!
Performing SoS operation for vcf-mgmt01 domain components
Logs : /tmp/nsx/sos-2024-10-04-09-21-42-78625
Log file : /tmp/nsx/sos-2024-10-04-09-21-42-78625/sos.log
Progress : Task NSX_MANAGER is in progress...
Log Collection completed successfully for : [NSX_MANAGER]                                                                                
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ 
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ ls -la /tmp/nsx/sos-2024-10-04-09-21-42-78625/
total 60
drwxr-x--- 3 vcf  vcf    120 Oct  4 10:03 .
drwxr-xr-x 3 root root    60 Oct  4 09:21 ..
drwxr-x--- 3 vcf  vcf     60 Oct  4 10:03 nsx
-rw-r----- 1 vcf  vcf    633 Oct  4 10:03 report.json
-rw-r----- 1 vcf  vcf    231 Oct  4 10:03 sha256_checksum.json
-rw-r----- 1 vcf  vcf  49465 Oct  4 10:03 sos.log
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ ls -la /tmp/nsx/sos-2024-10-04-09-21-42-78625/nsx/
total 0
drwxr-x--- 3 vcf vcf  60 Oct  4 10:03 .
drwxr-x--- 3 vcf vcf 120 Oct  4 10:03 ..
drwxr-x--- 2 vcf vcf  80 Oct  4 10:03 nsxt_manager-vcf-mgmt01-nsx01.mulab.local
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$ ls -la /tmp/nsx/sos-2024-10-04-09-21-42-78625/nsx/nsxt_manager-vcf-mgmt01-nsx01.mulab.local/
total 987212
drwxr-x--- 2 vcf vcf         80 Oct  4 10:03 .
drwxr-x--- 3 vcf vcf         60 Oct  4 10:03 ..
-rw-r----- 1 vcf vcf        639 Oct  4 10:01 manifest_20241004_092150.json
-rw-r----- 1 vcf vcf 1010898804 Oct  4 09:55 nsx_manager_acfa3b42-457b-327a-c5fc-61e672f9b502_20241004_092155.tgz
vcf@vcf-mgmt01-sddc01 [ /opt/vmware/sddc-support ]$
```

