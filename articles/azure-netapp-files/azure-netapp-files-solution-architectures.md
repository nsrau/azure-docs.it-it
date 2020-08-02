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
ms.date: 07/31/2020
ms.author: b-juche
ms.openlocfilehash: 19e02012c31246b3b6b4636ef61154fbade347ce
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494622"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Architetture di soluzioni con Azure NetApp Files
Questo articolo fornisce i riferimenti alle procedure consigliate che consentono di comprendere le architetture della soluzione per l'uso di Azure NetApp Files.  

Il diagramma seguente riepiloga le categorie di architetture di soluzioni offerte da Azure NetApp Files:

![Categorie architettura soluzione](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>App e soluzioni di database per OSS Linux

Questa sezione fornisce riferimenti per le soluzioni per le applicazioni e i database OSS Linux. 

### <a name="oracle"></a>Oracle

* [Guida alle procedure consigliate per la distribuzione di Oracle in Azure con Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Immagini di VM Oracle e relative distribuzioni in Microsoft Azure: opzioni di configurazione dell'archiviazione condivisa](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [Vantaggi dell'uso di Azure NetApp Files con Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>App di Windows e soluzioni SQL Server

In questa sezione vengono forniti i riferimenti per le applicazioni Windows e le soluzioni SQL Server.

### <a name="file-sharing-and-global-file-caching"></a>Condivisione di file e memorizzazione nella cache di file globali

* [Distribuzione di Talon/Azure NetApp Files](https://youtu.be/91LKb1qsLIM)
* [Creare il proprio NFS di Azure? Wrestling di condivisioni file Linux nel cloud](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)

### <a name="sql-server"></a>SQL Server

* [Distribuire SQL Server su SMB con Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Distribuire SQL Server cluster di failover always on su SMB con Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q)
* [Distribuire gruppi di disponibilità always on con Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc)

## <a name="sap-on-azure-solutions"></a>Soluzioni SAP in Azure

Questa sezione fornisce i riferimenti alle soluzioni SAP in Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>SAP e SAP NetWeaver generici 

* [Applicazioni SAP su Microsoft Azure con Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure su SUSE Linux Enterprise Server con Azure NetApp Files per applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Red Hat Enterprise Linux con Azure NetApp Files per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Windows con Azure NetApp Files (SMB) per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Red Hat Enterprise Linux per le applicazioni SAP guida a più SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="sap-hana"></a>SAP HANA 

* [Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [SAP HANA la scalabilità orizzontale con il nodo standby in macchine virtuali di Azure con Azure NetApp Files in SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [SAP HANA la scalabilità orizzontale con il nodo standby in macchine virtuali di Azure con Azure NetApp Files in Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)

### <a name="sap-tech-community-and-blog-posts"></a>Post di Blog e community di SAP Tech 

* [Azure NetApp Files: backup SAP HANA in pochi secondi](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files: ripristinare il database HANA da un backup di snapshot](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files: SAP HANA l'offload del backup con la sincronizzazione cloud](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Velocizzare le copie del sistema SAP HANA usando Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Volumi cloud ONTAP e Azure NetApp Files: SAP HANA migrazione del sistema è stata semplificata](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>Soluzioni di infrastruttura desktop virtuale

In questa sezione vengono forniti i riferimenti per le soluzioni di infrastruttura desktop virtuale.

### <a name="windows-virtual-desktop"></a>Desktop virtuale Windows

* [Opzioni di archiviazione per i contenitori del profilo FSLogix in desktop virtuale di Windows](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
* [Creare un contenitore di profili FSLogix per un pool host usando Azure NetApp Files](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)
* [Desktop virtuale Windows su scala Enterprise](https://docs.microsoft.com/azure/architecture/example-scenario/wvd/windows-virtual-desktop)

## <a name="hpc-solutions"></a>Soluzioni HPC

In questa sezione vengono forniti i riferimenti per le soluzioni HPC (High Performance Computing). 

### <a name="generic-hpc"></a>HPC generico

* [Azure NetApp Files: sfruttare al meglio l'archiviazione cloud](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Eseguire carichi di lavoro MPI con Azure Batch e Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure Cycle cloud: CycleCloud HPC environments on Azure NetApp Files](https://docs.microsoft.com/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Petrolio e gas

* [HPC (High Performance Computing): petrolio e gas in Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Esegui il software di simulazione del serbatoio in Azure](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Automazione della progettazione elettronica (EDA)

* [Vantaggi dell'uso di Azure NetApp Files per l'automazione della progettazione elettronica](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: EDA HPC Lab con Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)

### <a name="analytics"></a>Analytics

* [Azure NetApp Files: nuova file system condivisa da usare con la griglia SAS in Microsoft Azure](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)

## <a name="azure-platform-services-solutions"></a>Soluzioni dei servizi della piattaforma Azure

Questa sezione fornisce soluzioni per i servizi della piattaforma Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Servizi Kubernetes di Azure e Kubernetes

* [Integrare Azure NetApp Files con il servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/azure-netapp-files)
* [Prestazioni Kubernetes out-of-World in Azure con Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Agente di orchestrazione di archiviazione Trident per i contenitori](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [Eseguire carichi di lavoro MPI con Azure Batch e Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
 
