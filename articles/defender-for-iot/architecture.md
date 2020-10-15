---
title: Azure Defender per l'architettura dell'it
description: Scopri di più su Azure Defender per l'architettura e il flusso di informazioni.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: rkarlin
ms.openlocfilehash: 4189cb8628db58343c816535836af82825014b7e
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096318"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender per l'architettura dell'it

Questo articolo descrive l'architettura del sistema funzionale della soluzione Defender for Internet.

## <a name="defender-for-iot-components"></a>Defender per i componenti dell'it

Defender for Internet è connesso al cloud di Azure e ai componenti locali. La soluzione è progettata per la scalabilità in ambienti di grandi dimensioni e distribuiti geograficamente con più posizioni remote. Questa soluzione Abilita un'architettura distribuita a più livelli per paese, area, business unit o zona. 

Azure Defender per l'it include i componenti seguenti: 

**Distribuzioni connesse al cloud**

- Azure Defender per macchina virtuale o appliance del sensore Internet
- portale di Azure per l'integrazione e la gestione del cloud in Sentinel di Azure
- Console di gestione locale per la gestione del sito locale
- Un agente di sicurezza incorporato (facoltativo)

**Distribuzioni gapped (offline)**

- Azure Defender per macchina virtuale o appliance del sensore Internet
- Management Console locale per la gestione del sito locale


![Defender per l'architettura dell'it](./media/architecture/defender-iot-security-architecture.png)

### <a name="azure-defender-for-iot-sensors"></a>Azure Defender per sensori Internet

Defender per i sensori Internet rileva e monitora continuamente i dispositivi di rete. I sensori raccolgono il traffico di rete ICS usando il monitoraggio passivo (senza agenti) sui dispositivi e i dispositivi. 
 
Progettato in modo specifico per le reti Internet e Internet, la tecnologia senza agente offre una visibilità approfondita dei rischi e dei rischi in pochi minuti di connessione alla rete. Non ha alcun effetto sulle prestazioni della rete e dei dispositivi di rete a causa dell'approccio NTA (Network Traffic Analysis) non invasivo. 
 
Sfruttando le funzionalità di analisi dei comportamenti brevettate, Internet e di livello 7, è possibile analizzare oltre le tradizionali soluzioni basate su firme per rilevare immediatamente le minacce avanzate, ad esempio malware senza file, in base a attività anomale o non autorizzate. 
  
Il Defender per i sensori di Internet delle cose si connette a una porta di intervallo o a un TAP di rete e inizia immediatamente a eseguire le operazioni DPI e il traffico di rete 
 
La raccolta dati, l'elaborazione, l'analisi e gli avvisi avvengono direttamente sul sensore. Questo lo rende particolarmente adatto per posizioni con larghezza di banda ridotta o connettività a latenza elevata, perché solo i metadati vengono trasferiti alla console di gestione.

Il sensore include cinque motori di rilevamento di analisi. I motori attivano gli avvisi in base all'analisi del traffico in tempo reale e pre-registrato. Sono disponibili i motori seguenti: 

#### <a name="protocol-violation-detection-engine"></a>Motore di rilevamento delle violazioni del protocollo
Il motore di rilevamento delle violazioni del protocollo identifica l'uso di strutture di pacchetti e valori di campo che violano le specifiche del protocollo ICS, ad esempio, l'eccezione Modbus e l'avvio di avvisi di codice di una funzione obsoleti.

#### <a name="policy-violation-detection-engine"></a>Motore di rilevamento delle violazioni dei criteri
Usando Machine Learning, il motore di rilevamento delle violazioni dei criteri avvisa gli utenti di qualsiasi deviazione rispetto al comportamento della linea di base, ad esempio l'uso non autorizzato di codici di funzione specifici, l'accesso a oggetti specifici o la modifica della configurazione del dispositivo. Ad esempio: la versione del software DeltaV è cambiata e gli avvisi di programmazione del PLC non autorizzati. In particolare, il motore di violazione dei criteri modella le reti ICS come sequenze deterministiche di Stati e transizioni, usando una tecnica brevettata chiamata Industrial finite state Modeling (IFSM). Il motore di rilevamento delle violazioni dei criteri stabilisce una linea di base delle reti ICS, in modo che la piattaforma richieda un periodo di apprendimento più breve per la creazione di una linea di base della rete rispetto ad approcci matematici generici o analisi, che sono state originariamente sviluppate per l'IT anziché le reti OT.

