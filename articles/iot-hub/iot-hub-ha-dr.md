<properties
 pageTitle="Hub IoT disponibilità elevata e ripristino di emergenza | Microsoft Azure"
 description="Descrive le funzionalità che permettono di compilare soluzioni IoT a disponibilità elevata con opzioni di ripristino di emergenza."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# Disponibilità elevata e ripristino di emergenza dell'hub IoT

Come servizio di Azure, l'hub IoT offre velocità elevata usando le ridondanze a livello di area di Azure, senza richiedere attività aggiuntive alla soluzione. Azure offre anche numerose funzionalità che facilitano la compilazione di soluzioni con funzionalità di ripristino di emergenza o disponibilità tra aree, se necessario. Le soluzioni devono essere progettate e preparate per sfruttare i vantaggi delle funzionalità di ripristino di emergenza, per poter fornire la disponibilità elevata globale e tra aree a dispositivi o utenti. L'articolo [Indicazioni tecniche sulla resilienza di Azure][] descrive le funzionalità integrate in Azure per la continuità aziendale e il ripristino di emergenza. Il documento [Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure][] fornisce informazioni sull'architettura nelle strategie per permettere alle applicazioni di Azure di ottenere disponibilità elevata e ripristino di emergenza.

## Ripristino di emergenza dell'hub IoT di Azure
Oltre alla disponibilità elevata all'interno della stessa area, l'hub IoT implementa meccanismi di failover per il ripristino di emergenza che non richiedono alcun intervento da parte dell'utente. Il ripristino di emergenza dell'hub IoT viene avviato automaticamente e ha un obiettivo del tempo di ripristino (RTO) di 2-26 ore e obiettivi del punto di ripristino (RPO) successivi.

| Funzionalità | RPO |
| ------------- | --- |
| Disponibilità del servizio per le operazioni del Registro di sistema e di comunicazione | Possibile perdita di CName |
| Dati sull'identità nel registro delle identità dei dispositivi | Perdita di dati da 0 a 5 minuti |
| Messaggi da dispositivo a cloud | Tutti i messaggi non letti vengono persi |
| Messaggi di monitoraggio delle operazioni | Tutti i messaggi non letti vengono persi |
| Messaggi da cloud a dispositivo | Perdita di dati da 0 a 5 minuti |
| Coda di commenti da cloud a dispositivo | Tutti i messaggi non letti vengono persi |

## Failover di area con l'hub IoT

Un approfondimento completo sulle topologie di distribuzione nelle soluzioni IoT non rientra nelle finalità di questo articolo, ma per la disponibilità elevata e il ripristino di emergenza viene considerato il modello di distribuzione di *failover regionale*.

In un modello di failover regionale il back-end della soluzione viene eseguito principalmente in una posizione del data center. Per il failover vengono tuttavia distribuiti un hub IoT e un back-end aggiuntivi in un'altra posizione del data center, nel caso in cui l'hub IoT nel data center primario dovesse subire un'interruzione dell'alimentazione o la connettività di rete dal dispositivo al data center primario dovesse essere interrotta. I dispositivi usano un endpoint di servizio secondario quando il gateway primario non è raggiungibile. Con una funzionalità di failover tra aree, la disponibilità della soluzione può essere migliorata al di là della disponibilità elevata di una singola area.

In generale, per implementare un modello di failover regionale con l'hub IoT è necessario quanto segue.

* **Un hub IoT secondario e una logica di routing del dispositivo**: in caso di interruzione del servizio nell'area primaria, i dispositivi devono avviare la connessione all'area secondaria. Data la condizione con riconoscimento dello stato della maggior parte dei servizi coinvolti, gli amministratori delle soluzioni attivano comunemente il processo di failover tra aree. Il modo migliore per comunicare il nuovo endpoint ai dispositivi, mantenendo il controllo del processo, consiste nel fare in modo che controllino regolarmente un servizio *concierge* per verificare la disponibilità dell'endpoint attualmente attivo. Il servizio concierge può essere una semplice applicazione Web replicata e mantenuta raggiungibile con tecniche di reindirizzamento DNS, ad esempio con [Gestione traffico di Azure][].
* **Replica del registro di identità**: per essere utilizzabile, l'hub IoT secondario deve contenere tutte le identità dei dispositivi che possono connettersi alla soluzione. La soluzione deve mantenere backup con replica geografica delle identità dei dispositivi e caricarli nell'hub IoT secondario prima del passaggio all'endpoint attivo per i dispositivi. La funzionalità di esportazione delle identità dei dispositivi dell'hub IoT è molto utile in questo contesto. Per altre informazioni, vedere la [guida per sviluppatori dell'hub IoT - Registro di identità][].
* **Logica di unione**: quando un'area primaria diventa di nuovo disponibile, deve essere eseguita la migrazione di tutti i dati e dello stato creati nel sito secondario all'area primaria. Queste informazioni sono per lo più correlate alle identità dei dispositivi e ai metadati dell'applicazione, che devono essere uniti all'hub IoT primario egli altri archivi specifici dell'applicazione nell'area primaria. Per semplificare questo passaggio, è di solito consigliabile usare operazioni idempotenti. In questo modo si minimizzano gli effetti collaterali non solo dell'eventuale distribuzione coerente degli eventi, ma anche dei duplicati o del recapito non ordinato degli eventi. La logica dell'applicazione deve essere progettata per tollerare eventuali incoerenze o uno stato "leggermente" obsoleto. Ciò è dovuto al tempo aggiuntivo necessario per l'"integrità" del sistema basata sugli obiettivi del punto di ripristino (RPO).

## Passaggi successivi

Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

- [Introduzione agli hub IoT (esercitazione)][lnk-get-started]
- [Che cos'è l’hub IoT Azure?][]

[Azure resiliency technical guidance]: ../resiliency/resiliency-technical-guidance.md
[Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Failsafe: Guidance for Resilient Cloud Architectures]: https://msdn.microsoft.com/library/azure/jj853352.aspx
[Gestione traffico di Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[guida per sviluppatori dell'hub IoT - Registro di identità]: iot-hub-devguide.md#identityregistry

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Che cos'è l’hub IoT Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0629_2016-->