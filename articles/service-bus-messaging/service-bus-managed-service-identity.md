---
title: "Identità del servizio gestito con il bus di servizio di Azure (anteprima) | Microsoft Docs"
description: "Usare le identità del servizio gestito con il bus di servizio di Azure"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 6965e80cf10b732d4d0a8fb78447f188c133979d
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="managed-service-identity-preview"></a>Identità del servizio gestito (anteprima)

Un'identità del servizio gestito (MSI, Managed Service Identity) è una funzionalità di Azure che consente di creare un'identità sicura associata alla distribuzione in cui viene eseguito il codice dell'applicazione. È quindi possibile associare l'identità ai ruoli di controllo di accesso che concedono autorizzazioni personalizzate per l'accesso a risorse di Azure specifiche necessarie per l'applicazione.

Con l'identità del servizio gestito, la piattaforma Azure gestisce questa identità di runtime. Non è necessario archiviare e proteggere le chiavi di accesso nel codice o nella configurazione dell'applicazione, né per l'identità stessa, né per le risorse a cui è necessario accedere. Per un'app client del bus di servizio in esecuzione in un'applicazione di Servizio app di Azure o in una macchina virtuale con il supporto per l'identità del servizio gestito abilitato, non è necessario gestire le chiavi e le regole di firma di accesso condiviso o altri token di accesso. L'applicazione client necessita solo dell'indirizzo dell'endpoint dello spazio dei nomi della messaggistica del bus di servizio. Quando l'app si connette, il bus di servizio associa il contesto dell'identità del servizio gestito al client in un'operazione che viene illustrata in un esempio riportato più avanti in questo articolo. 

Una volta eseguita l'associazione a un'identità del servizio gestito, un client del bus di servizio può eseguire tutte le operazioni autorizzate. L'autorizzazione viene concessa associando un'identità del servizio gestito ai ruoli del bus di servizio. 

## <a name="service-bus-roles-and-permissions"></a>Ruoli e autorizzazioni del bus di servizio

Per la versione di anteprima pubblica iniziale, è possibile aggiungere un'identità del servizio gestito solo al ruolo "Proprietario" o "Collaboratore" di uno spazio dei nomi del bus di servizio, concedendo così all'identità il controllo completo su tutte le entità nello spazio dei nomi. Le operazioni di gestione che modificano la topologia dello spazio dei nomi sono tuttavia inizialmente supportate solo tramite Azure Resource Manager e non tramite l'interfaccia di gestione REST nativa del bus di servizio. Questo supporto significa anche che non è possibile usare l'oggetto [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) del client .NET Framework in un'identità del servizio gestito.

## <a name="use-service-bus-with-a-managed-service-identity"></a>Usare il bus di servizio con un'identità del servizio gestito

La sezione seguente descrive i passaggi necessari per creare e distribuire un'applicazione di esempio che viene eseguita con un'identità del servizio gestito, come concedere all'identità l'accesso a uno spazio dei nomi della messaggistica del bus di servizio e il modo in cui l'applicazione interagisce con le entità del bus di servizio usando tale identità.

Questa introduzione descrive un'applicazione Web ospitata in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/). I passaggi necessari per un'applicazione ospitata in una macchina virtuale sono simili.

### <a name="create-an-app-service-web-application"></a>Creare un'applicazione Web del servizio app

Il primo passaggio consiste nel creare un'applicazione ASP.NET del servizio app. Se non si ha familiarità con le procedure per eseguire questa operazione in Azure, seguire [questa guida pratica](../app-service/app-service-web-get-started-dotnet-framework.md). Invece di creare un'applicazione MVC come illustrato nell'esercitazione, creare però un'applicazione Web Form.

### <a name="set-up-the-managed-service-identity"></a>Configurare l'identità del servizio gestito

Una volta creata l'applicazione, passare all'app Web appena creata nel portale di Azure (come illustrato anche nella guida pratica) e quindi passare alla pagina **Identità del servizio gestito** e abilitare la funzionalità: 

![](./media/service-bus-managed-service-identity/msi1.png)

Una volta abilitata la funzionalità, una nuova identità del servizio viene creata in Azure Active Directory e configurata nell'host del servizio app.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Creare un nuovo spazio dei nomi della messaggistica del bus di servizio

Successivamente, [creare uno spazio dei nomi della messaggistica del bus di servizio](service-bus-create-namespace-portal.md) in una delle aree di Azure con supporto di anteprima per Controllo degli accessi in base al ruolo: **Stati Uniti orientali**, **Stati Uniti orientali 2** o **Europa occidentale**. 

Passare alla pagina **Controllo di accesso (IAM)** dello spazio dei nomi nel portale e quindi fare clic su **Aggiungi** per aggiungere l'identità del servizio gestito al ruolo **Proprietario**. A tale scopo, cercare il nome dell'applicazione Web nel campo **Seleziona** del pannello **Aggiungi autorizzazioni** e quindi fare clic sulla voce. Fare quindi clic su **Salva**.

![](./media/service-bus-managed-service-identity/msi2.png)
 
L'identità del servizio gestito dell'applicazione Web dispone ora dell'accesso allo spazio dei nomi del bus di servizio e alla coda creata in precedenza. 

### <a name="run-the-app"></a>Esecuzione dell'app

Modificare ora la pagina predefinita dell'applicazione ASP.NET creata. È anche possibile usare il codice dell'applicazione Web di [questo repository GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ManagedServiceIdentity). 

La pagina Default.aspx è la pagina di destinazione. Il codice è disponibile nel file Default.aspx.cs. Il risultato è un'applicazione Web minima con pochi campi di immissione e con pulsanti di **invio** e **ricezione** che consentono la connessione al bus di servizio per l'invio o la ricezione di messaggi.

Si noti come viene inizializzato l'oggetto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory). Invece di usare il provider di token di firma di accesso condiviso (SAS), il codice crea un provider di token per l'identità del servizio gestito con la chiamata di `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)`. In questo modo, non ci sono segreti da conservare e usare. Il flusso del contesto dell'identità del servizio gestito nel bus di servizio e l'handshake di autorizzazione vengono gestiti automaticamente dal provider di token, offrendo così un modello più semplice rispetto all'uso della firma di accesso condiviso.

Una volta apportate queste modifiche, pubblicare ed eseguire l'applicazione. Un modo semplice per ottenere i dati di pubblicazione corretti consiste nello scaricare e quindi importare un profilo di pubblicazione in Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Per inviare o ricevere messaggi, immettere il nome dello spazio dei nomi e il nome dell'entità creata, quindi fare clic su **Invia** o **Ricevi**. 
 
Si noti che l'identità del servizio gestito funziona solo all'interno dell'ambiente Azure e solo nella distribuzione del servizio app in cui è stata configurata. Si noti anche che, al momento, le identità del servizio gestito non funzionano con gli slot di distribuzione del servizio app.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)