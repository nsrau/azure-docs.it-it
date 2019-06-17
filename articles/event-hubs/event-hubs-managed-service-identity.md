---
title: Identità gestite per le risorse di Azure - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come usare le identità gestite per le risorse di Azure con Hub eventi di Azure
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 05/20/2019
ms.author: shvija
ms.openlocfilehash: 4e6f16a15547583baab63f452504d36eb2e43b85
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978433"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Identità gestite per le risorse di Azure con Hub eventi

Le [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) offrono una funzionalità per l'intera piattaforma Azure che consente di creare un'identità sicura associata alla distribuzione in cui viene eseguito il codice dell'applicazione. È quindi possibile associare l'identità ai ruoli di controllo di accesso che concedono autorizzazioni personalizzate per l'accesso a risorse di Azure specifiche necessarie per l'applicazione. 

Con le identità gestite, la piattaforma Azure gestisce questa identità di runtime. Non è necessario archiviare e proteggere le chiavi di accesso nel codice o nella configurazione dell'applicazione, né per l'identità stessa, né per le risorse a cui è necessario accedere. Un'app client di Hub eventi in esecuzione in un'applicazione di Servizio app di Azure o in una macchina virtuale con il supporto per le identità gestite per le risorse di Azure abilitato non deve quindi gestire le chiavi e le regole di firma di accesso condiviso o altri token di accesso. L'applicazione client necessita solo dell'indirizzo dell'endpoint dello spazio dei nomi di Hub eventi. Quando l'app si connette, Hub eventi associa il contesto dell'identità del servizio gestito al client in un'operazione che viene illustrata in un esempio riportato più avanti in questo articolo.

Una volta eseguita l'associazione a un'identità gestita, un client di Hub eventi può eseguire tutte le operazioni autorizzate. L'autorizzazione viene concessa associando un'identità gestita ai ruoli di Hub eventi. 

## <a name="event-hubs-roles-and-permissions"></a>Ruoli e autorizzazioni di Hub eventi
È possibile aggiungere un'identità gestita per il **proprietario dei dati di hub eventi** ruolo di uno spazio dei nomi di hub eventi. Questo ruolo concede l'identità del controllo completo (per la gestione e operazioni sui dati) in tutte le entità nello spazio dei nomi.

>[!IMPORTANT]
> È supportato in precedenza aggiungendo identità gestita per il **proprietario** oppure **collaboratore** ruolo. Accesso ai dati, tuttavia, i privilegi per **Owner** e **collaboratore** ruolo non vengono rispettate. Se si usa la **proprietario** o **collaboratore** ruolo, passare all'uso di **proprietario dei dati di hub eventi** ruolo.

Per usare il nuovo ruolo incorporato, seguire questa procedura: 

1. Passare al [portale di Azure](https://portal.azure.com)
2. Passare allo spazio dei nomi di hub eventi.
3. Nel **Namespace di hub eventi** pagina, selezionare **accesso (IAM)** nel menu a sinistra.
4. Nel **controllo di accesso (IAM)** pagina, selezionare **Add** nel **aggiungere un'assegnazione di ruolo** sezione. 

    ![Aggiungere un pulsante di assegnazione di ruolo](./media/event-hubs-managed-service-identity/add-role-assignment-button.png)
5. Nel **aggiungere un'assegnazione di ruolo** pagina, effettuare i passaggi seguenti: 
    1. Per la **ruolo**, selezionare **proprietario dei dati di hub eventi Azure**. 
    2. Selezionare il **identità** da aggiungere al ruolo.
    3. Selezionare **Salva**. 

        ![Ruolo di proprietario dei dati di hub eventi](./media/event-hubs-managed-service-identity/add-role-assignment-dialog.png)
6. Passare al **assegnazioni di ruolo** pagina e verificare che l'utente viene aggiunto al **proprietario dei dati hub eventi di Azure** ruolo. 

    ![Confermare l'utente viene aggiunto al ruolo](./media/event-hubs-managed-service-identity/role-assignments.png)
 
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

![Pagina dell’Identità del servizio gestita](./media/event-hubs-managed-service-identity/msi1.png)
 
Una volta abilitata la funzionalità, una nuova identità del servizio viene creata in Azure Active Directory e configurata nell'host del servizio app.

### <a name="create-a-new-event-hubs-namespace"></a>Creare un nuovo spazio dei nomi di Hub eventi

Successivamente [creare uno spazio dei nomi di hub eventi](event-hubs-create.md). 

Passare alla pagina **Controllo di accesso (IAM)** dello spazio dei nomi nel portale e quindi fare clic su **Aggiungi un'assegnazione di ruolo** per aggiungere l'identità gestita al ruolo **Proprietario**. A tale scopo, cercare il nome dell'applicazione Web nel campo **Seleziona** del pannello **Aggiungi autorizzazioni** e quindi fare clic sulla voce. Fare quindi clic su **Salva**. L'identità gestita per l'applicazione Web dispone ora dell'accesso allo spazio dei nomi di Hub eventi e all'hub eventi creato in precedenza. 

### <a name="run-the-app"></a>Esecuzione dell'app

Modificare ora la pagina predefinita dell'applicazione ASP.NET creata. È anche possibile usare il codice dell'applicazione Web di [questo repository GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Una volta avviata l'app, aprire nel browser la pagina EventHubsMSIDemo.aspx. È anche possibile impostare questa pagina come pagina iniziale. Il codice è disponibile nel file EventHubsMSIDemo.aspx.cs. Il risultato è un'applicazione Web minima con pochi campi di immissione e con pulsanti di **invio** e **ricezione** che consentono la connessione a Hub eventi per l'invio o la ricezione di eventi. 

Si noti come viene inizializzato l'oggetto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory). Invece di usare il provider di token di firma di accesso condiviso (SAS), il codice crea un provider di token per l'identità gestita con la chiamata a `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)`. In questo modo, non ci sono segreti da salvare e usare. Il flusso del contesto dell'identità gestita in Hub eventi e l'handshake di autorizzazione vengono gestiti automaticamente dal provider di token, offrendo così un modello più semplice rispetto all'uso della firma di accesso condiviso.

Dopo avere apportato queste modifiche, pubblicare ed eseguire l'applicazione. È possibile ottenere i dati di pubblicazione corretti scaricando e quindi importando un profilo di pubblicazione in Visual Studio:

![Importa profilo di pubblicazione](./media/event-hubs-managed-service-identity/msi3.png)
 
Per inviare o ricevere messaggi, immettere il nome dello spazio dei nomi e il nome dell'entità creata, quindi fare clic su **Invia** o **Ricevi**. 
 
L'identità gestita funziona solo all'interno dell'ambiente Azure e solo nella distribuzione del servizio app in cui è stata configurata. Al momento, le identità gestite non funzionano con gli slot di distribuzione del servizio app.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi, vedere i collegamenti seguenti:

* Iniziare con un'[esercitazione di Hub eventi](event-hubs-dotnet-standard-getstarted-send.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
* [Dettagli prezzi degli hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Applicazioni di esempio che usano Hub eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples)
