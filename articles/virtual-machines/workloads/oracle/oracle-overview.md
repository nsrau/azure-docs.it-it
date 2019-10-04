---
title: Soluzioni Oracle in Microsoft Azure | Microsoft Docs
description: Informazioni sulle opzioni per la distribuzione di applicazioni e soluzioni Oracle in Microsoft Azure, inclusa l'esecuzione interamente nell'infrastruttura di Azure o l'uso della connettività tra cloud con Oracle Cloud Infrastructure (OCI).
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
ms.openlocfilehash: d8fed68d9b830df359f8129d55f1b9911f69e8f1
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802242"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Panoramica delle applicazioni e delle soluzioni Oracle in Azure

Questo articolo presenta le funzionalità per eseguire soluzioni Oracle usando l'infrastruttura di Azure. Vedere anche introduzione a [Immagini di macchine virtuali Oracle](oracle-vm-solutions.md) disponibili in Azure Marketplace e la funzionalità di anteprima per l' [interconnessione di Azure con Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Database Oracle nell'infrastruttura di Azure

Eseguire database Oracle nell'infrastruttura di Azure usando Oracle Database su immagini Oracle Linux disponibili in Azure Marketplace:

* Oracle Database 12,1, 12,2 e 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 e 18,3 Standard Edition 

È anche possibile scegliere di configurare Oracle Database in un'immagine non Oracle Linux disponibile in Azure, basare una soluzione su un'immagine personalizzata creata da zero in Azure o caricare un'immagine personalizzata dall'ambiente locale.

Facoltativamente, configurare con più dischi collegati e migliorare le prestazioni del database installando Oracle Automated Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Applicazioni su Oracle Linux e WebLogic Server

Eseguire applicazioni aziendali in Azure nei sistemi operativi Oracle supportati. In Azure Marketplace sono disponibili le immagini seguenti:

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6,8, 6,9, 6,10, 7,3, 7,4, 7,5 e 7,6

## <a name="high-availability-and-disaster-recovery-options"></a>Opzioni di disponibilità elevata e ripristino di emergenza

* Configurare [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [Active Data Guard con FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [partizionamento orizzontale](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) o [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) nell'infrastruttura di Azure insieme a [zone di disponibilità](../../../availability-zones/az-overview.md) per la disponibilità elevata in-Region. È anche possibile configurare queste configurazioni in più aree di Azure per la disponibilità e il ripristino di emergenza aggiunti.

* Usare [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) per orchestrare e gestire il ripristino di emergenza per le macchine virtuali Oracle Linux in Azure e i server fisici o locali. 

* Abilitare Oracle Real Application Clusters (RAC) in Azure usando la [soluzione VMware di Azure](https://docs.azure.cloudsimple.com/oracle-rac/) o [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Eseguire il backup di carichi di lavoro Oracle

* Eseguire il backup delle macchine virtuali Oracle con [backup di Azure](https://docs.microsoft.com/en-us/azure/backup/backup-overview)

* Eseguire il backup del Oracle Database usando Oracle RMAN e, facoltativamente, usare il [fusibile BLOB di Azure](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-how-to-mount-container-linux) per montare un [account di archiviazione BLOB di Azure a redudant elevato](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy) e scrivere i backup RMAN per la resilienza aggiuntiva.

## <a name="integration-of-azure-with-oci-preview"></a>Integrazione di Azure con OCI (anteprima)

Eseguire applicazioni Oracle nell'infrastruttura di Azure, connesse a database back-end in Oracle Cloud Infrastructure (OCI). Questa soluzione USA le funzionalità seguenti: 

* **Rete tra cloud** : usare l'interconnessione diretta disponibile tra Azure ExpressRoute e Oracle FastConnect per stabilire connessioni a larghezza di banda elevata, private e a bassa latenza tra l'applicazione e il livello del database.
* **Identità integrata** : configurare l'identità federata tra Azure ad e Oracle IDC per creare un'unica origine di identità per le soluzioni. Abilitare Single Sign-On per gestire le risorse tra OCI e Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Distribuire applicazioni Oracle in Azure

Usare i modelli di bonifica per configurare l'infrastruttura di Azure e installare le applicazioni Oracle convalidate e supportate per l'esecuzione nella configurazione tra cloud:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applicazioni Oracle Retail
* Gestione finanziaria Oracle Hyperion

Distribuire anche applicazioni personalizzate in Azure che si connettono a OCI e ad altri servizi di Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Configurare i database Oracle in OCI

Usare Oracle Database servizi cloud (database autonomo, RAC, Exadata, DBaaS, nodo singolo) in combinazione con le applicazioni Oracle in esecuzione in Azure. Altre informazioni sulle [Opzioni del database OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licenze

La distribuzione di applicazioni Oracle in Azure si basa su un modello "Bring your own License". Si presuppone che l'utente disponga di una licenza appropriata per l'utilizzo del software Oracle e che sia stato stipulato un contratto di supporto attuale con Oracle. Oracle garantisce la mobilità delle licenze da locale ad Azure. Vedere le [domande frequenti](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)su Oracle-Azure.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla distribuzione di [Immagini di macchine virtuali Oracle](oracle-vm-solutions.md) nell'infrastruttura di Azure.

* Altre informazioni su come [collegare Azure con OCI](oracle-oci-overview.md).
