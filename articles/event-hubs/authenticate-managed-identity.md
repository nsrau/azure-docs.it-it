---
title: Autenticazione di un'identità gestita con Azure Active Directory
description: Questo articolo fornisce informazioni sull'autenticazione di un'identità gestita con Azure Active Directory per accedere alle risorse di Hub eventi di Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: dfc60fbc03021e72dccc0f60a7ac34d204ef6df9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025187"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse degli hub eventiAuthenticate a managed identity with Azure Active Directory to access Event Hubs Resources
Hub eventi di Azure supporta l'autenticazione di Azure Active Directory (Azure AD) con identità gestite per le risorse di Azure.Azure Event Hubs supports Azure Active Directory (Azure AD) authentication with [managed identities for Azure resources](../active-directory/managed-identities-azure-resources/overview.md). Le identità gestite per le risorse di Azure possono autorizzare l'accesso alle risorse di Hub eventi usando le credenziali di Azure AD dalle applicazioni in esecuzione in Macchine virtuali di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altri servizi. Usando le identità gestite per le risorse di Azure con l'autenticazione di Azure AD, è possibile evitare di archiviare le credenziali con le applicazioni eseguite nel cloud.

Questo articolo illustra come autorizzare l'accesso a un hub eventi usando un'identità gestita da una macchina virtuale di Azure.This article shows how to authorize access to an event hub by using a managed identity from an Azure VM.

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale
Prima di poter usare le identità gestite per le risorse di Azure per autorizzare le risorse di Hub eventi dalla macchina virtuale, è necessario abilitare le identità gestite per le risorse di Azure nella macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Azure portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Librerie client di Azure Resource ManagerAzure Resource Manager client libraries](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Concedere autorizzazioni a un'identità gestita in Azure ADGrant permissions to a managed identity in Azure AD
Per autorizzare una richiesta al servizio Hub eventi da un'identità gestita nell'applicazione, configurare innanzitutto le impostazioni del controllo degli accessi in base al ruolo per tale identità gestita. Hub eventi di Azure definisce i ruoli RBAC che includono le autorizzazioni per l'invio e la lettura dagli hub eventi. Quando il ruolo Controllo degli accessi in base al ruolo viene assegnato a un'identità gestita, all'identità gestita viene concesso l'accesso ai dati di Hub eventi nell'ambito appropriato.

Per altre informazioni sull'assegnazione di ruoli RBAC, vedere [Eseguire l'autenticazione con Azure Active Directory per accedere alle risorse di Hub eventi.](authorize-access-azure-active-directory.md)

## <a name="use-event-hubs-with-managed-identities"></a>Uso di Hub eventi con le identità gestite
Per usare Hub eventi con identità gestite, è necessario assegnare all'identità il ruolo e l'ambito appropriato. La procedura descritta in questa sezione usa una semplice applicazione che viene eseguita con un'identità gestita e accede alle risorse di Hub eventi.

