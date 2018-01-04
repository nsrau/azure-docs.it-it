---
title: "Identità del servizio gestito con anteprima di Azure Service Bus | Documenti Microsoft"
description: "Utilizzare le identità del servizio gestito con il Bus di servizio di Azure"
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="managed-service-identity-preview"></a>Identità del servizio gestito (anteprima)

Un'identità di servizio gestiti (MSI) è una funzionalità tra Azure che consente di creare un'identità di protezione associata alla distribuzione in cui viene eseguito il codice dell'applicazione. È quindi possibile associare l'identità con i ruoli di controllo di accesso che concedono autorizzazioni personalizzate per l'accesso alle risorse Azure specifiche necessarie all'applicazione.

Con estensione MSI, la piattaforma Azure gestisce questa identità di runtime. Non è necessario archiviare e proteggere le chiavi di accesso nel codice dell'applicazione o la configurazione, per l'identità di se stesso o per le risorse che necessarie per accedere. Un'app client Bus di servizio in esecuzione all'interno di un'applicazione di servizio App di Azure o in una macchina virtuale con il supporto MSI abilitato non è necessario gestire le regole di firma di accesso condiviso e le chiavi o eventuali altri token di accesso. L'applicazione client deve solo l'indirizzo dell'endpoint dello spazio dei nomi di messaggistica del Bus di servizio. Quando si connette l'app, Bus di servizio si associa il file MSI al client in un'operazione che viene visualizzato in un esempio più avanti in questo articolo. 

Una volta è associata a un'identità del servizio gestito, un client del Bus di servizio può eseguire operazioni tutti autorizzate. Viene concessa l'autorizzazione mediante l'associazione di un file MSI con i ruoli del Bus di servizio. 

## <a name="service-bus-roles-and-permissions"></a>Le autorizzazioni e ruoli di Service Bus

Per la versione di anteprima pubblica iniziale, è possibile aggiungere solo un'identità del servizio gestito ai ruoli "Proprietario" o "Collaboratore" di uno spazio dei nomi Service Bus, che garantisce il controllo completo di identità in tutte le entità nello spazio dei nomi. Tuttavia, Gestione operazioni che modificano la topologia di spazio dei nomi sono inizialmente supportato solo tramite Gestione risorse di Azure e non tramite l'interfaccia di gestione di Service Bus REST nativo. Questo supporto significa anche che è possibile utilizzare il client di .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) oggetto all'interno di un'identità del servizio gestito.

## <a name="use-service-bus-with-a-managed-service-identity"></a>Utilizzare il Bus di servizio con un'identità del servizio gestito

Nella sezione seguente descrive i passaggi necessari per creare e distribuire un'applicazione di esempio che viene eseguito con un'identità del servizio gestito, come concedere l'accesso a uno spazio dei nomi di messaggistica del Bus di servizio e come l'applicazione interagisce con il Bus di servizio entità mediante tale identità.

In questa introduzione descrive un'applicazione web ospitata in [Azure App Service](https://azure.microsoft.com/services/app-service/). I passaggi necessari per un'applicazione ospitata VM sono simili.

### <a name="create-an-app-service-web-application"></a>Creare un'applicazione di servizio App web

Il primo passaggio consiste nel creare un'applicazione ASP.NET di servizio App. Se non si ha familiarità con le procedure per eseguire questa operazione in Azure, seguire [questa Guida dettagliata](../app-service/app-service-web-get-started-dotnet-framework.md). Tuttavia, invece di creare un'applicazione MVC, come illustrato nell'esercitazione, è possibile creare un'applicazione Web Form.

### <a name="set-up-the-managed-service-identity"></a>Impostare l'identità del servizio gestito

Dopo aver creato l'applicazione, passare all'app web è stato appena creato nel portale di Azure (vedere la figura la procedura), quindi individuare il **identità del servizio gestito** pagina e abilitare la funzionalità: 

![](./media/service-bus-managed-service-identity/msi1.png)

Dopo aver abilitato la funzionalità, una nuova identità del servizio viene creata in Azure Active Directory e configurata nell'host del servizio App.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Creare un nuovo spazio nomi di messaggistica del Bus di servizio

Successivamente, [creare uno spazio dei nomi di messaggistica del Bus di servizio](service-bus-create-namespace-portal.md) in una delle aree di Azure che dispongono del supporto di anteprima per RBAC: **Stati Uniti orientali**, **Stati Uniti orientali 2**, o **Europa occidentale** . 

Passare allo spazio dei nomi **il controllo di accesso (IAM)** pagina del portale e quindi fare clic su **Aggiungi** per aggiungere l'identità del servizio gestito per il **proprietario** ruolo. A tale scopo, cercare il nome dell'applicazione web nel **aggiungere autorizzazioni** pannello **selezionare** campo e quindi fare clic sulla voce. Fare quindi clic su **Salva**.

![](./media/service-bus-managed-service-identity/msi2.png)
 
Identità del servizio gestito dell'applicazione web dispone ora dell'accesso allo spazio dei nomi Service Bus e alla coda creata in precedenza. 

### <a name="run-the-app"></a>Esecuzione dell'app

Modificare ora la pagina predefinita dell'applicazione ASP.NET che è stato creato. È inoltre possibile utilizzare il codice dell'applicazione web da [questo repository GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ManagedServiceIdentity). 

La pagina aspx è la pagina di destinazione. Il codice è reperibile nel file Default.aspx.cs. Il risultato è un'applicazione web minima con alcuni campi di immissione e **inviare** e **ricezione** pulsanti che si connettono al Bus di servizio per inviare o ricevere messaggi.

Si noti come [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) viene inizializzato l'oggetto. Anziché utilizzare il provider di token di accesso condiviso Token (SAS), il codice crea un provider di token per l'identità del servizio gestito con il `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` chiamare. Di conseguenza, non esistono Nessun segreto per conservare e utilizzare. Il flusso del contesto di identità del servizio gestito per il Bus di servizio e l'handshake di autorizzazione vengono gestiti automaticamente dal provider di token, ovvero un modello più semplice rispetto all'utilizzo di firma di accesso condiviso.

Una volta apportate queste modifiche, pubblicare ed eseguire l'applicazione. Un modo semplice per ottenere i dati corretti di pubblicazione è per scaricare e importare un profilo di pubblicazione in Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Per inviare o ricevere messaggi, immettere il nome dello spazio dei nomi e il nome dell'entità è stato creato, quindi fare clic su **inviare** o **ricezione**. 
 
Si noti che l'identità del servizio gestito funziona solo all'interno dell'ambiente Azure e solo nella distribuzione di servizio App in cui è stata configurata. Si noti inoltre che le identità del servizio gestito non funzionano con gli slot di distribuzione di servizio App in questo momento.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)