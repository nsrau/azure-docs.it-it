---
title: "Disponibilità elevata e ripristino di emergenza dell&quot;hub IoT di Azure | Documentazione Microsoft"
description: "Descrive le funzionalità di Azure e dell&quot;hub IoT che permettono di compilare soluzioni Azure IoT a disponibilità elevata con opzioni di ripristino di emergenza."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: ae320e58-aa20-45b9-abdc-fa4faae8e6dd
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 76c3187549e1821908263c30e394db26ee6f75e6


---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Disponibilità elevata e ripristino di emergenza dell'hub IoT
Come servizio di Azure, l'hub IoT offre velocità elevata usando le ridondanze a livello di area di Azure, senza richiedere attività aggiuntive alla soluzione. La piattaforma Microsoft Azure offre anche numerose funzionalità che facilitano la compilazione di soluzioni con funzionalità di ripristino di emergenza o disponibilità tra aree. Per offrire la disponibilità elevata globale tra le aree per dispositivi o utenti, progettare e preparare le soluzioni in modo da sfruttare le funzionalità di ripristino di emergenza di Azure. L'articolo [Indicazioni tecniche sulla resilienza di Azure](../resiliency/resiliency-technical-guidance.md) descrive le funzionalità integrate in Azure per la continuità aziendale e il ripristino di emergenza. Il documento [Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure][Disaster recovery and high availability for Azure applications] fornisce informazioni sull'architettura nelle strategie per consentire alle applicazioni di Azure di ottenere disponibilità elevata e ripristino di emergenza.

## <a name="azure-iot-hub-dr"></a>Ripristino di emergenza dell'hub IoT di Azure
Oltre alla disponibilità elevata all'interno della stessa area, l'hub IoT implementa meccanismi di failover per il ripristino di emergenza che non richiedono alcun intervento da parte dell'utente. Il ripristino di emergenza dell'hub IoT viene avviato automaticamente e ha un obiettivo del tempo di ripristino (RTO) di 2-26 ore e obiettivi del punto di ripristino (RPO) successivi.

| Funzionalità | RPO |
| --- | --- |
| Disponibilità del servizio per le operazioni del Registro di sistema e di comunicazione |Possibile perdita di CName |
| Dati sull'identità nel registro delle identità |Perdita di dati da&0; a&5; minuti |
| Messaggi da dispositivo a cloud |Tutti i messaggi non letti vengono persi |
| Messaggi di monitoraggio delle operazioni |Tutti i messaggi non letti vengono persi |
| Messaggi da cloud a dispositivo |Perdita di dati da&0; a&5; minuti |
| Coda di commenti da cloud a dispositivo |Tutti i messaggi non letti vengono persi |

## <a name="regional-failover-with-iot-hub"></a>Failover di area con l'hub IoT
L'analisi completa delle topologie di distribuzione nelle soluzioni IoT esula dall'ambito di questo articolo. L'articolo illustra il modello di distribuzione di *failover regionale* per obiettivi di disponibilità elevata e ripristino di emergenza.

In un modello di failover regionale, il back-end della soluzione viene eseguito principalmente nella località di un data center, mentre un hub IoT e un back-end secondari vengono distribuiti nella località di un altro data center. Se l'hub IoT nel data center principale subisce un'interruzione o viene interrotta la connettività di rete dal dispositivo al data center principale, i dispositivi usano un endpoint di servizio secondario quando il gateway primario non è raggiungibile. Con una funzionalità di failover tra aree, la disponibilità della soluzione può essere migliorata al di là della disponibilità elevata di una singola area.

In generale, per implementare un modello di failover regionale con l'hub IoT è necessario quanto segue:

* **Un hub IoT secondario e una logica di routing del dispositivo**: in caso di interruzione del servizio nell'area primaria, i dispositivi devono avviare la connessione all'area secondaria. Data la condizione con riconoscimento dello stato della maggior parte dei servizi coinvolti, gli amministratori delle soluzioni attivano comunemente il processo di failover tra aree. Il modo migliore per comunicare il nuovo endpoint ai dispositivi, mantenendo il controllo del processo, consiste nel fare in modo che controllino regolarmente un servizio *concierge* per verificare la disponibilità dell'endpoint attualmente attivo. Il servizio concierge può essere un'applicazione Web replicata e mantenuta raggiungibile con tecniche di reindirizzamento DNS, ad esempio con [Gestione traffico di Azure][Azure Traffic Manager].
* **Replica del registro delle identità**: per poter essere usato, l'hub IoT secondario deve contenere tutte le identità dei dispositivi che possono connettersi alla soluzione. La soluzione deve mantenere backup con replica geografica delle identità dei dispositivi e caricarli nell'hub IoT secondario prima del passaggio all'endpoint attivo per i dispositivi. La funzionalità di esportazione delle identità dei dispositivi dell'hub IoT è utile in questo contesto. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT: registro delle identità][IoT Hub developer guide - identity registry].
* **Logica di unione** : quando un'area primaria diventa di nuovo disponibile, deve essere eseguita la migrazione di tutti i dati e dello stato creati nel sito secondario all'area primaria. Lo stato e i dati sono per lo più correlati alle identità dei dispositivi e ai metadati dell'applicazione, che devono essere uniti all'hub IoT primario e agli altri archivi specifici dell'applicazione nell'area primaria. Per semplificare questo passaggio è consigliabile usare operazioni idempotenti. Le operazioni idempotenti riducono al minimo gli effetti collaterali della distribuzione coerente degli eventi e dei duplicati o del recapito non ordinato degli eventi. La logica dell'applicazione deve essere progettata per tollerare eventuali incoerenze o uno stato "leggermente" obsoleto. Questa situazione può verificarsi a causa del tempo aggiuntivo necessario per il recupero dell'integrità del sistema in base agli obiettivi del punto di ripristino (RPO).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

* [Introduzione agli hub IoT (esercitazione)][lnk-get-started]
* [Che cos'è l'hub IoT di Azure?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md



<!--HONumber=Jan17_HO4-->


