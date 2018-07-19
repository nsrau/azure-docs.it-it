---
title: Gestione e funzionamento dell'ambiente di produzione di Azure
description: Questo articolo descrive in generale la gestione e il funzionamento della rete di produzione di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: dc389f5f5c155555deb860f041b15b0ea49ee416
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101951"
---
# <a name="azure-production-operations-and-management"></a>Gestione e funzionamento dell'ambiente di produzione di Azure    
La gestione e il funzionamento della rete di produzione di Azure sono il risultato di una collaborazione coordinata tra i team operativi di Azure e del database SQL di Azure. Nell'ambiente vengono usati vari strumenti di monitoraggio delle prestazioni delle applicazioni e del sistema. Il monitoraggio dei dispositivi, dei server, dei servizi di rete e dei processi delle applicazioni viene eseguito con strumenti appropriati.

Sono presenti più livelli di monitoraggio, registrazione e creazione di report, per garantire l'esecuzione protetta di servizi in esecuzione nell'ambiente Microsoft Azure, incluse le azioni seguenti:

- Microsoft Azure Monitoring Agent (MA) raccoglie prima di tutto le informazioni di monitoraggio e dei registri di diagnostica da varie posizioni, inclusi FC e il sistema operativo radice, e le scrive in file di log. Effettua quindi il push di un subset di codice hash delle informazioni in un account di Archiviazione di Azure precedentemente configurato. Il servizio di monitoraggio e di diagnostica (MDS) è inoltre un servizio indipendente che legge i vari dati del monitoraggio e dei registri di diagnostica e riepiloga le informazioni. MDS scrive le informazioni in un registro integrato. Azure usa il monitoraggio della sicurezza di Azure che è un'estensione del proprio sistema di monitoraggio. Include componenti che osservano, analizzano e segnalano eventi pertinenti alla sicurezza da vari punti della piattaforma.
- La piattaforma WinFabric del database SQL di Microsoft Azure offre gestione, distribuzione, sviluppo e servizi di supervisione operativa per il database SQL di Microsoft Azure. Offre servizi di distribuzione a più passaggi e distribuiti, monitoraggio dell'integrità, ripristini automatici e conformità tra le versioni dei servizi. Include i servizi seguenti:

   - Funzionalità di modellazione del servizio con ambiente di sviluppo ad alta fedeltà (i cluster di data center sono costosi e insufficienti)
   - Flussi di lavoro di distruzione e aggiornamento con un clic per la manutenzione e il bootstrap del servizio
   - Creazione di report sull'integrità con flussi di lavoro di ripristino automatizzati per consentire la riparazione automatica
   - Sistemi di monitoraggio, avviso e debug in tempo reale tra i nodi di un sistema distribuito
   - Raccolta centralizzata di dati operativi e metriche per l'analisi della causa radice distribuita e l'analisi approfondita del servizio
   - Strumenti operativi per la distribuzione, la gestione delle modifiche e il monitoraggio
   - La piattaforma WinFabric del database SQL d Microsoft Azure e gli script di watchdog sono continuamente in esecuzione e monitorano in tempo reale.

Se si verifica un'anomalia, viene attivato il processo di risposa agli eventi imprevisti che viene seguito dal team di valutazione degli eventi imprevisti di Azure. Il personale di supporto di Azure appropriato riceve una notifica che chiede di rispondere all'evento imprevisto. Il rilevamento e la risoluzione del problema vengono documentati e gestiti in un sistema di creazione di ticket centralizzato. Le metriche relative ai tempi di attività del sistema sono disponibili in base all'accordo di riservatezza e su richiesta.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Rete aziendale e accesso a più fattori all'ambiente di produzione
La base utenti della rete aziendale include il personale di supporto di Microsoft Azure. La rete aziendale supporta funzioni aziendali interne e include l'accesso alle applicazioni interne che vengono usate per il supporto ai clienti di Azure. La rete aziendale è separata sia logicamente che fisicamente dalla rete di produzione di Azure. Il personale di Microsoft Azure accede alla rete aziendale tramite computer portatili e workstation di Microsoft Azure. Per accedere alle risorse della rete aziendale, tutti gli utenti devono disporre di un account di Active Directory (AD), costituito da un nome utente e una password. Per accedere alla rete aziendale, si usano account di Active Directory, che vengono emessi per tutto il personale Microsoft, i terzisti e i fornitori Microsoft, e vengono gestiti dal MSIT. Gli identificatori utente univoci distinguono il personale in base allo stato di occupazione presso Microsoft.

L'accesso alle applicazioni di Azure interno è controllato attraverso l'autenticazione con Active Directory Federation Services (ADFS). ADFS è un servizio ospitato da MSIT che gestisce l'autenticazione degli utenti della rete aziendale tramite l'uso di un token di sicurezza e di attestazioni utente. ADFS consente alle applicazioni di Microsoft Azure interne di autenticare gli utenti rispetto al dominio di AD aziendale di Microsoft. Per accedere alla rete di produzione dall'ambiente della rete aziendale, l'utente deve autenticarsi tramite l'autenticazione a più fattori.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni che Microsoft esegue per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Availability of Azure infrastructure](azure-infrastructure-availability.md) (Disponibilità dell'infrastruttura di Azure)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Monitoring of Azure infrastructure](azure-infrastructure-monitoring.md) (Monitoraggio dell'infrastruttura di Azure)
- [Integrity of Azure infrastructure](azure-infrastructure-integrity.md) (Integrità dell'infrastruttura di Azure)
- [Protezione dei dati dei clienti in Azure](azure-protection-of-customer-data.md)
