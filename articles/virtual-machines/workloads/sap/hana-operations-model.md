---
title: Modello di operazioni di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Modello di operazioni di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a8ea845dd53048766abc337a1351a408ea7f1bb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099699"
---
# <a name="operations-model-and-responsibilities"></a>Responsabilità e modello operativo

Il servizio fornito con SAP HANA in Azure (istanze di grandi dimensioni) è allineato ai servizi IaaS di Azure. Il cliente ottiene un'istanza Large di HANA con un sistema operativo installato ottimizzato per SAP HANA. Come per le VM IaaS di Azure, quasi tutte le attività di protezione avanzata del sistema operativo, installazione di software aggiuntivo, installazione di HANA, funzionamento e aggiornamento del sistema operativo e di HANA sono responsabilità del cliente. Microsoft non impone al cliente aggiornamenti del sistema operativo o di HANA.

![Responsabilità di SAP HANA in Azure (istanze di grandi dimensioni)](./media/hana-overview-architecture/image2-responsibilities.png)

Come illustrato nel diagramma, SAP HANA in Azure (istanze Large) è un'offerta IaaS multi-tenant. La ripartizione di gran parte delle responsabilità è al limite tra sistema operativo e infrastruttura. Microsoft si occupa di tutti gli aspetti del servizio al di sotto della linea relativa al sistema operativo. Il cliente è responsabile degli aspetti al di sopra di tale linea, incluso il sistema operativo. È quindi possibile continuare a usare i metodi adottati più di recente nel sistema locale per la conformità, la sicurezza, la gestione delle applicazioni, le operazioni di base e la gestione del sistema operativo. I sistemi vengono visualizzati come facenti parte della propria rete.

Questo servizio è ottimizzato per SAP HANA. Vi sono pertanto aree in cui il cliente deve collaborare con Microsoft per usare le funzionalità dell'infrastruttura sottostante per ottenere risultati ottimali.

L'elenco seguente fornisce informazioni dettagliate su ogni livello e indica le responsabilità del cliente:

**Rete**: tutte le reti interne del modulo per istanze Large su cui è in esecuzione SAP HANA. Le responsabilità del cliente includono l'accesso alle risorse di archiviazione, la connettività tra le istanze per la scalabilità orizzontale e altre funzioni, la connettività con il panorama applicativo e la connettività con Azure, dove il livello applicazione SAP è ospitato nelle VM. Tra le responsabilità rientra anche la connettività WAN tra data center di Azure per la replica a scopo di ripristino di emergenza. Tutte le reti vengono partizionate dal tenant e configurate con criteri di qualità del servizio.

**Archiviazione**: lo spazio di archiviazione partizionato virtualizzato per tutti i volumi necessari per i server di SAP HANA e per gli snapshot. 

**Server**: i server fisici dedicati per l'esecuzione dei database di SAP HANA assegnati ai tenant. I server di SKU Classe di tipo I sono indipendenti dall'hardware. La configurazione di questi tipi di server viene raccolta e mantenuta in profili che possono essere spostati da un hardware fisico a un altro. Queste operazioni (manuali) di spostamento di un profilo sono in parte paragonabili ad attività di correzione dei servizi di Azure. I server di SKU della classe di tipo II non offrono tale funzionalità.

**SDDC**: il software usato per gestire i data center come entità definite da software. Consente a Microsoft di creare pool delle risorse ai fini della scalabilità, della disponibilità e delle prestazioni.

**Sistema operativo**: il sistema operativo selezionato (SUSE Linux o Red Hat Linux) in esecuzione sui server. Le immagini del sistema operativo disponibili sono state offerte dal singolo fornitore di Linux a Microsoft per l'esecuzione di SAP HANA. È necessario disporre di una sottoscrizione con il fornitore di Linux per la specifica immagine ottimizzata per SAP HANA. Il cliente è responsabile della registrazione delle immagini con il fornitore del sistema operativo. 

Dal momento del trasferimento da Microsoft, l'utente è responsabile di eventuali altre applicazioni di patch del sistema operativo Linux. L'applicazione di patch include i pacchetti aggiuntivi che possono essere necessari per una corretta installazione di SAP HANA e che non sono stati inseriti dallo specifico fornitore di Linux nelle immagini del sistema operativo ottimizzate per SAP HANA. Per altre informazioni, vedere la documentazione e le note SAP relative all'installazione di SAP HANA. 

Il cliente è responsabile dell'applicazione di patch per problemi di malfunzionamento o per l'ottimizzazione del sistema operativo, per i driver relativi all'hardware del server specifico e anche per la sicurezza o le funzionalità del sistema operativo. 

Le responsabilità del cliente includono inoltre il monitoraggio e la pianificazione della capacità in relazione a:

- Utilizzo delle risorse della CPU
- Utilizzo della memoria
- Spazio disponibile, operazioni di I/O al secondo e latenza per i volumi dei dischi
- Traffico del volume di rete tra le istanze Large di HANA e il livello applicazione SAP

L'infrastruttura sottostante delle istanze Large di HANA fornisce funzionalità per il backup e il ripristino del volume del sistema operativo. Anche l'uso di queste funzionalità è responsabilità del cliente.

**Middleware**: principalmente l'istanza di SAP HANA. L'amministrazione, le operazioni e il monitoraggio sono responsabilità del cliente. Sono disponibili funzionalità che consentono di usare gli snapshot di archiviazione a scopo di backup/ripristino e ripristino di emergenza. Queste funzionalità sono offerte dall'infrastruttura. Tra le responsabilità del cliente rientra anche la progettazione della disponibilità elevata o del ripristino di emergenza con queste funzionalità, il relativo uso e il monitoraggio della corretta esecuzione degli snapshot di archiviazione.

**Dati**: i dati gestiti da SAP HANA e altri dati, ad esempio file di backup nei volumi o nelle condivisioni di file. Le responsabilità del cliente includono il monitoraggio dello spazio disponibile su disco e la gestione del contenuto nei volumi, nonché il monitoraggio della corretta esecuzione dei backup dei volumi dei dischi e degli snapshot di archiviazione. La corretta esecuzione della replica dei dati nei siti di ripristino di emergenza è responsabilità di Microsoft.

**Applicazioni:** le istanze di applicazioni SAP o, in caso di applicazioni non SAP, il relativo livello applicazione. Le responsabilità del cliente includono la distribuzione, l'amministrazione, l'esecuzione di operazioni e il monitoraggio di tali applicazioni. Il cliente è responsabile della pianificazione della capacità per l'utilizzo delle risorse della CPU, della memoria, delle risorse di archiviazione di Azure, della larghezza di banda di rete nelle reti virtuali e per l'utilizzo delle risorse dalle reti virtuali di Azure a SAP HANA in Azure (istanze Large).

**WAN**: le connessioni stabilite dall'ambiente locale alle distribuzioni di Azure per i carichi di lavoro. Tutti i clienti con istanze Large di HANA usano Azure ExpressRoute per la connettività. Questa connessione non fa parte della soluzione SAP HANA in Azure (istanze Large) e pertanto la relativa configurazione è responsabilità del cliente.

**Archivio**: può essere preferibile archiviare copie dei dati usando i metodi preferiti negli account di archiviazione. L'archiviazione comporta gestione, conformità, costi e operazioni. Il cliente è responsabile della generazione di copie di archivio e backup in Azure e della relativa archiviazione in base ai criteri di conformità.

Vedere [Contratto di Servizio per SAP HANA in istanze di grandi dimensioni di Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Passaggi successivi**
- Fare riferimento all'[architettura di SAP HANA (istanze Large) in Azure](hana-architecture.md)
