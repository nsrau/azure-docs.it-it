---
title: Disponibilità di SAP HANA nelle macchine virtuali di Azure | Microsoft Docs
description: Descrive le operazioni di SAP HANA nelle macchine virtuali di Azure native.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1db56ad31991b85ffad415818c7c67f0ee30808d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60708448"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Disponibilità elevata di SAP HANA per macchine virtuali di Azure

È possibile usare numerose funzionalità di Azure per distribuire database di importanza strategica come SAP HANA nelle macchine virtuali di Azure. Questo articolo fornisce istruzioni su come ottenere la disponibilità per le istanze di SAP HANA ospitate nelle macchine virtuali di Azure. L'articolo descrive numerosi scenari che è possibile implementare usando l'infrastruttura di Azure per aumentare la disponibilità di SAP HANA in Azure. 

## <a name="prerequisites"></a>Prerequisiti

L'articolo presuppone che l'utente abbia familiarità con le nozioni di base sull'infrastruttura distribuita come servizio (IaaS) in Azure, tra cui: 

- Come distribuire macchine virtuali o reti virtuali tramite il portale di Azure o PowerShell.
- Uso dell'interfaccia della riga di comando multipiattaforma di Azure che include l'opzione per l'uso dei modelli JSON (JavaScript Object Notation).

L'articolo presuppone anche che l'utente abbia familiarità con l'installazione, l'amministrazione e l'uso delle istanze di SAP HANA. È particolarmente importante avere familiarità con la configurazione e le operazioni della replica di sistema HANA, incluse le attività come il backup e ripristino dei database SAP HANA.

Questi articoli forniscono una buona panoramica dell'uso di SAP HANA in Azure:

- [Installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Configurare la replica di sistema SAP HANA nelle macchine virtuali di Azure](sap-hana-high-availability.md)
- [Eseguire il backup di SAP HANA in VM di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

È anche consigliabile avere familiarità con il contenuto di questi articoli su SAP HANA:

- [Disponibilità elevata per SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Domande frequenti: Disponibilità elevata per SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Perform system replication for SAP HANA](https://archive.sap.com/documents/docs/DOC-47702) (Eseguire la replica di sistema per SAP HANA)
- [Novità di SAP HANA 2.0 SPS 01: disponibilità elevata](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Network recommendations for SAP HANA system replication](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html) (Raccomandazioni sulla rete per la replica di sistema SAP HANA)
- [SAP HANA system replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) (Replica di sistema SAP HANA)
- [SAP HANA service auto-restart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) (Riavvio automatico del servizio SAP HANA)
- [Configure SAP HANA system replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html) (Configurare la replica di sistema SAP HANA)

Oltre ad avere familiarità con la distribuzione di macchine virtuali in Azure, prima di definire l'architettura di disponibilità in Azure, è consigliabile vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Contratti di servizio per componenti di Azure

Azure offre contratti di servizio di disponibilità differenti per i diversi componenti, ad esempio reti, archiviazione e macchine virtuali. Tutti i contratti di servizio sono documentati. Per altre informazioni, vedere [Contratti di servizio di Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). 

[Contratto di Servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) descrive due diversi contratti di servizio, per due diverse configurazioni:

- Una macchina virtuale singola che usa [dischi SSD Premium di Azure](../../windows/disks-types.md) per il disco del sistema operativo e tutti i dischi dati. Questa opzione offre un tempo di attività mensile del 99,9%.
- Più macchine virtuali (almeno due) organizzate in un [set di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Questa opzione offre un tempo di attività mensile del 99,95%.

Confrontare il requisito di disponibilità con i contratti di servizio che i componenti di Azure possono fornire. Scegliere quindi gli scenari per SAP HANA per ottenere il livello di disponibilità necessario.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [disponibilità di SAP HANA in un'area di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Informazioni sulla [disponibilità di SAP HANA tra aree di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


