---
title: "Disponibilità elevata e ripristino di emergenza dell'hub IoT di Azure | Documentazione Microsoft"
description: "Descrive le funzionalità di Azure e dell'hub IoT che permettono di compilare soluzioni Azure IoT a disponibilità elevata con opzioni di ripristino di emergenza."
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
ms.date: 10/13/2017
ms.author: elioda
ms.openlocfilehash: ecc5da8daf0f5c93dffc93798f40507f8eac48be
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Disponibilità elevata e ripristino di emergenza dell'hub IoT
Come servizio di Azure, l'hub IoT offre velocità elevata usando le ridondanze a livello di area di Azure, senza richiedere attività aggiuntive alla soluzione. La piattaforma Microsoft Azure offre anche numerose funzionalità che facilitano la compilazione di soluzioni con funzionalità di ripristino di emergenza o disponibilità tra aree. Per offrire la disponibilità elevata globale tra le aree per dispositivi o utenti, sfruttare le funzionalità di ripristino di emergenza di Azure. L'articolo [Indicazioni tecniche sulla resilienza di Azure](../resiliency/resiliency-technical-guidance.md) descrive le funzionalità integrate in Azure per la continuità aziendale e il ripristino di emergenza. Il documento [Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure][Disaster recovery and high availability for Azure applications] fornisce informazioni sull'architettura nelle strategie per consentire alle applicazioni di Azure di ottenere disponibilità elevata e ripristino di emergenza.

## <a name="azure-iot-hub-dr"></a>Ripristino di emergenza dell'hub IoT di Azure
Oltre alla disponibilità elevata all'interno della stessa area, l'hub IoT implementa meccanismi di failover per il ripristino di emergenza che non richiedono alcun intervento da parte dell'utente. Il ripristino di emergenza dell'hub IoT viene avviato automaticamente e ha un obiettivo del tempo di ripristino di 2-26 ore e gli obiettivi del punto di ripristino (RPO) seguenti:

| Funzionalità | RPO |
| --- | --- |
| Disponibilità del servizio per le operazioni del Registro di sistema e di comunicazione |Possibile perdita di CName |
| Dati sull'identità nel registro delle identità |Perdita di dati da 0 a 5 minuti |
| Messaggi da dispositivo a cloud |Tutti i messaggi non letti vengono persi |
| Messaggi di monitoraggio delle operazioni |Tutti i messaggi non letti vengono persi |
| Messaggi da cloud a dispositivo |Perdita di dati da 0 a 5 minuti |
| Coda di commenti da cloud a dispositivo |Tutti i messaggi non letti vengono persi |
| Dati del dispositivo gemello |Perdita di dati da 0 a 5 minuti |
| Processi padre e dispositivo |Perdita di dati da 0 a 5 minuti |

## <a name="regional-failover-with-iot-hub"></a>Failover di area con l'hub IoT
L'analisi completa delle topologie di distribuzione nelle soluzioni IoT esula dall'ambito di questo articolo. L'articolo illustra il modello di distribuzione di *failover regionale* per obiettivi di disponibilità elevata e ripristino di emergenza.

In un modello di failover a livello di area il back-end della soluzione viene eseguito principalmente nella località di un data center. Un hub IoT e un back-end secondari vengono distribuiti in un'altra località di data center. Se l'hub IoT nel data center principale subisce un'interruzione o viene interrotta la connettività di rete dal dispositivo al data center principale, i dispositivi usano un endpoint di servizio secondario. È possibile migliorare la disponibilità della soluzione implementando un modello di failover tra aree invece di usare una sola area. 

In generale, per implementare un modello di failover regionale con l'hub IoT è necessario quanto segue:

* **Un hub IoT secondario e una logica di routing del dispositivo**: se il servizio nell'area primaria viene interrotto, i dispositivi devono avviare la connessione all'area secondaria. Data la condizione con riconoscimento dello stato della maggior parte dei servizi coinvolti, gli amministratori delle soluzioni attivano comunemente il processo di failover tra aree. Il modo migliore per comunicare il nuovo endpoint ai dispositivi, mantenendo il controllo del processo, consiste nel fare in modo che controllino regolarmente un servizio *concierge* per verificare la disponibilità dell'endpoint attualmente attivo. Il servizio concierge può essere un'applicazione Web replicata e mantenuta raggiungibile con tecniche di reindirizzamento DNS, ad esempio con [Gestione traffico di Azure][Azure Traffic Manager].
* **Replica del registro delle identità**: per poter essere usato, l'hub IoT secondario deve contenere tutte le identità dei dispositivi che possono connettersi alla soluzione. La soluzione deve mantenere backup con replica geografica delle identità dei dispositivi e caricarli nell'hub IoT secondario prima del passaggio all'endpoint attivo per i dispositivi. La funzionalità di esportazione delle identità dei dispositivi dell'hub IoT è utile in questo contesto. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT: registro delle identità][IoT Hub developer guide - identity registry].
* **Logica di unione**: quando un'area primaria diventa di nuovo disponibile, deve essere eseguita la migrazione di tutti i dati e dello stato creati nel sito secondario all'area primaria. Lo stato e i dati sono per lo più correlati alle identità dei dispositivi e ai metadati dell'applicazione, che devono essere uniti all'hub IoT primario e agli altri archivi specifici dell'applicazione nell'area primaria. Per semplificare questo passaggio è consigliabile usare operazioni idempotenti. Le operazioni idempotenti riducono al minimo gli effetti collaterali della distribuzione coerente degli eventi e dei duplicati o del recapito non ordinato degli eventi. La logica dell'applicazione deve inoltre essere progettata per tollerare eventuali incoerenze o uno stato "leggermente" obsoleto. Questa situazione può verificarsi a causa del tempo aggiuntivo necessario per il recupero dell'integrità del sistema in base agli obiettivi del punto di ripristino (RPO).

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