#### <a name="industrial-malware-detection-engine"></a>Motore di rilevamento malware industriale
Il motore di rilevamento di malware industriali identifica i comportamenti che indicano la presenza di malware noto, ad esempio Conficker, energia nera, Havex, WannaCry, NotPetya e Triton. 

#### <a name="anomaly-detection-engine"></a>Motore di rilevamento delle anomalie
Il motore di rilevamento delle anomalie rileva comunicazioni e comportamenti insoliti da computer a computer (M2M). Grazie alla modellazione delle reti ICS come sequenze deterministiche di Stati e transizioni, la piattaforma richiede un periodo di apprendimento più breve rispetto agli approcci matematici generici o alle analisi sviluppate in origine, anziché ad OT. Rileva anche le anomalie più velocemente, con falsi positivi minimi. Gli avvisi del motore di rilevamento anomalie includono un numero eccessivo di tentativi di accesso SMB e avvisi di analisi del PLC

#### <a name="operational-incident-detection"></a>Rilevamento dell'evento imprevisto operativo
Il rilevamento dell'evento imprevisto operativo rileva i problemi operativi, ad esempio la connettività intermittente, che possono indicare i primi segnali degli errori delle apparecchiature. Ad esempio, si sospetta che il dispositivo sia disconnesso (non risponde) e che il comando Siemens S7 stop PLC sia stato inviato avvisi.


### <a name="management-consoles"></a>Console di gestione
La gestione di Azure Defender per tutti gli ambienti ibridi viene eseguita tramite due portali di gestione: 
- Console del sensore
- Console di gestione locale
- Portale di Azure

#### <a name="sensor-console"></a>Console del sensore
I rilevamenti dei sensori vengono visualizzati nella console dei sensori, dove possono essere visualizzati, analizzati e analizzati in una mappa di rete, un inventario delle risorse e in una vasta gamma di report, ad esempio report di valutazione dei rischi, data mining query e vettori di attacco. È anche possibile usare la console di per visualizzare e gestire le minacce rilevate dai motori dei sensori, inviare le informazioni ai sistemi di terze parti, gestire gli utenti e altro ancora.

![Defender per la console del sensore Internet](./media/architecture/sensor-console.png)

#### <a name="on-premises-management-console"></a>Console di gestione locale
La console di gestione locale consente agli operatori di Security Operations Center (SOC) di gestire e analizzare gli avvisi aggregati da più sensori in un unico dashboard e fornisce una visualizzazione complessiva dell'integrità delle reti OT.

Questa architettura offre una visualizzazione unificata completa della rete a livello di SOC, la gestione degli avvisi ottimizzata e il controllo della sicurezza della rete operativa, assicurando che il processo decisionale e la gestione dei rischi rimangano impeccabili.

Oltre al controllo remoto multi-tenant, al monitoraggio, all'analisi dei dati e ai sensori centralizzati, la console di gestione fornisce strumenti aggiuntivi per la manutenzione del sistema, ad esempio l'esclusione degli avvisi, e funzionalità di creazione di report completamente personalizzate per ogni appliance remota. Questa architettura scalabile supporta sia la gestione locale a livello di sito, a livello di zona che la gestione globale all'interno del SOC.

La console di gestione può essere distribuita per la configurazione a disponibilità elevata, che fornisce una console di backup che riceve periodicamente backup di tutti i file di configurazione necessari per il ripristino. Se si verifica un errore nella console master, le appliance di gestione del sito locale eseguiranno automaticamente il failover per eseguire la sincronizzazione con la console di backup per mantenere la disponibilità senza interruzioni.

#### <a name="azure-portal"></a>Portale di Azure

Il portale di Defender per Internet delle cose in Azure consente di: ·   Acquistare appliance della soluzione ·   Installare e aggiornare il software ·   Caricare i sensori in Azure ·   Aggiornare i pacchetti di intelligence per le minacce

