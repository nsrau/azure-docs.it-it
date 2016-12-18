---
title: Panoramica degli esempi di messaggistica del bus di servizio | Documentazione Microsoft
description: Suddivide gli esempi relativi alla messaggistica del bus di servizio in categorie e fornisce una descrizione e un collegamento a ognuno di essi.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0b420343-2d2a-4c65-98f1-ee0e39ef55c8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 719403ca2ef90cdcf5f76fe15f7a33095e71a74c


---
# <a name="service-bus-messaging-samples"></a>Esempi di messaggistica del bus di servizio
Gli esempi relativi alla messaggistica del bus di servizio illustrano come usare le funzionalità principali di [messaggistica del bus di servizio](https://azure.microsoft.com/services/service-bus/) (servizio cloud) e del [bus di servizio per Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). Questo articolo suddivide gli esempi disponibili in categorie e fornisce una descrizione e un collegamento a ognuno di essi.

> [!NOTE]
> Gli esempi relativi al bus di servizio non vengono installati con l'SDK. Per ottenere gli esempi, visitare la pagina relativa agli [esempi di Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
> 
> In aggiunta, [qui](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) è disponibile un set aggiornato di esempi di messaggi del bus di servizio (in questo articolo non viene descritta la scrittura degli stessi).  
> 
> 

Per esempi di inoltro, vedere [Esempi di inoltro del bus di servizio](../service-bus-relay/service-bus-relay-samples.md).

## <a name="service-bus-messaging"></a>Messaggistica del bus di servizio
Gli esempi seguenti illustrano come scrivere applicazioni che usano la messaggistica del bus di servizio.

Tenere presente che per gli esempi relativi alla messaggistica è necessaria una stringa di connessione per l'accesso allo spazio dei nomi del bus di servizio.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Per ottenere una stringa di connessione per il bus di servizio di Azure
1. Accedere al [portale di Azure](http://portal.azure.com).
2. Nella colonna sinistra fare clic su **Bus di servizio**.
3. Fare clic sul nome dello spazio dei nomi nell'elenco.
4. Nel pannello dello spazio dei nomi fare clic su **Criteri di accesso condivisi**.
5. Nel pannello **Criteri di accesso condivisi** fare clic su **RootManageSharedAccessKey**.
6. Copiare la stringa di connessione negli Appunti.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Per ottenere una stringa di connessione per il bus di servizio di Windows Server
1. Eseguire il cmdlet di PowerShell seguente:
   
    ```
    get-sbClientConfiguration
    ```
2. Incollare la stringa di connessione nel file App.config relativo all'esempio.

### <a name="getting-started-samples"></a>Per iniziare
Questi esempi illustrano la funzionalità di messaggistica di base.

| Nome esempio | Descrizione | Versione minima SDK | Disponibilità |
| --- | --- | --- | --- |
| [Introduzione: Messaggistica con code](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3) |Illustra come usare il bus di servizio di Microsoft Azure per inviare e ricevere messaggi da una coda. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Getting Started: Messaging With Topics](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5) |Illustra come usare il bus di servizio di Microsoft Azure per inviare e ricevere messaggi da un argomento con più sottoscrizioni. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |

### <a name="exploring-features"></a>Esplorazione delle funzionalità
Gli esempi seguenti illustrano varie funzionalità del bus di servizio.

| Nome esempio | Descrizione | Versione minima SDK | Disponibilità |
| --- | --- | --- | --- |
| [Provider di token HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5) |Illustra le varie modalità di autenticazione di un client HTTP/REST con il bus di servizio. |2.1 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Client HTTP del bus di servizio](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a) |Illustra come inviare e ricevere messaggi dal bus di servizio tramite HTTP/REST. |2.3 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Inoltro automatico del bus di servizio](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b) |Illustra come inoltrare automaticamente messaggi da una coda, una sottoscrizione o una coda di messaggi non recapitabili a un'altra coda o argomento. Illustra anche come inviare un messaggio a una coda o a un argomento tramite una coda di trasferimento. |2.3 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Esempio di sessione con canali WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451) |Illustra come usare il bus di servizio di Microsoft Azure tramite canali Windows Communication Foundation (WCF). L'esempio mostra l'uso di canali WCF per inviare e ricevere messaggi tramite una coda del bus di servizio. Illustra sia la comunicazione basata su sessione sia la comunicazione non basata su sessione attraverso il bus di servizio. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Transazioni](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e) |Illustra come usare le funzionalità di messaggistica del bus di servizio di Microsoft Azure nell'ambito di una transazione per garantire che venga eseguito in modo atomico il commit di batch di operazioni di messaggistica. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Operazioni di gestione tramite REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88) |Illustra come eseguire operazioni di gestione nel bus di servizio tramite REST. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [API REST del provider di risorse](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203) |Illustra come usare le nuove API REST RDFE del bus di servizio per gestire spazi dei nomi ed entità di messaggistica. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Esempio di sessione con il servizio WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2) |Illustra come usare il bus di servizio di Microsoft Azure con il modello di servizio WCF. L'esempio mostra l'uso del modello di servizio WCF per la comunicazione basata su sessione tramite una coda del bus di servizio. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Richiesta-risposta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8) |Illustra come usare il bus di servizio di Microsoft Azure e la funzionalità di richiesta-risposta. L'esempio mostra client e server semplici che comunicano tramite una coda del bus di servizio. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Coda di messaggi non recapitabili](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8) |Illustra come usare il bus di servizio di Microsoft Azure e la funzionalità di messaggistica "coda di messaggi non recapitabili". L'esempio mostra un mittente e un ricevitore semplici che comunicano tramite una coda del bus di servizio. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Messaggi rinviati](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879) |Illustra come usare la funzionalità di rinvio dei messaggi del bus di servizio di Microsoft Azure. L'esempio mostra un mittente e un ricevitore semplici che comunicano tramite una coda del bus di servizio. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Sessione di messaggistica](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4) |Illustra come usare il bus di servizio di Microsoft Azure e la funzionalità Sessione di messaggistica. L'esempio mostra mittenti e un ricevitori semplici che comunicano tramite una coda del bus di servizio. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Brokered Messaging: Request Response Topic](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e) |Illustra come implementare il modello richiesta-risposta usando gli argomenti e le sottoscrizioni del bus di servizio di Microsoft Azure. L'esempio mostra client e server semplici che comunicano tramite un argomento del bus di servizio. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Coda con modello richiesta-risposta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf) |Illustra come usare il bus di servizio di Microsoft Azure e la funzionalità di richiesta-risposta. L'esempio mostra client e server semplici che comunicano tramite due code del bus di servizio. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Rilevamento di duplicati](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25) |Illustra come usare la funzionalità di rilevamento dei messaggi duplicati del bus di servizio di Microsoft Azure tramite le code. Vengono create due code, una con il rilevamento di duplicati abilitato e l'altra senza rilevamento di duplicati. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Messaggistica asincrona](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74) |Illustra come usare il bus di servizio di Microsoft Azure per inviare e ricevere messaggi in modo asincrono da una coda. La coda fornisce una comunicazione asincrona disaccoppiata tra un mittente e un numero qualsiasi di ricevitori (in questo esempio il ricevitore è uno solo). |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Filtri avanzati](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) |Illustra come usare i filtri avanzati di pubblicazione/sottoscrizione del bus di servizio di Microsoft Azure. Vengono creati un argomento e 3 sottoscrizioni con definizioni di filtro diverse, vengono inviati messaggi all'argomento e vengono ricevuti tutti i messaggi dalle sottoscrizioni. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Messaggistica negoziata: Prelettura di messaggi](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d) |Illustra come usare la funzionalità di prelettura dei messaggi del bus di servizio di Microsoft Azure. Mostra come usare la funzionalità di prelettura dei messaggi alla ricezione. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |

## <a name="service-bus-reference-tools"></a>Strumenti di riferimento del bus di servizio
Gli esempi seguenti illustrano altre funzionalità del servizio.

| Nome esempio | Descrizione | Versione minima SDK | Disponibilità |
| --- | --- | --- | --- |
| [Service Bus Explorer](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a) |Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi servizio del bus di servizio e di gestire le entità di messaggistica in modo semplificato. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione, nonché la possibilità di testare le entità di messaggistica e i servizi di inoltro. |1.8 |Bus di servizio di Microsoft Azure. Bus di servizio per Windows Server |
| [Autorizzazione: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93) |Questo esempio illustra come creare e gestire identità del servizio in Controllo di accesso di Microsoft Azure Active Directory (anche noto come Servizio di controllo di accesso o ACS) per l'uso con il bus di servizio. |N/D |Bus di servizio di Microsoft Azure |

## <a name="next-steps"></a>Passaggi successivi
Per altre panoramiche sul bus di servizio, vedere gli argomenti seguenti.

* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Architettura del bus di servizio](service-bus-architecture.md)
* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)




<!--HONumber=Nov16_HO3-->


