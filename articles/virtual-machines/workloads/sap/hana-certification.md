---
title: Certificazione di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Certificazione di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d623a189da5c5eccd73018e1ae0b7e5564fe09ab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709696"
---
# <a name="certification"></a>Certificazione

Oltre alla certificazione NetWeaver, SAP richiede una particolare certificazione per il supporto di SAP HANA in determinate infrastrutture, ad esempio IaaS di Azure.

La nota principale SAP in NetWeaver e a una certificazione di SAP HANA, gradi è [SAP Note #1928533-applicazioni SAP in Azure: Prodotti e tipi di macchine Virtuali di Azure supportati](https://launchpad.support.sap.com/#/notes/1928533).

Le registrazioni delle certificazioni per le unità SAP HANA in Azure (istanze Large) si trovano nel sito [SAP HANA certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Piattaforme IaaS certificate per SAP HANA). 

I tipi di SAP HANA in Azure (istanze Large) a cui si fa riferimento nel sito delle piattaforme IaaS certificate per SAP HANA fornisce ai clienti di Microsoft e SAP la possibilità di distribuire in Azure carichi di lavoro di grandi dimensioni di SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA o altri carichi di lavoro di SAP HANA. La soluzione è basata sul modulo hardware dedicato con certificazione per SAP HANA ([SAP HANA Tailored Data Center Integration - TDI](https://scn.sap.com/docs/DOC-63140)). Se si esegue una soluzione configurata per SAP HANA TDI, tutte le applicazioni basate su SAP HANA, come SAP Business Suite in SAP HANA, SAP BW in SAP HANA, S4/HANA e BW4/HANA, funzioneranno nell'infrastruttura hardware.

Rispetto all'esecuzione di SAP HANA nelle VM, questa soluzione presenta un vantaggio poiché consente di sfruttare volumi di memoria molto più grandi. Per abilitare questa soluzione, è necessario comprendere gli aspetti fondamentali seguenti:

- Il livello applicazione SAP e le applicazioni non SAP vengono eseguiti nelle VM ospitate nei consueti moduli hardware di Azure.
- L'infrastruttura, le distribuzioni di applicazioni e i data center locali dei clienti sono connessi alla piattaforma cloud tramite ExpressRoute (scelta consigliata) o una rete privata virtuale (VPN). Anche Active Directory e DNS vengono estesi in Azure.
- L'istanza del database SAP HANA per il carico di lavoro HANA viene eseguita su SAP HANA in Azure (istanze di grandi dimensioni). Il modulo per istanze Large è connesso alla rete di Azure. In questo modo, il software in esecuzione nelle VM può interagire con l'istanza di HANA in esecuzione in istanze Large di HANA.
- L'hardware di SAP HANA in Azure (istanze Large) è dedicato e viene fornito in un'infrastruttura IaaS in cui è preinstallato SUSE Linux Enterprise Server o Red Hat Enterprise Linux. Come nel caso delle macchine virtuali, gli aggiornamenti e la manutenzione del sistema operativo sono responsabilità del cliente.
- L'installazione di HANA o di eventuali componenti aggiuntivi necessari per l'esecuzione di SAP HANA nelle unità di istanze Large di HANA è responsabilità del cliente, così come tutte le operazioni e le attività di amministrazione di SAP HANA in Azure.
- Oltre alle soluzioni qui descritte, è possibile installare altri componenti nella sottoscrizione di Azure che si connette a SAP HANA in Azure (istanze di grandi dimensioni). Ad esempio, i componenti che consentono la comunicazione o la connessione diretta con il database SAP HANA, come jump server, server RDP, SAP HANA Studio, SAP Data Services per gli scenari SAP BI o soluzioni di monitoraggio di rete.
- Come in Azure, le istanze Large di HANA offrono il supporto di funzionalità per la disponibilità elevata e il ripristino di emergenza.

**Passaggi successivi**
- Vedere [SKU disponibili per HLI](hana-available-skus.md) 