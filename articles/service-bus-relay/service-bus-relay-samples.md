---
title: Panoramica degli esempi di Inoltro del bus di servizio di Azure | Documentazione Microsoft
description: Suddivide gli esempi di inoltro del bus di servizio in categorie e offre una descrizione e un collegamento a ognuno di essi.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9237a9a2-f126-4d3f-9f9b-604ee6b32153
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/13/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 6027c973f0104fff9705a70f7812f62562165c1e
ms.openlocfilehash: d830770c49caeacf762382ce1c6113d9196a5994
ms.lasthandoff: 02/14/2017


---
# <a name="service-bus-relay-samples"></a>Esempi di inoltro del bus di servizio
Gli esempi di inoltro del bus di servizio illustrano le funzionalità chiave del servizio di [inoltro del bus di servizio](https://azure.microsoft.com/services/service-bus/). Questo articolo suddivide gli esempi disponibili in categorie e fornisce una descrizione e un collegamento a ognuno di essi.

> [!NOTE]
> Gli esempi relativi al bus di servizio non vengono installati con Azure SDK. Per ottenere gli esempi, visitare la pagina relativa agli [esempi di Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
> 
> Inoltre è disponibile un insieme aggiornato di esempi di Inoltro del bus di servizio [qui](https://github.com/Azure-Samples/azure-servicebus-relay-samples).  
> 
> 

Per esempi relativi alla messaggistica del bus di servizio, vedere [Esempi di messaggistica del bus di servizio](../service-bus-messaging/service-bus-samples.md).

## <a name="azure-service-bus-relay"></a>Inoltro del bus di servizio di Azure
Gli esempi seguenti illustrano come scrivere applicazioni che usano il servizio di inoltro di Azure.

Tenere presente che per gli esempi di inoltro è necessaria una stringa di connessione per l'accesso allo spazio dei nomi di inoltro.

### <a name="to-obtain-a-connection-string-for-azure-relay"></a>Per ottenere una stringa di connessione per il servizio di inoltro Azure
1. Accedere al [portale di Azure](http://portal.azure.com).
2. Fare clic per espandere l'elenco degli spazi dei nomi nel riquadro **Tutte le risorse**.
3. Fare clic sul nome dello spazio dei nomi di inoltro nell'elenco.
4. Nel pannello dello spazio dei nomi fare clic su **Criteri di accesso condivisi**.
5. Nel pannello **Criteri di accesso condivisi** fare clic su **RootManageSharedAccessKey**.
6. Copiare la stringa di connessione negli Appunti.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Per ottenere una stringa di connessione per il bus di servizio di Windows Server
1. Eseguire il cmdlet di PowerShell seguente:
   
    ```powershell
    get-sbClientConfiguration
    ```
2. Incollare la stringa di connessione nel file App.config relativo all'esempio.

## <a name="azure-relay"></a>Servizio di inoltro di Azure
Esempi che illustrano il servizio di inoltro di Azure.

### <a name="getting-started"></a>Introduzione
| Nome esempio | Descrizione | Versione minima SDK | Disponibilità |
| --- | --- | --- | --- |
| [Messaggistica inoltrata con WCF: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3) |Illustra come eseguire un client e un servizio del bus di servizio di Microsoft Azure in Azure. Nell'esempio il bus di servizio viene configurato a livello di codice. Nei file di configurazione sono archiviate solo le informazioni relative alla sicurezza e all'ambiente. |1.8 |Bus di servizio di Microsoft Azure |
| [Autenticazione della messaggistica inoltrata con WCF: Segreto condiviso](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02) |Illustra come usare un nome dell'autorità di certificazione e un segreto dell'autorità di certificazione per eseguire l'autenticazione con il bus di servizio. |1.8 |Bus di servizio di Microsoft Azure |
| [Autenticazione della messaggistica inoltrata con WCF: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831) |Illustra come esporre un servizio HTTP che non richiede l'autenticazione utente del client. |1.8 |Bus di servizio di Microsoft Azure |
| [Binding della messaggistica inoltrata con WCF: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0) |Illustra come usare il binding **WebHttpRelayBinding** per restituire dati binari con il modello di programmazione Web. |1.8 |Bus di servizio di Microsoft Azure |
| [Binding della messaggistica inoltrata con WCF: Connessione inoltrata NetTcp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692) |Illustra come usare il binding **NetTcpRelayBinding** . |1.8 |Bus di servizio di Microsoft Azure |

### <a name="exploring-features"></a>Esplorazione delle funzionalità
Esempi che illustrano varie funzionalità dell'inoltro del bus di servizio.

| Nome esempio | Descrizione | Versione minima SDK | Disponibilità |
| --- | --- | --- | --- |
| [Autenticazione della messaggistica inoltrata con WCF: WebToken semplice](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392) |Illustra come usare una credenziale token Web semplice per eseguire l'autenticazione con il bus di servizio. Questo esempio è simile all'esempio per Echo, fatta eccezione per alcune modifiche. In particolare, questo esempio aggiunge un comportamento alle applicazioni ServiceHost (servizio) e ChannelFactory (client). |1.8 |Bus di servizio di Microsoft Azure |
| [Messaggistica inoltrata con WCF: Bilanciamento del carico](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8) |Illustra come usare il bus di servizio di Microsoft Azure per instradare messaggi a più ricevitori. Vengono descritte più istanze di un servizio semplice che comunicano con un client usando il binding **NetTcpRelayBinding** . |1.8 |Bus di servizio di Microsoft Azure |
| [Binding della messaggistica inoltrata con WCF: Net Event](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977) |Illustra l'uso del binding **NetEventRelayBinding** nel bus di servizio di Microsoft Azure. |1.8 |Bus di servizio di Microsoft Azure |
| [Binding della messaggistica inoltrata con WCF: Sessione WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb) |Illustra l'uso del binding **WS2007HttpRelayBinding** con le sessioni affidabili abilitate. Mostra anche come specificare credenziali del bus di servizio nel file di configurazione anziché a livello di codice. |1.8 |Bus di servizio di Microsoft Azure |
| [Binding della messaggistica inoltrata con WCF: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5) |Illustra come usare il binding **WS2007HttpRelayBinding** con la sicurezza dei messaggi, per proteggere i messaggi end-to-end richiedendo al tempo stesso ai client di eseguire l'autenticazione con il bus di servizio. |1.8 |Bus di servizio di Microsoft Azure |
| [Messaggistica inoltrata con WCF: Scambio di metadati](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e) |Illustra come esporre un endpoint di metadati che usa il binding di inoltro. **MetadataExchange** è supportato nei binding di inoltro seguenti: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding** e **WS2007HttpRelayBinding**. |1.8 |Bus di servizio di Microsoft Azure |
| [Binding della messaggistica inoltrata WCF: Connessione diretta NetTcp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161) |Illustra come configurare il binding **NetTcpRelayBinding** per supportare la modalità di connessione **ibrida**, che stabilisce prima una connessione inoltrata e, se possibile, passa automaticamente a una connessione diretta tra un client e un servizio. |1.8 |Bus di servizio di Microsoft Azure |
| [Binding della messaggistica inoltrata con WCF: NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392) |Illustra come usare il binding **NetTcpRelayBinding** con la sicurezza dei messaggi. |1.8 |Bus di servizio di Microsoft Azure |
| [Binding della messaggistica inoltrata con WCF: Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a) |Illustra come esporre e consumare un endpoint di servizio usando il binding **NetOnewayRelayBinding** . |1.8 |Bus di servizio di Microsoft Azure |
| [Binding della messaggistica inoltrata con WCF: WS2007Http semplice](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a) |Illustra l'uso del binding **WS2007HttpRelayBinding** . Descrive un servizio semplice che non usa opzioni di sicurezza e non richiede l'autenticazione da parte dei client. |1.8 |Bus di servizio di Microsoft Azure |

## <a name="next-steps"></a>Passaggi successivi
Per altre panoramiche sul bus di servizio, vedere gli argomenti seguenti.

* [Panoramica del servizio di inoltro di Azure](relay-what-is-it.md)
* [Architettura del bus di servizio](../service-bus-messaging/service-bus-architecture.md)
* [Dati fondamentali del bus di servizio](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)


