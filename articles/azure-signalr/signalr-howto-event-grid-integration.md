---
title: Come inviare eventi di servizio Azure SignalR per griglia di eventi
description: Una guida descrive come abilitare gli eventi di griglia di eventi per il SignalR Service, quindi connessione client disconnesso/connesso eventi inviati a un'applicazione di esempio.
services: azure-signalr
author: chenyl
ms.service: azure-signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 2d782306938136ce6d21a331185f591316f58a29
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789176"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Come inviare eventi dal servizio Azure SignalR per griglia di eventi

Griglia di eventi di Azure è un servizio di routing di eventi completamente gestito che fornisce il consumo di eventi uniforme tramite un modello pub-sub. In questa guida è usare il comando di Azure per creare un servizio Azure SignalR, sottoscrivere gli eventi di connessione, quindi distribuire un'applicazione web di esempio per ricevere gli eventi. Infine, è possibile connettersi e disconnettersi e vedere il payload dell'evento nell'applicazione di esempio.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito][azure-account] prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

I comandi dell'interfaccia della riga di comando di Azure in questo articolo sono formattati per la shell **Bash**. Se si usa una shell diversa, ad esempio PowerShell o il prompt dei comandi, può essere necessario modificare di conseguenza i caratteri di continuazione di riga o le righe di assegnazione di variabile. Questo articolo usa le variabili per ridurre al minimo la quantità di modifiche necessarie per i comandi.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite le risorse di Azure. Quanto segue [creare il gruppo di az][az-group-create] comando crea un gruppo di risorse denominato *myResourceGroup* nel *eastus* area. Se si vuole usare un altro nome per il gruppo di risorse, impostare `RESOURCE_GROUP_NAME` su un valore diverso.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Servizio Azure SignalR

Successivamente, distribuire un servizio Azure Signalr nel gruppo di risorse con i comandi seguenti.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Dopo aver creato il SignalR Service, il comando di Azure restituisce un output simile al seguente:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Creare un endpoint per gli eventi

In questa sezione si usa un modello di Resource Manager disponibile in un repository GitHub per distribuire un'applicazione Web di esempio nel servizio app di Azure. Successivamente, si sottoscrivono gli eventi di Griglia di eventi del registro e si specifica l'app come endpoint a cui vengono inviati gli eventi.

Per distribuire l'app di esempio, impostare `SITE_NAME` su un nome univoco per l'app Web ed eseguire i comandi seguenti. Il nome del sito deve essere univoco all'interno di Azure perché fa parte del nome di dominio completo (FQDN) dell'app Web. In una sezione successiva, si passa al nome di dominio completo dell'app in un Web browser per visualizzare gli eventi del registro.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Dopo aver completato la distribuzione (potrebbe richiedere alcuni minuti), aprire un browser e passare all'App web per verificare che sia in esecuzione:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Sottoscrivere gli eventi del registro

In Griglia di eventi si sottoscrive un *argomento* per indicare gli eventi di cui si vuole tenere traccia e la destinazione a cui inviarli. Quanto segue [sottoscrizione di eventi di az eventgrid creare][az-eventgrid-event-subscription-create] comando effettua la sottoscrizione al servizio Azure SignalR creato e specifica l'URL dell'app web come endpoint a cui è necessario inviare gli eventi. In questo caso vengono riutilizzate le variabili di ambiente popolate nelle sezioni precedenti. Non è quindi necessaria alcuna modifica.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Al termine della sottoscrizione, l'output dovrebbe essere simile al seguente:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Attivare gli eventi del registro

Passare alla modalità di servizio per `Serverless Mode` e configurare una connessione client per il SignalR Service. È possibile eseguire [esempio senza server](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) come riferimento.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a seperate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Visualizzare gli eventi del registro

A questo punto, è stato connesso un client per il SignalR Service. Passare all'App web del Visualizzatore di griglia di eventi e verrà visualizzato un `ClientConnectionConnected` evento. Se si interrompe il client, si noterà anche un `ClientConnectionDisconnected` evento.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
