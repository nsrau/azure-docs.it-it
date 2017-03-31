---
title: Uso di SAP in macchine virtuali Windows | Microsoft Docs
description: Informazioni sull&quot;uso di SAP in macchine virtuali (VM) Windows in Microsoft Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: timlt
editor: 
tags: azure-service-management
keywords: 
ms.assetid: 1b455be4-c02f-43e3-8d39-c2d5f216e646
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: campaign-page
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1d6326d5f79ea4e975abd1aa90b0d4a635f4c31a
ms.lasthandoff: 03/27/2017


---
# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>Uso di SAP su macchine virtuali Windows in Azure
Cloud computing è un termine ampiamente diffuso che sta assumendo un'importanza sempre più rilevante nel settore IT, dalle piccole imprese fino alle grandi aziende e alle multinazionali. Microsoft Azure è la piattaforma di servizi cloud di Microsoft che offre un'ampia gamma di nuove possibilità. I clienti possono infatti effettuare rapidamente il provisioning e il deprovisioning di applicazioni come servizi cloud, senza essere soggetti a eventuali limiti tecnici o di budget. Anziché investire tempo e denaro nell'infrastruttura hardware, le aziende possono concentrarsi sull'applicazione, sui processi aziendali e sui vantaggi per clienti e utenti.

Grazie alle macchine virtuali Microsoft Azure, Microsoft offre una piattaforma IaaS (Infrastructure as a Service, infrastruttura distribuita come servizio) completa. Le applicazioni basate su SAP NetWeaver sono supportate in Macchine virtuali di Azure (IaaS). I white paper seguenti descrivono come pianificare e implementare applicazioni basate su SAP NetWeaver su macchine virtuali Windows in Azure. È anche possibile implementare applicazioni basate su SAP NetWeaver in [Macchine virtuali Linux](../../linux/classic/sap-get-started.md).

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver in Azure - Disponibilità elevata
Titolo: SAP NetWeaver in Azure - Clustering di istanze SAP ASCS/SCS tramite cluster di failover di Windows Server in Azure con SIOS DataKeeper

Riepilogo: Questo documento descrive come usare SIOS DataKeeper per definire una configurazione SAP ASCS/SCS a disponibilità elevata in Azure. SAP protegge i componenti con singolo punto di errore, ad esempio SAP ASCS/SCS o Enqueue Replication Services tramite configurazioni cluster di failover di Windows Server che richiedono dischi condivisi. Questi componenti SAP sono essenziali per la funzionalità di un sistema SAP. È quindi necessario implementare la disponibilità elevata per assicurarsi che tali componenti siano in grado di sostenere l'errore di un server o di una macchina virtuale come avviene con le configurazioni dei cluster Windows per gli ambienti Hyper-V e bare metal. Al momento, Azure non è in grado di fornire i dischi condivisi necessari per configurazioni a disponibilità elevata basate su Windows richieste per tali componenti SAP critici. Tuttavia, grazie a DataKeeper di SIOS, è possibile creare le configurazioni cluster di failover di Windows Server necessarie per SAP ASCS/SCS sulla piattaforma IaaS di Azure. Questo documento descrive la procedura dettagliata per installare una configurazione cluster di failover di Windows Server con disco condiviso fornito da SIOS Datakeeper in Azure, nonché le configurazioni dettagliate sul lato Azure, Windows e SAP necessarie per garantire il funzionamento ottimale della configurazione con disponibilità elevata. Questo documento è complementare alla documentazione relativa all'installazione di SAP e alle note su SAP che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP nelle piattaforme specifiche.

Ultimo aggiornamento: agosto 2015

[Scaricare la guida](http://go.microsoft.com/fwlink/?LinkId=613056)


