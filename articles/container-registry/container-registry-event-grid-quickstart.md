---
title: 'Guida introduttiva: Inviare eventi di Registro contenitori di Azure a Griglia di eventi'
description: In questa guida introduttiva si abilitano gli eventi di Griglia di eventi per il proprio registro contenitori e quindi si inviano gli eventi push e delete di un'immagine di contenitore a un'applicazione di esempio.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/23/2018
ms.author: danlep
ms.custom: seodec18
ms.openlocfilehash: 48fd64908a5cb2488d58f3c078e0ccb17419352f
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53260250"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Guida introduttiva: Inviare eventi dal registro contenitori privato a Griglia di eventi

Griglia di eventi di Azure è un servizio di routing di eventi completamente gestito che consente un uso degli eventi uniforme tramite un modello di pubblicazione-sottoscrizione. In questa guida introduttiva si usa l'interfaccia della riga di comando di Azure per creare un registro contenitori, sottoscrivere gli eventi del registro e quindi distribuire un'applicazione Web di esempio per ricevere gli eventi. Infine, si attivano gli eventi `push` e `delete` di un'immagine di contenitore e si visualizza il payload degli eventi nell'applicazione di esempio.

Al termine dei passaggi descritti in questo articolo, gli eventi inviati dal registro contenitori a Griglia di eventi vengono visualizzati nell'app Web di esempio:

![Rendering nel Web browser dell'applicazione Web di esempio con tre eventi ricevuti][sample-app-01]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito][azure-account] prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

I comandi dell'interfaccia della riga di comando di Azure in questo articolo sono formattati per la shell **Bash**. Se si usa una shell diversa, ad esempio PowerShell o il prompt dei comandi, può essere necessario modificare di conseguenza i caratteri di continuazione di riga o le righe di assegnazione di variabile. Questo articolo usa le variabili per ridurre al minimo la quantità di modifiche necessarie per i comandi.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite le risorse di Azure. Il comando [az group create][az-group-create] seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*. Se si vuole usare un altro nome per il gruppo di risorse, impostare `RESOURCE_GROUP_NAME` su un valore diverso.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Creare un registro di contenitori

Dopo aver creato il gruppo di risorse, distribuire un registro contenitori nel gruppo con i comandi seguenti. Prima di eseguire il comando [az acr create][az-acr-create], impostare `ACR_NAME` specificando un nome per il registro. Il nome del registro deve essere univoco in Azure e contenere un numero di caratteri alfanumerici compreso tra 5 e 50.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Dopo la creazione del registro, l'interfaccia della riga di comando di Azure restituisce un output simile al seguente:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
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

Al termine della distribuzione (che può richiedere alcuni minuti), aprire un browser e passare all'app Web per verificare se è in esecuzione:

`http://<your-site-name>.azurewebsites.net`

Verrà visualizzata l'app di esempio senza messaggi di evento:

![Visualizzazione nel Web browser dell'app Web di esempio senza eventi][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Sottoscrivere gli eventi del registro

In Griglia di eventi si sottoscrive un *argomento* per indicare gli eventi di cui si vuole tenere traccia e la destinazione a cui inviarli. Il comando [az eventgrid event-subscription create][az-eventgrid-event-subscription-create] seguente sottoscrive il registro contenitori creato e specifica l'URL dell'app Web come endpoint a cui inviare gli eventi. In questo caso vengono riutilizzate le variabili di ambiente popolate nelle sezioni precedenti. Non è quindi necessaria alcuna modifica.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Al termine della sottoscrizione, l'output dovrebbe essere simile al seguente:

```JSON
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Attivare gli eventi del registro

Ora che l'app di esempio è attiva e si è eseguita la sottoscrizione al registro con Griglia di eventi, si è pronti per generare alcuni eventi. In questa sezione si usa ACR Tasks per compilare un'immagine di contenitore ed eseguirne il push nel registro. ACR Tasks è una funzionalità di Registro contenitori di Azure che consente di compilare immagini dei contenitori nel cloud, senza che sia necessario installare il motore Docker nel computer locale.

### <a name="build-and-push-image"></a>Compilare l'immagine ed eseguirne il push

Eseguire il seguente comando dell'interfaccia della riga di comando di Azure per compilare un'immagine di contenitore dal contenuto di un repository GitHub. Per impostazione predefinita, ACR Tasks esegue automaticamente il push di un'immagine compilata nel registro generando l'evento `ImagePushed`.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Mentre ACR Tasks compila l'immagine e ne esegue il push dovrebbe essere visualizzato un output simile al seguente. L'output di esempio seguente è stato troncato per motivi di brevità.

```console
$ az acr build -r $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Per verificare che l'immagine compilata sia presente nel registro, eseguire il comando seguente per visualizzare i tag nel repository "myimage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Il tag "v1" dell'immagine compilata dovrebbe essere visualizzato nell'output, come nell'esempio seguente:

```console
$ az acr repository show-tags --name $ACR_NAME --repository myimage
[
  "v1"
]
```

### <a name="delete-the-image"></a>Eliminare l'immagine

A questo punto, generare un evento `ImageDeleted` eliminando l'immagine con il comando [az acr repository delete][az-acr-repository-delete]:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Verrà visualizzato un output simile al seguente, in cui viene chiesto di confermare l'eliminazione del manifesto e delle immagini associate:

```console
$ az acr repository delete --name $ACR_NAME --image myimage:v1
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): y
```

## <a name="view-registry-events"></a>Visualizzare gli eventi del registro

È stato ora eseguito il push di un'immagine nel registro ed è stata eliminata l'immagine. Passando all'app Web Event Grid Viewer dovrebbe essere possibile visualizzare entrambi gli eventi `ImageDeleted` e `ImagePushed`. È anche possibile che venga visualizzato un evento di convalida della sottoscrizione generato dall'esecuzione del comando nella sezione [Sottoscrivere gli eventi del registro](#subscribe-to-registry-events).

Lo screenshot seguente mostra l'app di esempio con tre eventi. L'evento `ImageDeleted` è espanso per mostrare i dettagli.

![Visualizzazione nel Web browser dell'app di esempio con gli eventi ImagePushed e ImageDeleted][sample-app-03]

Congratulazioni! Se vengono visualizzati gli eventi `ImagePushed` e `ImageDeleted`, significa che il registro invia eventi a Griglia di eventi e che quest'ultimo li inoltra a sua volta all'endpoint dell'app Web.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create in questa guida introduttiva non sono più necessarie, è possibile eliminarle tutte con il seguente comando dell'interfaccia della riga di comando di Azure. Quando si elimina un gruppo di risorse, vengono eliminate definitivamente tutte le risorse contenute nel gruppo.

**AVVISO**: questa operazione è irreversibile. Prima di eseguire il comando assicurarsi che nessuna delle risorse del gruppo sia più necessaria.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

È possibile trovare informazioni di riferimento sullo schema dei messaggi di evento di Registro contenitori di Azure nella documentazione relativa a Griglia di eventi:

[Schema di eventi di Griglia di eventi di Azure per il Registro contenitori](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è distribuito un registro contenitori, si è compilata un'immagine con ACR Tasks, si è eliminata l'immagine e si sono usati gli eventi del registro da Griglia di eventi con un'applicazione di esempio. A questo punto, passare all'esercitazione di ACR Tasks per altre informazioni sulla compilazione di immagini dei contenitori nel cloud, tra cui la compilazione automatizzata in caso di aggiornamento dell'immagine di base:

> [!div class="nextstepaction"]
> [Compilare immagini dei contenitori nel cloud con ACR Tasks](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository#az-acr-create
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
