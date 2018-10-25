---
title: Identità gestite per le risorse di Azure con l'anteprima di Hub eventi di Azure | Microsoft Docs
description: Usare le identità gestite per le risorse di Azure con Hub eventi di Azure
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: shvija
ms.openlocfilehash: 815a6ff528e024ed1685b09b66f8fabce4d360c1
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2018
ms.locfileid: "48784554"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Identità gestite per le risorse di Azure con Hub eventi

Le [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) offrono una funzionalità per l'intera piattaforma Azure che consente di creare un'identità sicura associata alla distribuzione in cui viene eseguito il codice dell'applicazione. È quindi possibile associare l'identità ai ruoli di controllo di accesso che concedono autorizzazioni personalizzate per l'accesso a risorse di Azure specifiche necessarie per l'applicazione. 

Con le identità gestite, la piattaforma Azure gestisce questa identità di runtime. Non è necessario archiviare e proteggere le chiavi di accesso nel codice o nella configurazione dell'applicazione, né per l'identità stessa, né per le risorse a cui è necessario accedere. Un'app client di Hub eventi in esecuzione in un'applicazione di Servizio app di Azure o in una macchina virtuale con il supporto per le identità gestite per le risorse di Azure abilitato non deve quindi gestire le chiavi e le regole di firma di accesso condiviso o altri token di accesso. L'applicazione client necessita solo dell'indirizzo dell'endpoint dello spazio dei nomi di Hub eventi. Quando l'app si connette, Hub eventi associa il contesto dell'identità del servizio gestito al client in un'operazione che viene illustrata in un esempio riportato più avanti in questo articolo.

Una volta eseguita l'associazione a un'identità gestita, un client di Hub eventi può eseguire tutte le operazioni autorizzate. L'autorizzazione viene concessa associando un'identità gestita ai ruoli di Hub eventi. 

## <a name="event-hubs-roles-and-permissions"></a>Ruoli e autorizzazioni di Hub eventi

È possibile aggiungere un'identità gestita solo al ruolo "Proprietario" o "Collaboratore" di uno spazio dei nomi di Hub eventi, concedendo così all'identità controllo completo su tutte le entità nello spazio dei nomi. Le operazioni di gestione che modificano la topologia dello spazio dei nomi sono tuttavia inizialmente supportate solo tramite Azure Resource Manager e non tramite l'interfaccia di gestione REST nativa dell'Hub eventi. Questo supporto significa anche che non è possibile usare l'oggetto [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) del client .NET Framework in un'identità gestita. 
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>Uso dell'Hub eventi con le identità gestite per le risorse di Azure

La sezione seguente descrive la procedura seguente:

1. Creare e distribuire un'applicazione di esempio eseguita in un'identità gestita.
2. Concedere all'identità l'accesso a uno spazio dei nomi di Hub eventi.
3. Come l'applicazione interagisce con l'hub eventi usando tale identità.

Questa introduzione descrive un'applicazione Web ospitata in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/). I passaggi necessari per un'applicazione ospitata in una macchina virtuale sono simili.

### <a name="create-an-app-service-web-application"></a>Creare un'applicazione Web del servizio app

Il primo passaggio consiste nel creare un'applicazione ASP.NET del servizio app. Se non si ha familiarità con le procedure per eseguire questa operazione in Azure, seguire [questa guida pratica](../app-service/app-service-web-get-started-dotnet-framework.md). Invece di creare un'applicazione MVC come illustrato nell'esercitazione, creare però un'applicazione Web Form.

### <a name="set-up-the-managed-identity"></a>Configurare l'identità gestita

Una volta creata l'applicazione, passare all'app Web appena creata nel portale di Azure (come illustrato anche nella guida pratica) e quindi passare alla pagina **Identità del servizio gestito** e abilitare la funzionalità: 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
Una volta abilitata la funzionalità, una nuova identità del servizio viene creata in Azure Active Directory e configurata nell'host del servizio app.

### <a name="create-a-new-event-hubs-namespace"></a>Creare un nuovo spazio dei nomi di Hub eventi

Successivamente, [creare uno spazio dei nomi di Hub eventi](event-hubs-create.md) in una delle aree di Azure con supporto di anteprima per l'identità gestite per le risorse di Azure: **Stati Uniti orientali**, **Stati Uniti orientali 2**, o **Europa occidentale**. 

Passare alla pagina **Controllo di accesso (IAM)** dello spazio dei nomi nel portale e quindi fare clic su **Aggiungi** per aggiungere l'identità gestita al ruolo **Proprietario**. A tale scopo, cercare il nome dell'applicazione Web nel campo **Seleziona** del pannello **Aggiungi autorizzazioni** e quindi fare clic sulla voce. Fare quindi clic su **Salva**.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
L'identità gestita per l'applicazione Web dispone ora dell'accesso allo spazio dei nomi di Hub eventi e all'hub eventi creato in precedenza. 

### <a name="run-the-app"></a>Esecuzione dell'app

Modificare ora la pagina predefinita dell'applicazione ASP.NET creata. È anche possibile usare il codice dell'applicazione Web di [questo repository GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Una volta avviata l'app, aprire nel browser la pagina EventHubsMSIDemo.aspx. È anche possibile impostare questa pagina come pagina iniziale. Il codice è disponibile nel file EventHubsMSIDemo.aspx.cs. Il risultato è un'applicazione Web minima con pochi campi di immissione e con pulsanti di **invio** e **ricezione** che consentono la connessione a Hub eventi per l'invio o la ricezione di eventi. 

Si noti come viene inizializzato l'oggetto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory). Invece di usare il provider di token di firma di accesso condiviso (SAS), il codice crea un provider di token per l'identità gestita con la chiamata a `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)`. In questo modo, non ci sono segreti da salvare e usare. Il flusso del contesto dell'identità gestita in Hub eventi e l'handshake di autorizzazione vengono gestiti automaticamente dal provider di token, offrendo così un modello più semplice rispetto all'uso della firma di accesso condiviso.

Dopo avere apportato queste modifiche, pubblicare ed eseguire l'applicazione. È possibile ottenere i dati di pubblicazione corretti scaricando e quindi importando un profilo di pubblicazione in Visual Studio:

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Per inviare o ricevere messaggi, immettere il nome dello spazio dei nomi e il nome dell'entità creata, quindi fare clic su **Invia** o **Ricevi**. 
 
L'identità gestita funziona solo all'interno dell'ambiente Azure e solo nella distribuzione del servizio app in cui è stata configurata. Al momento, le identità gestite non funzionano con gli slot di distribuzione del servizio app.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi, vedere i collegamenti seguenti:

* Iniziare con un'[esercitazione di Hub eventi](event-hubs-dotnet-standard-getstarted-send.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
* [Dettagli prezzi degli hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Applicazioni di esempio che usano Hub eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples)
