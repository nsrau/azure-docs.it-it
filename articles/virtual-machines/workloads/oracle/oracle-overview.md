---
title: Soluzioni Oracle in Microsoft Azure | Microsoft Docs
description: Informazioni sulle opzioni per la distribuzione delle applicazioni Oracle e soluzioni in Microsoft Azure, tra cui esegue interamente nell'infrastruttura di Azure o si usa la connettività tra cloud con Oracle Cloud dell'infrastruttura (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b8bfa0dfa82f73cad010a608150eac48c7f3d4c8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707439"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Panoramica delle applicazioni Oracle e soluzioni in Azure

Questo articolo presenta le funzionalità per eseguire soluzioni Oracle usando l'infrastruttura di Azure. Vedere anche introduzioni dettagliate a disponibili [immagini di VM Oracle](oracle-vm-solutions.md) in Azure Marketplace e la possibilità di anteprima [interconnessione Azure con Oracle Cloud dell'infrastruttura (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Database Oracle nell'infrastruttura di Azure

Eseguire i database Oracle nell'infrastruttura di Azure usando le immagini Linux disponibili in Azure Marketplace:

* Oracle Database 12.1 12.2 e 18,3 Enterprise Edition 

* Oracle Database 12.1 12.2 e 18,3 Standard Edition 

È anche possibile scegliere di una soluzione di base su un'immagine personalizzata creare da zero in Azure oppure caricare un'immagine personalizzata dall'ambiente locale.

Se lo si desidera configurare con più dischi collegati e migliorare le prestazioni del database tramite l'installazione di Oracle Automated Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Applicazioni in Oracle Linux e WebLogic Server

Nei sistemi operativi Oracle supportati, eseguire le applicazioni aziendali in Azure. Le immagini seguenti sono disponibili in Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 e 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>Opzioni di ripristino di emergenza e disponibilità elevate

* Configurare Oracle Data Guard, Active Data Guard e GoldenGate nell'infrastruttura di Azure in combinazione con [zone di disponibilità](../../../availability-zones/az-overview.md) per la disponibilità elevata.

* Uso [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) per orchestrare e gestire il ripristino di emergenza per macchine virtuali Oracle Linux in Azure e i server fisici o in locale. 

* Abilitare Oracle Real Application Clusters (RAC) in Azure usando [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Integrazione di Azure con OCI (anteprima)

Esecuzione delle applicazioni Oracle nell'infrastruttura di Azure, connessa al database back-end nell'infrastruttura Cloud Oracle (OCI). Questa soluzione Usa le funzionalità seguenti: 

* **La rete tra cloud** -uso diretto di interconnessione disponibile tra FastConnect Oracle ed ExpressRoute di Azure per stabilire connessioni a banda larga, private e a bassa latenza tra l'applicazione e il livello di database.
* **Integrato identità** -configurazione di identità federato tra Azure AD e IDCS Oracle per creare un'origine singola identità per le soluzioni. Abilitare single sign-on gestire le risorse in Azure e OCI.

### <a name="deploy-oracle-applications-on-azure"></a>Distribuisci applicazioni Oracle in Azure

Usare i modelli Terraform per configurare l'infrastruttura di Azure e installare le applicazioni Oracle convalidate e supportate per l'esecuzione nella configurazione tra cloud:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applicazioni di vendita al dettaglio Oracle
* Gestione finanziaria di Oracle Hyperion

Distribuire anche applicazioni personalizzate in Azure che si connettono con OCI e altri servizi di Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Impostare backup di database Oracle OCI

Usare servizi Cloud di Oracle Database (Database autonomi, RAC, Exadata, DBaaS, singolo nodo) in combinazione con le applicazioni Oracle in esecuzione in Azure. Altre informazioni sulle [opzioni di database OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licenze

Distribuzione delle applicazioni Oracle in Azure si basa su un modello "bring your own license". Si presuppone che una licenza idonea per l'uso di software Oracle e che si disponga di un contratto di supporto corrente con Oracle. Oracle garantisce la mobilità delle licenze da locale ad Azure. Vedere il Azure Oracle [domande frequenti su](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla distribuzione [immagini di VM Oracle](oracle-vm-solutions.md) nell'infrastruttura di Azure.

* Altre informazioni su come [interconnessione Azure con OCI](oracle-oci-overview.md).