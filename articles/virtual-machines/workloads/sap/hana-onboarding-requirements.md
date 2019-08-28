---
title: Requisiti di onboarding per SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Requisiti di onboarding per SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99ad334a526b269879034dcc0e1cd0b1b22f1f7f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101185"
---
# <a name="onboarding-requirements"></a>Requisiti di onboarding

Questo elenco include i requisiti per l'esecuzione di SAP HANA in Azure (istanze Large).

**Microsoft Azure**

- Una sottoscrizione di Azure che può essere collegata a SAP HANA in Azure (istanze di grandi dimensioni).
- Contratto di supporto tecnico Premier Microsoft. Per informazioni specifiche sull'esecuzione di SAP in Azure, consultare [SAP Support Note #2015553 - SAP on Microsoft Azure: Support prerequisites](https://launchpad.support.sap.com/#/notes/2015553) (Nota di supporto SAP 2015553 - SAP in Microsoft Azure: prerequisiti di supporto). Se si usano unità di istanze Large di HANA con 384 e più CPU, è necessario anche estendere il contratto di supporto tecnico Premier in modo da includere Azure Rapid Response.
- Conoscenza degli SKU delle istanze Large di HANA necessari dopo l'esecuzione di un esercizio di ridimensionamento con SAP.

**Connettività di rete**

- ExpressRoute da locale ad Azure: per connettere il data center locale ad Azure assicurarsi di ordinare all'ISP una connessione di almeno 1 Gbps. La connettività tra unità di istanze Large di HANA e Azure usa anche la tecnologia ExpressRoute. Questa connessione ExpressRoute tra unità di istanze Large di HANA e Azure è inclusa nel prezzo delle unità di istanze Large di HANA, inclusi tutti gli addebiti per il traffico in uscita e per l'ingresso dati per questo circuito ExpressRoute. Per questo motivo, i clienti non rilevano costi aggiuntivi oltre il collegamento ExpressRoute tra l'ambiente locale e Azure.

**Sistema operativo**

- Licenze per SUSE Linux Enterprise Server 12 per applicazioni SAP.

   > [!NOTE] 
   > Il sistema operativo di Microsoft non è registrato con SUSE e non è connesso a un'istanza di Subscription Management Tool.

- SUSE Linux Subscription Management Tool distribuito in Azure in una VM. Con questo strumento, la soluzione SAP HANA in Azure (istanze Large) può essere registrata e aggiornata da SUSE. Non è disponibile l'accesso diretto a Internet dall'interno del data center delle istanze Large di HANA. 
- Licenze per Red Hat Enterprise Linux 6.7 o 7 per SAP HANA.

   > [!NOTE]
   > Il sistema operativo di Microsoft non è registrato con Red Hat e non è connesso a un'istanza di Red Hat Subscription Manager.

- Red Hat Subscription Manager distribuito in Azure in una VM. Con Red Hat Subscription Manager, la soluzione SAP HANA in Azure (istanze Large) può essere registrata e aggiornata da Red Hat. Non è disponibile l'accesso diretto a Internet dall'interno del tenant distribuito nel modulo per istanze Large in Azure.
- SAP richiede un contratto di supporto anche con il provider di Linux. Questo requisito non viene escluso per il fatto che viene usata la soluzione di istanze Large di HANA o che Linux viene eseguito in Azure. A differenza di alcune delle immagini della raccolta di Azure per Linux, la tariffa del servizio *non* è inclusa nell'offerta della soluzione di istanze Large di HANA. È responsabilità del cliente soddisfare i requisiti di SAP in relazione ai contratti di supporto con il distributore di Linux. 
   - Per SUSE Linux, consultare i requisiti dei contratti di supporto in [SAP Note #1984787 - SUSE Linux Enterprise Server 12: Installation notes](https://launchpad.support.sap.com/#/notes/1984787) (Nota SAP 1984787 - SUSE Linux Enterprise Server 12: note di installazione) e [SAP Note #1056161 - SUSE priority support for SAP applications](https://launchpad.support.sap.com/#/notes/1056161) (Nota SAP 1056161 - Supporto SUSE prioritario per applicazioni SAP).
   - Per Red Hat Linux, è necessario avere livelli di sottoscrizione corretti che includano supporto e aggiornamenti di servizio per i sistemi operativi delle istanze Large di HANA. Red Hat consiglia di ottenere una sottoscrizione Red Hat Enterprise Linux per soluzioni di SAP. Vedere https://access.redhat.com/solutions/3082481. 

Per la matrice di supporto delle diverse versioni di SAP HANA con le varie versioni di Linux, vedere la [nota SAP 2235581](https://launchpad.support.sap.com/#/notes/2235581).

Per la matrice di compatibilità del sistema operativo e le versioni del firmware/ driver HLI, fare riferimento a [OS Upgrade for HLI](os-upgrade-hana-large-instance.md) (Aggiornamento del sistema operativo per HLI).


> [!IMPORTANT] 
> Per le unità di tipo II, solo la versione del sistema operativo SLES 12 SP2 è supportata in questa fase. 


**Database**

- Licenze e componenti di installazione software per SAP HANA (edizione Platform o Enterprise).

**Applicazioni**

- Licenze e componenti di installazione software per le applicazioni SAP che si connettono a SAP HANA e relativi contratti di supporto SAP.
- Licenze e componenti di installazione software per tutte le applicazioni non SAP usate con ambienti SAP HANA in Azure (istanze Large) e i relativi contratti di supporto.

**Competenze**

- Esperienza e conoscenza di IaaS di Azure e dei relativi componenti.
- Esperienza e conoscenza della distribuzione di un carico di lavoro SAP in Azure.
- Certificazione personale dell'installazione di SAP HANA.
- Competenze relative all'architettura SAP per la progettazione della disponibilità elevata e del ripristino di emergenza in SAP HANA.

**SAP**

- Si presuppone che il cliente sia un cliente SAP che ha stipulato un contratto di supporto con SAP.
- Soprattutto per le implementazioni di SKU della classe di tipo II delle istanze Large di HANA, consultare SAP in merito alle versioni di SAP HANA e alle possibili configurazioni in hardware di grandi dimensioni con scalabilità verticale.

**Passaggi successivi**
- Fare riferimento all'[architettura di SAP HANA (istanze Large) in Azure](hana-architecture.md)