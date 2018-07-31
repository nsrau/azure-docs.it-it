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
ms.openlocfilehash: 21ae81f1d8423a9d05208ec6d8c4f31d909d2f9f
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173160"
---
# <a name="azure-production-operations-and-management"></a>Gestione e funzionamento dell'ambiente di produzione di Azure    
La gestione e il funzionamento della rete di produzione di Azure sono il risultato di una collaborazione coordinata tra i team operativi di Azure e del database SQL di Azure. Nell'ambiente, i team usano vari strumenti di monitoraggio delle prestazioni delle applicazioni e del sistema. E usano strumenti appropriati per il monitoraggio della rete dei dispositivi, dei server e dei processi delle applicazioni.

Per garantire l'esecuzione protetta di servizi in esecuzione nell'ambiente Azure, i team delle operazioni implementano diversi livelli di monitoraggio, registrazione e reporting, incluse le azioni seguenti:

- Microsoft Monitoring Agent (MMA) raccoglie prima di tutto le informazioni di monitoraggio e dei registri di diagnostica da varie posizioni, inclusi il controller di infrastruttura (FC) e il sistema operativo radice (OS), e le scrive in file di log. L'agente infine effettua il push di un subset di codice hash delle informazioni in un account di Archiviazione di Azure precedentemente configurato. Il servizio di monitoraggio autonomo e di diagnostica (MDS) legge i vari dati del monitoraggio e dei registri di diagnostica e riepiloga le informazioni. Il servizio di monitoraggio e diagnostica scrive le informazioni in un log integrato. Azure usa il monitoraggio della sicurezza di Azure che è un'estensione del proprio sistema di monitoraggio. Include componenti che osservano, analizzano e segnalano eventi pertinenti alla sicurezza da vari punti della piattaforma.

- La piattaforma Windows Fabric del database SQL di Azure offre gestione, distribuzione, sviluppo e servizi di supervisione operativa per il database SQL di Azure. La piattaforma offre servizi di distribuzione a più passaggi e distribuiti, monitoraggio dell'integrità, ripristini automatici e conformità tra le versioni dei servizi. Include i servizi seguenti:

   - Funzionalità di modellazione del servizio con ambiente di sviluppo ad alta fedeltà (i cluster di data center sono costosi e insufficienti)
   - Flussi di lavoro di distruzione e aggiornamento con un clic per la manutenzione e il bootstrap del servizio
   - Creazione di report sull'integrità con flussi di lavoro di ripristino automatizzati per consentire la riparazione automatica
   - Sistemi di monitoraggio, avviso e debug in tempo reale tra i nodi di un sistema distribuito.
   - Raccolta centralizzata di dati operativi e metriche per l'analisi della causa radice distribuita e l'analisi approfondita del servizio
   - Strumenti operativi per la distribuzione, la gestione delle modifiche e il monitoraggio
   - La piattaforma Windows Fabric del database SQL d Microsoft Azure e gli script di watchdog sono continuamente in esecuzione e monitorano in tempo reale.

Se si verifica un'anomalia, viene attivato il processo di risposta agli eventi imprevisti che viene seguito dal team di valutazione degli eventi imprevisti di Azure. Il personale di supporto di Azure appropriato riceve una notifica che chiede di rispondere all'evento imprevisto. Il rilevamento e la risoluzione del problema vengono documentati e gestiti in un sistema di creazione di ticket centralizzato. Le metriche relative ai tempi di attività del sistema sono disponibili in base all'accordo di riservatezza e su richiesta.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Rete aziendale e accesso a più fattori all'ambiente di produzione
La base utenti della rete aziendale include il personale di supporto di Azure. La rete aziendale supporta funzioni aziendali interne e include l'accesso alle applicazioni interne che vengono usate per il supporto ai clienti di Azure. La rete aziendale è separata sia logicamente che fisicamente dalla rete di produzione di Azure. Il personale di Azure accede alla rete aziendale tramite computer portatili e workstation di Azure. Per accedere alle risorse della rete aziendale, tutti gli utenti devono disporre di un account di Active Directory di Azure (AD di Azure), costituito da un nome utente e una password. Per accedere alla rete aziendale, si usano account di Active Directory di Azure, che vengono emessi per tutto il personale Microsoft, i terzisti e i fornitori, e vengono gestiti da Microsoft Information Technology. Gli identificatori utente univoci distinguono il personale in base allo stato di occupazione presso Microsoft.

L'accesso alle applicazioni di Azure interno è controllato attraverso l'autenticazione con Active Directory Federation Services (AD FS). AD FS è un servizio ospitato da Microsoft Information Technology che gestisce l'autenticazione degli utenti della rete aziendale tramite l'uso di un token di sicurezza e di attestazioni utente. AD FS consente alle applicazioni interne di Azure di autenticare gli utenti nel dominio di active directory aziendale di Microsoft. Per accedere alla rete di produzione dall'ambiente di rete aziendale, gli utenti devono eseguire l'autenticazione tramite Multi-Factor Authentication.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Disponibilità dell'infrastruttura di Azure](azure-infrastructure-availability.md)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Monitoraggio dell'infrastruttura di Azure](azure-infrastructure-monitoring.md)
- [Integrità dell'infrastruttura di Azure](azure-infrastructure-integrity.md)
- [Protezione dei dati dei clienti di Azure](azure-protection-of-customer-data.md)
