---
title: Architetture di soluzioni con Azure NetApp Files | Microsoft Docs
description: Fornisce riferimenti alle procedure consigliate per le architetture della soluzione usando Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: b-juche
ms.openlocfilehash: 0818d113b5475e847c009f979345e103d6e089bb
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91938866"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Architetture di soluzioni con Azure NetApp Files
Questo articolo fornisce i riferimenti alle procedure consigliate che consentono di comprendere le architetture della soluzione per l'uso di Azure NetApp Files.  

Il diagramma seguente riepiloga le categorie di architetture di soluzioni offerte da Azure NetApp Files:

![Categorie architettura soluzione](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>App e soluzioni di database per OSS Linux

Questa sezione fornisce riferimenti per le soluzioni per le applicazioni e i database OSS Linux. 

### <a name="oracle"></a>Oracle

* [Prestazioni del database Oracle in singoli volumi di Azure NetApp Files](performance-oracle-single-volumes.md)
* [Guida alle procedure consigliate per la distribuzione di Oracle in Azure con Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Immagini di VM Oracle e relative distribuzioni in Microsoft Azure: opzioni di configurazione dell'archiviazione condivisa](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Vantaggi dell'uso di Azure NetApp Files con Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>App di Windows e soluzioni SQL Server

In questa sezione vengono forniti i riferimenti per le applicazioni Windows e le soluzioni SQL Server.

### <a name="file-sharing-and-global-file-caching"></a>Condivisione di file e memorizzazione nella cache di file globali

* [Creare il proprio NFS di Azure? Wrestling di condivisioni file Linux nel cloud](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Distribuzione Azure NetApp Files/cache di file globali](https://youtu.be/91LKb1qsLIM)

### <a name="sql-server"></a>SQL Server

* [Distribuire SQL Server su SMB con Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
<!-- * [Deploy SQL Server Always-On Failover Cluster over SMB with Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q) --> 
<!-- * [Deploy Always-On Availability Groups with Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc) --> 

## <a name="sap-on-azure-solutions"></a>Soluzioni SAP in Azure

Questa sezione fornisce i riferimenti alle soluzioni SAP in Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>SAP e SAP NetWeaver generici 

* [Applicazioni SAP su Microsoft Azure con Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure su SUSE Linux Enterprise Server con Azure NetApp Files per applicazioni SAP](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Red Hat Enterprise Linux con Azure NetApp Files per le applicazioni SAP](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Windows con Azure NetApp Files (SMB) per le applicazioni SAP](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Red Hat Enterprise Linux per le applicazioni SAP guida a più SID](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [Disponibilità elevata di SAP HANA scalabilità verticale con Azure NetApp Files in Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [SAP HANA la scalabilità orizzontale con il nodo standby in macchine virtuali di Azure con Azure NetApp Files in SUSE Linux Enterprise Server](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [SAP HANA la scalabilità orizzontale con il nodo standby in macchine virtuali di Azure con Azure NetApp Files in Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)

### <a name="sap-iq-nls"></a>SAP IQ-NLS
*   [Distribuire la soluzione SAP IQ-NLS a disponibilità elevata usando Azure NetApp Files in SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)

### <a name="sap-tech-community-and-blog-posts"></a>Post di Blog e community di SAP Tech 

* [Azure NetApp Files: backup SAP HANA in pochi secondi](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files: ripristinare il database HANA da un backup di snapshot](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files: SAP HANA l'offload del backup con la sincronizzazione cloud](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Velocizzare le copie del sistema SAP HANA usando Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Volumi cloud ONTAP e Azure NetApp Files: SAP HANA migrazione del sistema è stata semplificata](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>Soluzioni di infrastruttura desktop virtuale

In questa sezione vengono forniti i riferimenti per le soluzioni di infrastruttura desktop virtuale.

### <a name="windows-virtual-desktop"></a>Desktop virtuale Windows

* [Vantaggi dell'uso di Azure NetApp Files con Desktop virtuale Windows](solutions-windows-virtual-desktop.md)
* [Opzioni di archiviazione per i contenitori del profilo FSLogix in desktop virtuale di Windows](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [Creare un contenitore di profili FSLogix per un pool host usando Azure NetApp Files](../virtual-desktop/create-fslogix-profile-container.md)
* [Desktop virtuale Windows su scala aziendale](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [Microsoft FSLogix per le procedure consigliate per Azure NetApp Files Enterprise](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)

## <a name="hpc-solutions"></a>Soluzioni HPC

In questa sezione vengono forniti i riferimenti per le soluzioni HPC (High Performance Computing). 

### <a name="generic-hpc"></a>HPC generico

* [Azure NetApp Files: sfruttare al meglio l'archiviazione cloud](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Eseguire carichi di lavoro MPI con Azure Batch e Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure Cycle cloud: CycleCloud HPC environments on Azure NetApp Files](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Petrolio e gas

* [HPC (High Performance Computing): petrolio e gas in Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Esegui il software di simulazione del serbatoio in Azure](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Automazione della progettazione elettronica (EDA)

* [Vantaggi dell'uso di Azure NetApp Files per l'automazione della progettazione elettronica](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: EDA HPC Lab con Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [Azure per il settore dei semiconduttori](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>Analisi

* [Azure NetApp Files: nuova file system condivisa da usare con la griglia SAS in Microsoft Azure](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)
* [Procedure consigliate per l'uso di Microsoft Azure con SAS®](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

## <a name="azure-platform-services-solutions"></a>Soluzioni dei servizi della piattaforma Azure

Questa sezione fornisce soluzioni per i servizi della piattaforma Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Servizi Kubernetes di Azure e Kubernetes

* [Integrare Azure NetApp Files con il servizio Azure Kubernetes](../aks/azure-netapp-files.md)
* [Prestazioni Kubernetes out-of-World in Azure con Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Agente di orchestrazione di archiviazione Trident per i contenitori](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [Eseguire carichi di lavoro MPI con Azure Batch e Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
