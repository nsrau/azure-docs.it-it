---
title: Disponibilità di SAP HANA nelle macchine virtuali di Azure | Microsoft Docs
description: Operazioni di SAP HANA in macchine virtuali native di Azure
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
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Guida alla disponibilità elevata di SAP HANA per macchine virtuali di Azure
Azure offre numerose funzionalità che consentono di distribuire database di importanza strategica come SAP HANA nelle macchine virtuali di Azure. Questo documento fornisce istruzioni su come ottenere la disponibilità per le istanze di SAP HANA ospitate nelle macchine virtuali di Azure. All'interno del documento vengono descritti numerosi scenari che possono essere implementati nell'infrastruttura di Azure per aumentare la disponibilità di SAP HANA in Azure. 

## <a name="prerequisites"></a>prerequisiti
La guida presuppone che l'utente abbia familiarità con le nozioni di base sull'infrastruttura distribuita come servizio (IaaS) in Azure, ad esempio: 

- Come distribuire macchine virtuali o reti virtuali tramite il portale di Azure o PowerShell.
- L'interfaccia della riga di comando multipiattaforma di Azure che include l'opzione per l'uso dei modelli JSON (JavaScript Object Notification).

La guida presuppone anche che l'utente abbia familiarità con l'installazione, l'amministrazione e l'uso delle istanze di SAP HANA, in particolare con la configurazione e le operazioni relative alla replica di sistema SAP HANA o con attività come il backup e il ripristino di database SAP HANA.

Altri articoli che forniscono una buona panoramica degli argomenti relativi a SAP HANA in Azure sono i seguenti:

- [Installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Configurare la replica di sistema SAP HANA nelle macchine virtuali di Azure](sap-hana-high-availability.md)
- [Guida del backup di SAP HANA in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

L'elenco seguente indica gli articoli e i contenuti con i quali è opportuno acquisire familiarità per SAP HANA:

- [Disponibilità elevata per SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Domande frequenti: Disponibilità elevata per SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [How to Perform System Replication for SAP HANA](https://archive.sap.com/documents/docs/DOC-47702) (Come eseguire la replica di sistema per SAP HANA)
- [SAP HANA 2.0 SPS 01 What’s New: High Availability](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/) (Novità di SAP HANA 2.0 SPS 01: disponibilità elevata)
- [Network Recommendations for SAP HANA System Replication](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html) (Raccomandazioni sulla rete per la replica di sistema SAP HANA)
- [SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) (Replica di sistema SAP HANA)
- [SAP HANA Service Auto-Restart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) (Riavvio automatico del servizio SAP HANA)
- [Configuring SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html) (Configurazione della replica di sistema SAP HANA)


Oltre ad avere familiarità con la distribuzione di macchine virtuali in Azure, è anche consigliabile vedere l'articolo [Gestire la disponibilità delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) prima di proseguire con la definizione dell'architettura di disponibilità in Azure.

## <a name="service-level-agreements-for-different-azure-components"></a>Contratti di servizio per diversi componenti di Azure
Azure offre contratti di servizio di disponibilità differenti per i diversi componenti, ad esempio reti, archiviazione e macchine virtuali. Tutti questi contratti di servizio sono documentati ed elencati nella pagina dei [Contratti di servizio di Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). Come si può notare esaminando il [contratto di servizio per le macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), Azure offre due contratti di servizio diversi con due configurazioni distinte. I contratti di servizio sono definiti come segue:

- Una singola macchina virtuale che usa [Archiviazione Premium di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) per il disco del sistema operativo e tutti i dischi dati fornisce una percentuale di tempo di attività mensile pari al 99,9%
- Più macchine virtuali (almeno due) organizzate in un [set di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) forniscono una percentuale di tempo di attività mensile pari al 99,95%

Determinare i propri requisiti di disponibilità rispetto ai contratti di servizio offerti dai componenti di Azure e quindi scegliere i diversi scenari da implementare con SAP HANA per ottenere la disponibilità che è necessario fornire.

## <a name="next-steps"></a>Passaggi successivi
Passare ai documenti:

- [Disponibilità di SAP HANA in un'area di Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilità di SAP HANA tra aree di Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