In questo caso si usa un'applicazione Web di esempio ospitata nel servizio app di [Azure.](https://azure.microsoft.com/services/app-service/) Per istruzioni dettagliate per la creazione di un'applicazione Web, vedere [Creare un'app Web ASP.NET Core in AzureFor](../app-service/app-service-web-get-started-dotnet.md) step-by-step instructions for creating a web application, see Create an ASP.NET Core web app in Azure

Una volta creata l'applicazione, attenersi alla seguente procedura: 

1. Vai a **Impostazioni** e seleziona **Identità**. 
1. Selezionare lo **stato** su **.** 
1. Selezionare **Salva** per salvare l'impostazione. 

    ![Identità gestita per un'app Web](./media/authenticate-managed-identity/identity-web-app.png)

Dopo aver abilitato questa impostazione, una nuova identità del servizio viene creata in Azure Active Directory (Azure AD) e configurata nell'host del servizio app.

A questo punto, assegnare questa identità del servizio a un ruolo nell'ambito richiesto nelle risorse di Hub eventi.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Per assegnare ruoli RBAC tramite il portale di AzureTo Assign RBAC roles using the Azure portal
Per assegnare un ruolo alle risorse di Hub eventi, passare a tale risorsa nel portale di Azure.To assign a role to Event Hubs resources, navigate to that resource in the Azure portal. Visualizzare le impostazioni di controllo di accesso (IAM) per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

> [!NOTE]
> I passaggi seguenti assegnano un ruolo di identità del servizio agli spazi dei nomi Di Hub eventi. È possibile seguire la stessa procedura per assegnare un ruolo con ambito a qualsiasi risorsa Hub eventi. 

1. Nel portale di Azure passare allo spazio dei nomi Hub eventi e visualizzare la **panoramica** dello spazio dei nomi. 
1. Selezionare **Controllo di accesso (IAM)** nel menu a sinistra per visualizzare le impostazioni di controllo di accesso per l'hub eventi.
1.  Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
3.  Selezionare **Aggiungi** per aggiungere un nuovo ruolo.
4.  Nella pagina **Aggiungi assegnazione ruolo** selezionare i ruoli Hub eventi che si desidera assegnare. Quindi cercare per individuare l'identità del servizio registrato per assegnare il ruolo.
    
    ![Pagina Aggiungi assegnazione ruolo](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Selezionare **Salva**. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, l'immagine seguente mostra che l'identità del servizio ha il proprietario dei dati degli hub eventi.
    
    ![Identità assegnata a un ruolo](./media/authenticate-managed-identity/role-assigned.png)

Dopo aver assegnato il ruolo, l'applicazione Web avrà accesso alle risorse di Hub eventi nell'ambito definito. 

### <a name="test-the-web-application"></a>Testare l'applicazione Web
1. Creare uno spazio dei nomi di Hub eventi e un hub eventi. 
2. Distribuire l'app Web in Azure.Deploy the web app to Azure. Vedere la sezione a schede seguente per i collegamenti all'applicazione Web su GitHub.See the following tabbed section for links to the web application on GitHub. 
3. Assicurarsi che SendReceive.aspx sia impostato come documento predefinito per l'app Web. 
3. Abilitare **l'identità** per l'app Web. 
4. Assegnare questa identità al ruolo **Proprietario dati hub eventi** a livello di spazio dei nomi o hub eventi. 
5. Eseguire l'applicazione Web, immettere il nome dello spazio dei nomi e il nome dell'hub eventi, un messaggio e selezionare **Invia**. Per ricevere l'evento, selezionare **Ricevi**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs (ultimi)](#tab/latest)
È ora possibile avviare l'applicazione web e puntare il browser alla pagina aspx di esempio. È possibile trovare l'applicazione Web di esempio che invia e riceve dati dalle risorse di Hub eventi nel [repository GitHub.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

Installare il pacchetto più recente da [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)e avviare l'invio di eventi agli hub eventi utilizzando **EventHubProducerClient** e la ricezione di eventi tramite **EventHubConsumerClient**. 

> [!NOTE]
> Per un esempio Java che usa un'identità gestita per pubblicare eventi in un hub eventi, vedere [Pubblicare eventi con identità di Azure in GitHub.For](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)a Java sample that uses a managed identity to publish events to an event hub, see Publish events with Azure identity sample on GitHub .

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (legacy)](#tab/old)
È ora possibile avviare l'applicazione web e puntare il browser alla pagina aspx di esempio. È possibile trovare l'applicazione Web di esempio che invia e riceve dati dalle risorse di Hub eventi nel [repository GitHub.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)

Installare il pacchetto più recente da [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)e iniziare a inviare e ricevere dati dagli hub eventi usando EventHubClient, come illustrato nel codice seguente: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Hub eventi per Kafka
È possibile usare le applicazioni Apache Kafka per inviare e ricevere messaggi dagli hub eventi di Azure usando l'identità gestita OAuth.You can use Apache Kafka applications to send messages to and receive messages from Azure Event Hubs using managed identity OAuth. Vedere l'esempio seguente su GitHub: [Hub di eventi per Kafka - inviare e ricevere messaggi utilizzando l'identità gestita OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Esempi
- **Esempi di Azure.Messaging.EventHubsAzure.Messaging.EventHubs** samples
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- Esempi di [Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Questi esempi usano la vecchia libreria **Microsoft.Azure.EventHubs,** ma è possibile aggiornarla facilmente usando la libreria **Azure.Messaging.EventHubs** più recente. Per spostare l'esempio dall'uso della libreria precedente a una nuova, vedere la [Guida per eseguire la migrazione da Microsoft.Azure.EventHubs ad Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Questo esempio è stato aggiornato per usare la libreria **Azure.Messaging.EventHubs** più recente.
- [Hub di eventi per Kafka - inviare e ricevere messaggi utilizzando l'identità gestita OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Passaggi successivi
- Vedere l'articolo seguente per informazioni sulle identità gestite per le risorse di Azure: [Che cos'è le identità gestite per le risorse](../active-directory/managed-identities-azure-resources/overview.md) di Azure?
- Vedere i seguenti articoli correlati:
    - [Autenticare le richieste agli hub eventi di Azure da un'applicazione tramite Azure Active DirectoryAuthenticate requests to Azure Event Hubs from an application using Azure Active Directory](authenticate-application.md)
    - [Autenticare le richieste agli hub eventi di Azure usando le firme di accesso condivisoAuthenticate requests to Azure Event Hubs using Shared Access Signatures](authenticate-shared-access-signature.md)
    - [Autorizzare l'accesso alle risorse di Hub eventi usando Azure Active DirectoryAuthorize access to Event Hubs resources using Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizzare l'accesso alle risorse degli hub eventi usando le firme di accesso condivisoAuthorize access to Event Hubs resources using Shared Access Signatures](authorize-access-shared-access-signature.md)