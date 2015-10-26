<properties
 pageTitle="Hub IoT disponibilità elevata e ripristino di emergenza | Microsoft Azure"
 description="Descrive le funzionalità che consentono di creare soluzioni a disponibilità elevata con opzioni di ripristino di emergenza."
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
 ms.date="10/02/2015"
 ms.author="elioda"/>

# Disponibilità elevata e ripristino di emergenza dell'hub IoT

Come servizio di Azure, l'hub IoT offre velocità elevata usando le ridondanze a livello di area di Azure, senza richiedere attività aggiuntive alla soluzione. Azure offre anche numerose funzionalità che facilitano la compilazione di soluzioni con funzionalità di ripristino di emergenza o disponibilità tra aree, se necessario. Le soluzioni devono essere progettate e preparate per sfruttare i vantaggi di tali funzionalità, in modo da fornire la disponibilità elevata globale e tra aree a dispositivi o utenti. L'articolo [Informazioni tecniche sulla continuità aziendale di Azure][] descrive le funzionalità integrate di Azure per la continuità aziendale e il ripristino di emergenza. Il documento [Ripristino di emergenza e disponibilità elevata per applicazioni Azure][] fornisce informazioni sull'architettura nelle strategie per consentire alle applicazioni di Azure di ottenere disponibilità elevata e ripristino di emergenza (HADR).

## Failover di area con l'hub IoT

Un approfondimento completo sulle tecnologie di distribuzione nelle soluzioni IoT non rientra nell'ambito di questa sezione, ma ai fini della disponibilità elevata e del ripristino di emergenza viene considerato il modello di distribuzione per il *failover di area*.

In un modello di failover di area, il back-end della soluzione viene eseguito principalmente in una determinata posizione del data center. Vengono tuttavia un hub IoT e un back-end aggiuntivi in un'ulteriore area del data center ai fini del failover, qualora l'hub IoT nel data center primario dovesse subire un'interruzione dell'alimentazione o la connettività di rete dal dispositivo al data center primario dovesse interrompersi. I dispositivi usano un endpoint di servizio secondario quando il gateway primario non è raggiungibile. Con una funzionalità di failover tra aree, la disponibilità della soluzione può essere migliorata al di là della disponibilità elevata di una singola area.

Ad alto livello, per implementare un modello di failover di area con l'hub IoT, sono necessari gli elementi seguenti.

* **Un hub IoT secondario e una logica di routing del dispositivo**: in caso di interruzione del servizio nell'area primaria, i dispositivi devono avviare la connessione all'area secondaria. Data la condizione con riconoscimento dello stato della maggior parte dei servizi coinvolti, gli amministratori delle soluzioni attivano comunemente il processo di failover tra aree. Il modo migliore per comunicare il nuovo endpoint ai dispositivi, mantenendo il controllo del processo, consiste nel fare in modo che controllino regolarmente un servizio *concierge* per verificare la disponibilità dell'endpoint attualmente attivo. Il servizio concierge può essere una semplice applicazione Web replicata e mantenuta raggiungibile con tecniche di reindirizzamento DNS, ad esempio con [Gestione traffico di Azure][].
* **Replica del registro di identità**: per essere utilizzabile, l'hub IoT secondario deve contenere tutte le identità dei dispositivi che possono connettersi alla soluzione. La soluzione deve mantenere backup con replica geografica delle identità dei dispositivi e caricarli nell'hub IoT secondario prima del passaggio all'endpoint attivo per i dispositivi. La funzionalità di esportazione delle identità dei dispositivi dell'hub IoT è molto utile in questo contesto. Per altre informazioni, vedere la [guida per sviluppatori dell'hub IoT - Registro di identità][].
* **Logica di unione**: quando un'area primaria diventa di nuovo disponibile, deve essere eseguita la migrazione di tutti i dati e dello stato creati nel sito secondario all'area primaria. Queste informazioni sono per lo più correlate alle identità dei dispositivi e ai metadati dell'applicazione, che dovranno essere uniti all'hub IoT primario e probabilmente ad altri archivi specifici dell'applicazione nell'area primaria. Per semplificare questo passaggio, è di solito consigliabile usare operazioni idempotenti. In questo modo si minimizzano gli effetti collaterali non solo dell'eventuale distribuzione coerente degli eventi, ma anche dei duplicati o del recapito non ordinato degli eventi. Inoltre, la logica dell'applicazione deve essere progettata per essere in grado di tollerare eventuali incoerenze o uno stato "leggermente" obsoleto. Ciò è dovuto al tempo aggiuntivo necessario per l'"integrità" del sistema basata sugli obiettivi del punto di ripristino (RPO). L'articolo seguente fornisce informazioni più dettagliate su questo argomento: [Operatore alternativo: informazioni aggiuntive per architetture cloud resilienti][].

## Passaggi successivi

Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

- [Introduzione agli hub IoT (esercitazione)][lnk-get-started]
- [Che cos'è l’hub IoT Azure?][]

[Informazioni tecniche sulla continuità aziendale di Azure]: https://msdn.microsoft.com/library/azure/hh873027.aspx
[Ripristino di emergenza e disponibilità elevata per applicazioni Azure]: https://msdn.microsoft.com/library/azure/dn251004.aspx
[Operatore alternativo: informazioni aggiuntive per architetture cloud resilienti]: https://msdn.microsoft.com/library/azure/jj853352.aspx
[Gestione traffico di Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[guida per sviluppatori dell'hub IoT - Registro di identità]: iot-hub-devguide.md#identityregistry

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Che cos'è l’hub IoT Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO3-->