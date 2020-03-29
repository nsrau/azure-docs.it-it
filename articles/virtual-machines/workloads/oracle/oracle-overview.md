---
title: Soluzioni Oracle in Microsoft Azure | Microsoft Docs
description: Informazioni sulle opzioni per distribuire applicazioni e soluzioni Oracle in Microsoft Azure, inclusa l'esecuzione interamente nell'infrastruttura di Azure o l'utilizzo della connettività cross-cloud con Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: 6ab41182d59f8c96edfca4854aad5f9a13f53436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806610"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Panoramica delle applicazioni e delle soluzioni Oracle in AzureOverview of Oracle Applications and solutions on Azure

Questo articolo introduce le funzionalità per eseguire soluzioni Oracle usando l'infrastruttura di Azure.This article introduces capabilities to run Oracle solutions using Azure infrastructure. Vedere anche introduzione dettagliate alle [immagini di macchine virtuali Oracle](oracle-vm-solutions.md) disponibili in Azure Marketplace e la funzionalità di anteprima per [interconnettere Azure con Oracle Cloud Infrastructure (OCI).](oracle-oci-overview.md)

## <a name="oracle-databases-on-azure-infrastructure"></a>Database Oracle nell'infrastruttura di Azure

Eseguire database Oracle nell'infrastruttura di Azure usando Oracle Database in immagini Oracle Linux disponibili in Azure Marketplace:

* Oracle Database 12.1, 12.2 e 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 e 18.3 Standard Edition 

È anche possibile scegliere di configurare Oracle Database in un'immagine Linux non Oracle disponibile in Azure, basare una soluzione su un'immagine personalizzata creata da zero in Azure o caricare un'immagine personalizzata dall'ambiente locale.

Facoltativamente, configurare con più dischi collegati e migliorare le prestazioni del database installando Oracle Automated Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Applicazioni su Oracle Linux e WebLogic Server

Eseguire applicazioni aziendali in Azure nei sistemi operativi Oracle supportati. The following images are available in the Azure Marketplace:

* Server WebLogic Oracle 12.1.2

* Oracle Linux con Il kernel aziendale indistruttibile (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 e 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>Disponibilità elevata e opzioni di ripristino di emergenzaHigh availability and disaster recovery options

* Configurare [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), Active Data Guard con [FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) o [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) nell'infrastruttura di Azure insieme alle zone di [disponibilità](../../../availability-zones/az-overview.md) per la disponibilità elevata nell'area. È anche possibile configurare queste configurazioni in più aree di Azure per la disponibilità aggiuntiva e il ripristino di emergenza.

* Usare [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) per orchestrare e gestire il ripristino di emergenza per le macchine virtuali Oracle Linux in Azure e i server locali o fisici. 

* Abilitare Oracle Real Application Clusters (RAC) in Azure usando [Azure VMWare Solution](https://docs.azure.cloudsimple.com/oracle-rac/) o [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Carichi di lavoro Oracle di backup

* Eseguire il backup delle macchine virtuali Oracle tramite Backup di AzureBack-up your Oracle VMs using [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)

* Eseguire il backup del database Oracle usando Oracle RMAN e, facoltativamente, usare Azure [Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) per montare un account di archiviazione BLOB di [Azure altamente redudant](https://docs.microsoft.com/azure/storage/common/storage-redundancy) e scrivere i backup RMAN per una maggiore resilienza.

## <a name="integration-of-azure-with-oci-preview"></a>Integrazione di Azure con OCI (anteprima)Integration of Azure with OCI (preview)

Eseguire applicazioni Oracle nell'infrastruttura di Azure, connesse ai database back-end nell'infrastruttura Oracle Cloud Infrastructure (OCI). Questa soluzione utilizza le funzionalità seguenti:This solution uses the following capabilities: 

* **Rete tra cloud:** usare l'interconnessione diretta disponibile tra Azure ExpressRoute e Oracle FastConnect per stabilire connessioni a larghezza di banda elevata, private e a bassa latenza tra l'applicazione e il livello di database.
* **Identità integrata:** configurare l'identità federata tra Azure AD e Oracle IDCS per creare una singola origine di identità per le soluzioni. Abilitare l'accesso Single Sign-On per gestire le risorse in OCI e Azure.Enable single sign-on to manage resources across OCI and Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Distribuire applicazioni Oracle in AzureDeploy Oracle Applications on Azure

Usare i modelli Terraform per configurare l'infrastruttura di Azure e installare le applicazioni Oracle convalidate e supportate per l'esecuzione nella configurazione cross-cloud:Use Terraform templates to set up Azure infrastructure and install Oracle Applications validated and supported to run in the cross-cloud configuration:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applicazioni Oracle Retail
* Oracle Hyperion Financial Management

Distribuire anche applicazioni personalizzate in Azure che si connettono con OCI e altri servizi di Azure.Also deploy custom applications in Azure that connect with OCI and other Azure services.

### <a name="set-up-oracle-databases-in-oci"></a>Configurare i database Oracle in OCI

Usare Oracle Database Cloud Services (Autonomous Database, RAC, Exadata, DBaaS, Single Node) insieme alle applicazioni Oracle in esecuzione in Azure.Use Oracle Database Cloud Services (Autonomous Database, RAC, Exadata, DBaaS, Single Node) in conjunction with Oracle Applications running in Azure. Ulteriori informazioni sulle [opzioni del database OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Gestione delle licenze

La distribuzione di applicazioni Oracle in Azure si basa su un modello di "portare la propria licenza". Si presuppone che l'utente disponga di una licenza appropriata per l'utilizzo del software Oracle e che si disponga di un contratto di supporto corrente con Oracle. Oracle garantisce la mobilità delle licenze da locale ad Azure. Vedere le [domande frequenti su](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)Oracle-Azure .

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla distribuzione di immagini di macchine virtuali Oracle nell'infrastruttura di Azure.Learn more about deploying [Oracle VM images](oracle-vm-solutions.md) in Azure infrastructure.

* Altre informazioni su come [interconnettere Azure con OCI](oracle-oci-overview.md).

* Vedere la [sessione di panoramica](https://myignite.techcommunity.microsoft.com/sessions/82915) di Oracle su Azure da Ignite 2019.Check out the Oracle on Azure overview session from Ignite 2019. 
