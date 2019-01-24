---
title: Identità gestite per le risorse di Azure con il bus di servizio di Azure (anteprima) | Microsoft Docs
description: Usare le identità gestite per le risorse di Azure con il bus di servizio di Azure
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2018
ms.author: aschhab
ms.openlocfilehash: 5edeebd9698384785082e5a441c24e136ed22481
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856884"
---
# <a name="managed-identities-for-azure-resources-with-service-bus"></a>Identità gestite per le risorse di Azure con il bus di servizio 

Le [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) offrono una funzionalità per l'intera piattaforma Azure che consente di creare un'identità sicura associata alla distribuzione in cui viene eseguito il codice dell'applicazione. È quindi possibile associare l'identità ai ruoli di controllo di accesso che concedono autorizzazioni personalizzate per l'accesso a risorse di Azure specifiche necessarie per l'applicazione.

Con le identità gestite, la piattaforma Azure gestisce questa identità di runtime. Non è necessario archiviare e proteggere le chiavi di accesso nel codice o nella configurazione dell'applicazione, né per l'identità stessa, né per le risorse a cui è necessario accedere. Un'app client del bus di servizio in esecuzione in un'applicazione di Servizio app di Azure o in una macchina virtuale con il supporto per le identità gestite per le risorse di Azure abilitato non deve quindi gestire le chiavi e le regole di firma di accesso condiviso o altri token di accesso. L'applicazione client necessita solo dell'indirizzo dell'endpoint dello spazio dei nomi della messaggistica del bus di servizio. Quando l'app si connette, il bus di servizio associa il contesto dell'entità gestita al client in un'operazione che viene illustrata in un esempio riportato più avanti in questo articolo. Una volta eseguita l'associazione a un'identità gestita, il client del bus di servizio può eseguire tutte le operazioni autorizzate. L'autorizzazione viene concessa associando un'entità gestita ai ruoli del bus di servizio. 

## <a name="service-bus-roles-and-permissions"></a>Ruoli e autorizzazioni del bus di servizio

È possibile aggiungere un'identità gestita solo ai ruoli "Proprietario" o "Collaboratore" di uno spazio dei nomi del bus di servizio. In questo modo si concede all'identità il controllo completo su tutte le entità nello spazio dei nomi. Le operazioni di gestione che modificano la topologia dello spazio dei nomi sono tuttavia inizialmente supportate solo tramite Azure Resource Manager e non tramite l'interfaccia di gestione REST nativa del bus di servizio. Questo supporto significa anche che non è possibile usare l'oggetto [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) del client .NET Framework in un'identità gestita.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Usare il bus di servizio con le identità gestite per le risorse di Azure

La sezione seguente descrive i passaggi necessari per creare e distribuire un'applicazione di esempio che viene eseguita con un'identità gestita e mostra come concedere all'identità l'accesso a uno spazio dei nomi della messaggistica del bus di servizio e come l'applicazione interagisce con le entità del bus di servizio usando tale identità.

Questa introduzione descrive un'applicazione Web ospitata in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/). I passaggi necessari per un'applicazione ospitata in una macchina virtuale sono simili.

### <a name="create-an-app-service-web-application"></a>Creare un'applicazione Web del servizio app

Il primo passaggio consiste nel creare un'applicazione ASP.NET del servizio app. Se non si ha familiarità con la procedura per eseguire questa operazione in Azure, seguire [questa guida pratica](../app-service/app-service-web-get-started-dotnet-framework.md). Invece di creare un'applicazione MVC come illustrato nell'esercitazione, creare però un'applicazione Web Form.

### <a name="set-up-the-managed-identity"></a>Configurare l'identità gestita

Una volta creata l'applicazione, passare all'app Web appena creata nel portale di Azure (come illustrato anche nella guida pratica) e quindi passare alla pagina **Identità del servizio gestito** e abilitare la funzionalità: 

![](./media/service-bus-managed-service-identity/msi1.png)

Una volta abilitata la funzionalità, una nuova identità del servizio viene creata in Azure Active Directory e configurata nell'host del servizio app.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Creare un nuovo spazio dei nomi della messaggistica del bus di servizio

Successivamente, [creare uno spazio dei nomi della messaggistica del bus di servizio](service-bus-create-namespace-portal.md) in una delle aree di Azure con supporto di anteprima per Controllo degli accessi in base al ruolo: **Stati Uniti orientali**, **Stati Uniti orientali 2** o **Europa occidentale**. 

Passare alla pagina **Controllo di accesso (IAM)** dello spazio dei nomi nel portale e quindi fare clic su **Aggiungi un'assegnazione di ruolo** per aggiungere l'identità gestita al ruolo **Proprietario**. A tale scopo, cercare il nome dell'applicazione Web nel campo **Seleziona** del pannello **Aggiungi autorizzazioni** e quindi fare clic sulla voce. Fare quindi clic su **Salva**.

L'identità gestita dell'applicazione Web può ora accedere allo spazio dei nomi del bus di servizio e alla coda creata in precedenza. 

### <a name="run-the-app"></a>Esecuzione dell'app

Modificare ora la pagina predefinita dell'applicazione ASP.NET creata. È possibile usare il codice dell'applicazione Web di [questo repository GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

La pagina Default.aspx è la pagina di destinazione. Il codice è disponibile nel file Default.aspx.cs. Il risultato è un'applicazione Web minima con pochi campi di immissione e con pulsanti di **invio** e **ricezione** che consentono la connessione al bus di servizio per l'invio o la ricezione di messaggi.

Si noti come viene inizializzato l'oggetto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory). Invece di usare il provider di token di firma di accesso condiviso (SAS), il codice crea un provider di token per l'identità gestita con la chiamata a `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)`. In questo modo, non ci sono segreti da conservare e usare. Il flusso del contesto dell'identità gestita nel bus di servizio e l'handshake di autorizzazione vengono gestiti automaticamente dal provider di token. Questo modello è più semplice rispetto all'uso della firma di accesso condiviso.

Dopo avere apportato queste modifiche, pubblicare ed eseguire l'applicazione. È possibile ottenere con facilità i dati di pubblicazione corretti scaricando e quindi importando un profilo di pubblicazione in Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Per inviare o ricevere messaggi, immettere il nome dello spazio dei nomi e il nome dell'entità creata. Fare quindi clic su **Invia** o **Ricevi**.


> [!NOTE]
> - L'identità gestita funziona solo all'interno dell'ambiente di Azure, nei servizi app, nelle macchine virtuali di Azure e nei set di scalabilità. Per le applicazioni .NET, la libreria Microsoft.Azure.Services.AppAuthentication, usata dal pacchetto NuGet del bus di servizio, rappresenta un'astrazione di questo protocollo e supporta un'esperienza di sviluppo locale. Questa libreria consente anche di testare il codice in locale nel computer di sviluppo usando l'account utente da Visual Studio, dall'interfaccia della riga di comando di Azure 2.0 o tramite l'autenticazione integrata di Active Directory. Per altre informazioni sulle opzioni di sviluppo locale con questa libreria, vedere [Autenticazione da servizio a servizio ad Azure Key Vault usando .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Le identità gestite attualmente non funzionano con gli slot di distribuzione del servizio app.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)