## <a name="embedded-security-agent-built-in-mode"></a>Agente sicurezza incorporato: modalità predefinita

In modalità **predefinita** , Defender for Internet è abilitato quando si sceglie di attivare l'opzione di **sicurezza** nell'hub Internet delle cose. Il monitoraggio, le raccomandazioni e gli avvisi in tempo reale, la modalità predefinita offre visibilità dei dispositivi in un singolo passaggio e sicurezza senza corrispondenza. La modalità di compilazione non richiede l'installazione dell'agente su tutti i dispositivi e usa l'analisi avanzata sulle attività registrate per analizzare e proteggere il dispositivo Field e l'hub Internet.

## <a name="embedded-security-agent-enhanced-mode"></a>Agente sicurezza incorporato: modalità avanzata

In modalità **avanzata** , dopo l'attivazione dell'opzione di **sicurezza** nell'hub Internet e l'installazione di Defender per gli agenti di dispositivi di tutto il dispositivo, gli agenti raccolgono, aggregano e analizzano gli eventi di sicurezza non elaborati dai dispositivi. Gli eventi di sicurezza non elaborati possono includere connessioni IP, creazione di processi, account di accesso utente e altre informazioni rilevanti per la sicurezza. Il Defender per gli agenti di dispositivo Internet gestisce anche l'aggregazione di eventi per evitare una elevata velocità effettiva della rete. Gli agenti sono altamente personalizzabili e possono essere usati per attività specifiche, ad esempio per l'invio di informazioni importanti al contratto di servizio più veloce o per l'aggregazione di informazioni e contesto di sicurezza estese in segmenti più grandi, evitando i costi di servizio più elevati.

Gli agenti dispositivo e altre applicazioni usano **Azure Send Security Message SDK** per inviare informazioni di sicurezza nell'hub Azure. L'hub tutto ottiene queste informazioni e le invia al Defender per il servizio Internet delle cose.

Una volta abilitato il servizio Defender for Internet, oltre ai dati inoltrati, l'hub Internet invia anche tutti i dati interni per l'analisi da parte di Defender. Questi dati includono i log delle operazioni del cloud, le identità del dispositivo e la configurazione dell'hub. Tutte queste informazioni consentono di creare il Defender per la pipeline di analisi Internet delle cose.

Il Defender per la pipeline di analisi del servizio Internet riceve anche flussi di intelligence per le minacce aggiuntivi da diverse origini all'interno di partner Microsoft e Microsoft. Il Defender per l'intera pipeline di analisi funziona con tutte le configurazioni dei clienti effettuate sul servizio, ad esempio gli avvisi personalizzati e l'uso dell'SDK del messaggio di sicurezza di invio.

Con la pipeline di analisi, Defender for Internet usa tutti i flussi di informazioni per generare raccomandazioni e avvisi di utilità pratica. La pipeline contiene sia le regole personalizzate create dai ricercatori e gli esperti della sicurezza, sia i modelli di apprendimento automatico che cercano la deviazione dal comportamento del dispositivo standard e dall'analisi dei rischi.

Il servizio Defender per gli avvisi e le raccomandazioni per l'it (output della pipeline di analisi) viene scritto nell'area di lavoro Log Analytics di ogni cliente. L'inclusione degli eventi non elaborati nell'area di lavoro e degli avvisi e delle raccomandazioni consente l'analisi approfondita e le query usando i dettagli esatti delle attività sospette rilevate.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come l'architettura di base e il flusso di lavoro di Defender per la soluzione Internet delle cose. Per altre informazioni sui prerequisiti, su come iniziare e abilitare la soluzione di sicurezza nell'hub Internet, vedere gli articoli seguenti:

- [Prerequisiti del servizio](service-prerequisites.md)
- [Introduzione](getting-started.md)
- [Configurare la soluzione](quickstart-configure-your-solution.md)
- [Abilitare la sicurezza nell'hub Internet](quickstart-onboard-iot-hub.md)
- [Domande frequenti su Defender](resources-frequently-asked-questions.md)
- [Avvisi di sicurezza di Defender for Internet](concept-security-alerts.md)
