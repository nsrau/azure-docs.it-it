---
title: Soluzioni Oracle in Microsoft Azure | Microsoft Docs
description: Informazioni sulle opzioni per la distribuzione di applicazioni e soluzioni Oracle in Microsoft Azure, inclusa l'esecuzione completa nell'infrastruttura Azure o l'uso della connettività tra cloud con Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: dbakevlar
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/05/2020
ms.author: kegorman
ms.openlocfilehash: 8f20ad13953e3767e7693733d8c68f3d0aa2057c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91274478"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Panoramica delle applicazioni e delle soluzioni Oracle in Azure

Questo articolo presenta le funzionalità necessarie per eseguire le soluzioni Oracle usando l'infrastruttura Azure. Per informazioni introduttive dettagliate, vedere anche [Applicazioni WebLogic Server in Azure](oracle-weblogic.md), [Immagini di VM Oracle](oracle-vm-solutions.md) in Azure Marketplace ed esaminare la possibilità di [collegare Azure con Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Database Oracle nell'infrastruttura Azure

Eseguire i database Oracle nell'infrastruttura Azure usando Oracle Database in immagini Oracle Linux disponibili in Azure Marketplace:

* Oracle Database 12.1, 12.2 e 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 e 18.3 Standard Edition

* Oracle Database 19,3

È anche possibile scegliere di configurare Oracle Database in un'immagine non di Oracle Linux disponibile in Azure, basare una soluzione su un'immagine personalizzata creata da zero in Azure o caricare un'immagine personalizzata dall'ambiente locale.

Facoltativamente, eseguire la configurazione con più dischi collegati e migliorare le prestazioni del database installando Oracle Automated Storage Management (ASM).

## <a name="weblogic-server-with-azure-service-integrations"></a>WebLogic Server con integrazioni di servizi di Azure

Per accelerare l'accesso al cloud, scegliere tra un'ampia gamma di applicazioni di WebLogic Server in Azure.  Sono disponibili diverse integrazioni di servizi di Azure preconfigurate, tra cui database, gateway applicazione di Azure e Azure Active Directory.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Applicazioni in Oracle Linux e WebLogic Server

Eseguire applicazioni aziendali in Azure su immagini Oracle Linux supportate. In Azure Marketplace sono disponibili le immagini di macchina virtuale seguenti:

* Oracle WebLogic Server 12.1.2

* Oracle Linux con Unbreakable Enterprise kernel (UEK) 6,8, 6,9, 6,10, 7,3 a 7,7, 8,0, 8,1. 

## <a name="high-availability-and-disaster-recovery-options"></a>Opzioni di disponibilità elevata e ripristino di emergenza

* Configurare [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [Active Data Guard con FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) o [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) nell'infrastruttura Azure insieme a [Zone di disponibilità](../../../availability-zones/az-overview.md) per la disponibilità elevata all'interno delle singole aree. È anche possibile impostare queste configurazioni in più aree di Azure per livelli aggiuntivi di disponibilità e ripristino di emergenza.

* Usare [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) per orchestrare e gestire il ripristino di emergenza per le macchine virtuali Oracle Linux in Azure e i server fisici. 

* Abilitare Oracle Real Application Clusters (RAC) in Azure usando la [soluzione VMware di Azure](../../../vmware-cloudsimple/oracle-real-application-clusters.md) o [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Eseguire il backup di carichi di lavoro Oracle

* Eseguire il backup delle macchine virtuali Oracle con [Backup di Azure](../../../backup/backup-overview.md).

* Eseguire il backup di Oracle Database con Oracle RMAN e, facoltativamente, usare [blobfuse di Azure](../../../storage/blobs/storage-how-to-mount-container-linux.md) per montare un [account di archiviazione BLOB di Azure altamente ridondante](../../../storage/common/storage-redundancy.md) e scrivere i backup RMAN in tale account per una maggiore resilienza.

## <a name="integration-of-azure-with-oci"></a>Integrazione di Azure con OCI

Eseguire le applicazioni Oracle nell'infrastruttura Azure, connessa a database back-end in Oracle Cloud Infrastructure (OCI). Questa soluzione usa le funzionalità seguenti: 

* **Rete tra cloud**: usare l'interconnessione diretta disponibile tra Azure ExpressRoute e Oracle FastConnect per stabilire connessioni private, a larghezza di banda elevata e bassa latenza tra l'applicazione e il livello del database.
* **Identità integrata**: configurare l'identità federata tra Azure AD e Oracle IDCS per creare un'unica origine di identità per le soluzioni. Abilitare l'accesso Single Sign-On per gestire le risorse tra OCI e Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Distribuire le applicazioni Oracle in Azure

Per configurare l'infrastruttura Azure e installare le applicazioni Oracle, usare i modelli Terraform. 

Oracle ha certificato queste applicazioni per l'esecuzione in Azure quando si connette a un database Oracle tramite la soluzione di interconnessione cloud di Azure/Oracle:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applicazioni Oracle Retail
* Oracle Hyperion Financial Management

Distribuire anche applicazioni personalizzate in Azure che si connettono a OCI e ad altri servizi di Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Configurare i database Oracle in OCI

Usare i servizi cloud Oracle Database (database autonomo, RAC, Exadata, DBaaS, nodo singolo) in combinazione con il software Oracle in esecuzione in Azure. Per altre informazioni, vedere le [opzioni di database di OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Gestione delle licenze

La distribuzione di applicazioni Oracle in Azure è basata su un modello "Bring Your Own License". Si presuppone che siano disponibili una licenza idonea per l'uso di software Oracle e un contratto di assistenza corrente con Oracle. Oracle garantisce la mobilità delle licenze da locale ad Azure. Vedere le [domande frequenti](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) relative al rapporto Oracle-Azure.

## <a name="next-steps"></a>Passaggi successivi

* Vedere altre informazioni sulle [applicazioni WebLogic Server in Azure](oracle-weblogic.md) e sulle integrazioni dei servizi di Azure supportate.

* Vedere altre informazioni sulla distribuzione di [immagini di VM Oracle](oracle-vm-solutions.md) nell'infrastruttura Azure.

* Vedere altre informazioni su come [interconnettere Azure con OCI](oracle-oci-overview.md).

* Scaricare la [sessione panoramica di Oracle in Azure](https://myignite.techcommunity.microsoft.com/sessions/82915) da Ignite 2019. 
