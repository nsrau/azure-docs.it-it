---
title: Disponibilità elevata e ripristino di emergenza dell'hub IoT di Azure | Documentazione Microsoft
description: Descrive le funzionalità di Azure e dell'hub IoT che permettono di compilare soluzioni Azure IoT a disponibilità elevata con opzioni di ripristino di emergenza.
author: rkmanda
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: rkmanda
ms.openlocfilehash: 7479d9a230bd28c2ed2e4c8c79ba9301028af36c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60779373"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Disponibilità elevata e ripristino di emergenza dell'hub IoT

Come primo passaggio verso l'implementazione di una soluzione IoT resiliente, architetti, sviluppatori e proprietari di aziende devono definire gli obiettivi dei tempi di attività per le soluzioni che stanno costruendo. Questi obiettivi possono essere definiti principalmente in base a obiettivi di business specifici per ogni scenario di base. In questo contesto, l'articolo [Informazioni tecniche sulla continuità aziendale Azure](https://docs.microsoft.com/azure/architecture/resiliency/) descrive un contesto generale per aiutare a pensare alla continuità aziendale e al ripristino di emergenza. Il documento [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure](https://msdn.microsoft.com/library/dn251004.aspx) fornisce informazioni sull'architettura nelle strategie per consentire alle applicazioni di Azure di ottenere disponibilità elevata e ripristino di emergenza.

Questo articolo illustra le funzionalità di disponibilità elevata e ripristino di emergenza offerte in modo specifico dal servizio IoT Hub. Le aree generali illustrate in questo articolo sono:

- Disponibilità elevata intra-area
- Ripristino di emergenza tra aree
- Ottenere disponibilità elevata tra aree

A seconda del tempo di attività degli obiettivi definiti per le soluzioni IoT, è necessario determinare quale delle soluzioni indicate di seguito si adatti meglio agli obiettivi di business. L'inclusione di una delle alternative tra disponibilità elevata e ripristino di emergenza nella soluzione IoT richiede un'attenta valutazione dei compromessi tra il:

- Livello di resilienza desiderata 
- Complessità di implementazione e manutenzione
- Impatto COGS

## <a name="intra-region-ha"></a>Disponibilità elevata intra-area

Il servizio Hub IoT fornisce disponibilità elevata intra-area tramite l'implementazione delle ridondanze in quasi tutti i livelli di servizio. Il [Contratto di servizio pubblicato dal servizio Hub IoT](https://azure.microsoft.com/support/legal/sla/iot-hub) è realizzato sfruttando queste ridondanze. Non è necessario alcun intervento aggiuntivo degli sviluppatori di una soluzione IoT per sfruttare i vantaggi di queste funzionalità a disponibilità elevata. Anche se l'Hub IoT offre una garanzia di tempo di attività relativamente elevata, sono comunque attesi errori temporanei come in una qualsiasi piattaforma di elaborazione distribuita. Se sta appena iniziando a usare la migrazione delle soluzioni nel cloud da una soluzione in locale, lo stato attivo si deve spostare ottimizzazione del "tempo medio tra errori" per "tempo medio per il ripristino". In altre parole, gli errori temporanei sono considerati normali quando si opera con il cloud in combinazione. È necessario incorporare appropriati [criteri di ripetizione](iot-hub-reliability-features-in-sdks.md) per i componenti che interagiscono con un'applicazione cloud per gestire i guasti temporanei.

> [!NOTE]
> Anche alcuni servizi di Azure forniscono ulteriori livelli di disponibilità all'interno di un'area grazie all'integrazione con [Zone di disponibilità (AZ)](../availability-zones/az-overview.md). Le AZ non sono attualmente supportate dal servizio Hub IoT.

## <a name="cross-region-dr"></a>Ripristino di emergenza tra aree

Potrebbero esserci alcune rare situazioni in cui un data center subisce interruzioni prolungate a causa di interruzioni dell'alimentazione o altri guasti che coinvolgono risorse fisiche. Tali eventi sono rari e quando si verificano la funzionalità di disponibilità elevata intra area sopra descritta potrebbe non sempre essere d'aiuto. L'Hub IoT offre diverse soluzioni per il ripristino da tali interruzioni estese. 

Le opzioni di ripristino disponibili per i clienti in una situazione di questo tipo sono "Failover avviato da Microsoft" e "Failover manuale". La differenza fondamentale tra i due è che Microsoft avvia il primo mentre l'ultimo è avviato dall'utente. Inoltre, il failover manuale fornisce un obiettivo di recupero inferiore (RTO) rispetto all'opzione di failover avviato da Microsoft. Nelle sezioni seguenti vengono illustrate gli RTO specifici offerti con ogni opzione. Quando viene eseguita una di queste opzioni per effettuare il failover di un Hub IoT dalla sua area principale, l'Hub diventa pienamente funzionante nella corrispondente [area geografica di Azure abbinata](../best-practices-availability-paired-regions.md).

Entrambe queste opzioni di failover offrono gli obiettivi del punto di ripristino (RPO) seguenti:

| Tipo di dati | Obiettivi del punto di ripristino (RPO) |
| --- | --- |
| Registro delle identità |Perdita di dati da 0 a 5 minuti |
| Dati del dispositivo gemello |Perdita di dati da 0 a 5 minuti |
| Messaggi da cloud a dispositivo<sup>1</sup> |Perdita di dati da 0 a 5 minuti |
| Processi padre<sup>1</sup> e dispositivo |Perdita di dati da 0 a 5 minuti |
| Messaggi da dispositivo a cloud |Tutti i messaggi non letti vengono persi |
| Messaggi di monitoraggio delle operazioni |Tutti i messaggi non letti vengono persi |
| Messaggi di feedback da cloud a dispositivo |Tutti i messaggi non letti vengono persi |

<sup>1</sup>I messaggi da cloud a dispositivo e i processi padre non vengono ripristinati come parte del failover manuale nell'offerta di anteprima di questa funzione.

Una volta completata l'operazione di failover per l'Hub IoT, tutte le operazioni dal dispositivo e dalle applicazioni di back-end continueranno a funzionare senza richiedere un intervento manuale.

> [!CAUTION]
> - Il nome e l'endpoint compatibili con l'Hub eventi dell'Hub incorporato degli eventi IoT cambiano dopo il failover. Quando si ricevono messaggi di telemetria dall'endpoint incorporato usano il client Hub eventi o l'host del processore eventi, è necessario [usare la stringa di connessione Hub IoT ](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) per stabilire la connessione. Ciò garantisce che le applicazioni back-end continuino a funzionare senza richiedere l'intervento manuale dopo il failover. Se si usa il nome compatibile con Hub eventi e l'endpoint nell'applicazione back-end direttamente, è necessario riconfigurare l'applicazione eseguendo il [recupero del nuovo nome compatibile con l'Hub eventi e con l'endpoint](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) dopo il failover per continuare operazioni.
>
> - Dopo il failover, gli eventi creati tramite Griglia di eventi possono essere usati tramite le stesse sottoscrizioni configurate in precedenza, purché le sottoscrizioni a Griglia di eventi continuino a essere disponibili.
>
> - Quando si esegue il routing all'archivio BLOB è consigliabile integrare i BLOB e quindi eseguirne l'iterazione per garantire che tutti i contenitori vengano letti senza fare ipotesi sulla partizione. L'intervallo di partizione potrebbe in teoria cambiare durante un failover avviato da Microsoft o un failover manuale. Per informazioni su come enumerare l'elenco di BLOB, vedere [routing nell'archivio blob](iot-hub-devguide-messages-d2c.md#azure-blob-storage).

### <a name="microsoft-initiated-failover"></a>Failover avviato da Microsoft

Il failover avviato da Microsoft viene eseguito da Microsoft in situazioni rare per il failover su tutti gli hub IoT da un'area interessata alla corrispondente area geografica abbinata. Questo processo è un'opzione predefinita (non è consentito agli utenti di rifiutarlo) e non richiede alcun intervento da parte dell'utente. Microsoft si riserva il diritto di effettuare una determinazione di quando questa opzione viene esercitata. Questo meccanismo non implica il consenso dell'utente prima che l'Hub dell'utente eseguito il failover. Il failover avviato da Microsoft ha un obiettivo di ripristino (RTO) di 2-26 ore. 

L'obiettivo RTO è di grandi dimensioni poiché Microsoft deve eseguire l'operazione di failover per conto di tutti i clienti interessati in tale area. Se si sta usando una soluzione IoT meno critica in grado di sostenere un tempo di inattività di circa un giorno, è opportuno prendere in carico questa opzione per soddisfare gli obiettivi generali di ripristino di emergenza per la propria soluzione IoT. Il tempo totale per cui le operazioni di runtime diventano pienamente operative una volta che questo processo è stato attivato, è descritto nella sezione "Tempo di ripristino".

### <a name="manual-failover-preview"></a>Failover manuale (anteprima)

Se gli obiettivi di business non sono soddisfatti dal RTO fornito dal failover avviato da Microsoft, è consigliabile utilizzare il failover manuale per attivare autonomamente il processo di failover. L'obiettivo RTO che utilizza questa opzione può essere compreso tra 10 minuti e un paio d'ore. L'obiettivo RTO è attualmente una funzione del numero di dispositivi registrati con l'istanza dell'Hub IoT su cui viene eseguito il failover. È possibile prevedere l'obiettivo RTO per un Hub di hosting di circa 100.000 dispositivi in approssimativamente 15 minuti. Il tempo totale per cui le operazioni di runtime diventano pienamente operative una volta che questo processo è stato attivato, è descritto nella sezione "Tempo di ripristino".

L'opzione di failover manuale è sempre disponibile per l'uso, indipendentemente dal fatto che nell'area primaria si siano verificati o meno tempi di inattività. Pertanto, questa opzione potrebbe potenzialmente essere usata per eseguire i failover pianificati. Un esempio di utilizzo del failover pianificato consiste nell'eseguire esercitazioni periodiche per il failover. Tuttavia, è importante precisare che un'operazione di failover pianificata determina un tempo di inattività per l'Hub per il periodo definito per l'obiettivo RTO per questa opzione e genera anche una perdita di dati definita dalla tabella RPO sovrastante. Si può considerare l'impostazione di un test di istanza dell'Hub IoT per provare l'opzione di failover pianificato periodicamente per ottenere una maggiore fiducia nella capacità di soluzioni end-to-end backup e in esecuzione quando si verifica un'emergenza reale.

> [!IMPORTANT]
> - Esercitazioni per il test non devono essere eseguite sugli Hub IoT che sono in uso in ambienti di produzione.
>
> - Il failover manuale non deve essere usato come meccanismo per eseguire la migrazione in modo permanente dell'Hub tra le aree geografiche di Azure abbinate. Questa operazione causerebbe un aumento della latenza per le operazioni in esecuzione con l'Hub da dispositivi incluso nell'area primaria precedente.

### <a name="failback"></a>Failback

Attivando l'azione di failover, è possibile eseguire il failback nell'area primaria precedente un secondo momento. Se l'operazione di failover originale è stata eseguita per il ripristino da un'interruzione prolungata nell'area primaria originale, si consiglia di ripristinare l'Hub nella posizione originale dalla situazione di interruzione.

> [!IMPORTANT]
> - Gli utenti possono eseguire al giorn solo 2 failover riusciti e 2 operazioni di failback riuscite.
>
> - Eseguire il backup per eseguire le operazioni di failover/failback non è consentito. Gli utenti dovranno attendere un'ora tra queste operazioni.

### <a name="time-to-recover"></a>Tempo di ripristino

Mentre il nome di dominio completo (e pertanto la stringa di connessione) dell'istanza dell'Hub IoT rimane la stessa dopo il failover, l'indirizzo IP sottostante verrà modificato. Pertanto, il tempo complessivo in cui le operazioni di runtime vengono eseguite contro l'istanza dell'Hub IoT per diventare pienamente operativa dopo l'attivazione del processo di failover può essere espresso usando la seguente funzione.

Tempi di ripristino = RTO [10 min - 2 ore per il failover manuale | 2-26 ore per il failover avviato da Microsoft] + ritardo nella propagazione DNS + Tempo impiegato dall'applicazione client per aggiornare qualsiasi indirizzo IP dell'hub IoT memorizzato nella cache.

> [!IMPORTANT]
> Gli SDK IoT non memorizzano nella cache l'indirizzo IP dell'Hub IoT. Raccomandiamo che il codice utente che si interfaccia con gli SDK non deve memorizzare nella cache l'indirizzo IP dell'Hub IoT.

## <a name="achieve-cross-region-ha"></a>Ottenere disponibilità elevata tra aree

Se gli obiettivi di uptime aziendali non sono soddisfatti dal RTO che forniscono le opzioni di failover manuale o di failover avviato da Microsoft, si dovrebbe prendere in considerazione l'implementazione di un meccanismo di failover automatico tra aree per dispositivo.
L'analisi completa delle topologie di distribuzione nelle soluzioni IoT esula dall'ambito di questo articolo. L'articolo illustra il modello di distribuzione di *failover regionale* per obiettivi di disponibilità elevata e ripristino di emergenza.

In un modello di failover a livello di area il back-end della soluzione viene eseguito principalmente nella località di un data center. Un hub IoT e un back-end secondari vengono distribuiti in un'altra località di data center. Se l'Hub IoT nella regione principale subisce un'interruzione o la connettività di rete dal dispositivo all'area principale viene interrotta, i dispositivi usano un endpoint del servizio secondario. È possibile migliorare la disponibilità della soluzione implementando un modello di failover tra aree invece di usare una sola area. 

In generale, per implementare un modello di failover regionale con l'Hub IoT è necessario quanto segue:

* **Un hub IoT secondario e per la logica di routing del dispositivo**: Se il servizio nell'area primaria viene interrotto, i dispositivi devono avviare la connessione all'area secondaria. Data la condizione con riconoscimento dello stato della maggior parte dei servizi coinvolti, gli amministratori delle soluzioni attivano comunemente il processo di failover tra aree. Il modo migliore per comunicare il nuovo endpoint ai dispositivi, mantenendo il controllo del processo, consiste nel fare in modo che controllino regolarmente un servizio *concierge* per verificare la disponibilità dell'endpoint attualmente attivo. Il servizio concierge può essere un'applicazione Web replicata e mantenuta raggiungibile con tecniche di reindirizzamento DNS, ad esempio con [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md).

   > [!NOTE]
   > Il servizio Hub IoT non è un tipo di endpoint supportato in Gestione traffico di Azure. La raccomandazione è quella di integrare il servizio di concierge proposto con il gestore del traffico di Azure, implementando l'API della sonda di endpoint.

* **Replica del registro delle identità**: Per poter essere usato, l'hub IoT secondario deve contenere tutte le identità dei dispositivi in grado di connettersi alla soluzione. La soluzione deve mantenere backup con replica geografica delle identità dei dispositivi e caricarli nell'hub IoT secondario prima del passaggio all'endpoint attivo per i dispositivi. La funzionalità di esportazione delle identità dei dispositivi dell'hub IoT è utile in questo contesto. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT: registro delle identità](iot-hub-devguide-identity-registry.md).

* **Logica di unione**: Quando l'area primaria diventa nuovamente disponibile, tutto lo stato e i dati che sono stati creati nel sito secondario devono essere migrati nell'area primaria. Lo stato e i dati sono per lo più correlati alle identità dei dispositivi e ai metadati dell'applicazione, che devono essere uniti all'hub IoT primario e agli altri archivi specifici dell'applicazione nell'area primaria. 

Per semplificare questo passaggio è consigliabile usare operazioni idempotenti. Le operazioni idempotenti riducono al minimo gli effetti collaterali della distribuzione coerente degli eventi e dei duplicati o del recapito non ordinato degli eventi. La logica dell'applicazione deve inoltre essere progettata per tollerare eventuali incoerenze o uno stato "leggermente" obsoleto. Questa situazione può verificarsi a causa del tempo aggiuntivo necessario per il recupero dell'integrità del sistema in base agli obiettivi del punto di ripristino (RPO).

## <a name="choose-the-right-hadr-option"></a>Scegliere l'opzione disponibilità elevata o ripristino di emergenza corretta

Ecco un riepilogo delle opzioni di disponibilità elevata e ripristino di emergenza presentate in questo articolo, utilizzabili come riferimento per scegliere l'opzione corretta per la soluzione.

| Opzione a disponibilità elevata e ripristino di emergenza | RTO | RPO | È necessario un intervento manuale? | Complessità dell'implementazione | Impatto dei costi aggiuntivi|
| --- | --- | --- | --- | --- | --- |
| Failover avviato da Microsoft |2-26 ore|Fare riferimento a tabella RPO precedente|No|Nessuna|Nessuna|
| Failover manuale |10 min-2 ore|Fare riferimento a tabella RPO precedente|Yes|Molto bassa. È sufficiente attivare questa operazione dal portale.|Nessuna|
| Disponibilità elevata fra aree |< 1 min|Dipende dalla frequenza di replica della soluzione disponibilità elevata personalizzata|No|Alto|> 1 x il costo di 1 Hub IoT|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

* [Introduzione agli hub IoT (guida introduttiva)](quickstart-send-telemetry-dotnet.md)
* [Che cos'è l'hub IoT Azure?](about-iot-hub.md)