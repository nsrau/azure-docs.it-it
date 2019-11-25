---
title: Webhooks to respond to registry actions
description: Learn how to use webhooks to trigger events when push or pull actions occur in your registry repositories.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454369"
---
# <a name="using-azure-container-registry-webhooks"></a>Uso dei webhook di Registro Azure Container

Un Registro Azure Container archivia e gestisce le immagini dei contenitori Docker private, in modo analogo a come Docker Hub archivia le immagini Docker pubbliche. It can also host repositories for [Helm charts](container-registry-helm-repos.md) (preview), a packaging format to deploy applications to Kubernetes. È possibile usare i webhook per attivare eventi specifici quando in uno dei repository del registro si verificano determinate azioni. I webhook possono rispondere agli eventi a livello di registro oppure possono essere limitati a un tag di repository specifico. With a  [geo-replicated](container-registry-geo-replication.md) registry, you configure each webhook to respond to events in a specific regional replica.

Per informazioni dettagliate sulle richieste di webhook, vedere le [informazioni di riferimento sullo schema del webhook di Registro Azure Container](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Prerequisiti

* Registro Azure Container: creare un registro contenitori nella sottoscrizione di Azure. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md). The [Azure Container Registry SKUs](container-registry-skus.md) have different webhooks quotas.
* Interfaccia della riga di comando di Docker: per configurare il computer locale come host Docker e accedere ai comandi della riga di comando di Docker, installare [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Create webhook - Azure portal

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al registro contenitori in cui si intende creare un webhook.
1. Under **Services**, select **Webhooks**.
1. Selezionare **Aggiungi** nella barra degli strumenti relativa al webhook.
1. Completare il modulo *Crea webhook* con le informazioni seguenti:

| Value | Description |
|---|---|
| Webhook name | Il nome da assegnare al webhook. It may contain only letters and numbers, and must be 5-50 characters in length. |
| Località | For a [geo-replicated](container-registry-geo-replication.md) registry, specify the Azure region of the registry replica. 
| URI del servizio | L'URI in cui il webhook deve inviare le notifiche POST. |
| Intestazioni personalizzate | Le intestazioni che si vuole passare insieme alla richiesta POST. Devono essere nel formato "chiave: valore". |
| Azioni trigger | Le azioni che attivano il webhook. Actions include image push, image delete, Helm chart push, Helm chart delete, and image quarantine. You can choose one or more actions to trigger the webhook. |
| Status | Lo stato del webhook dopo che è stato creato. Questa opzione è abilitata per impostazione predefinita. |
| Scope | L'ambito in cui viene applicato il webhook. If not specified, the scope is for all events in the registry. It can be specified for a repository or a tag by using the format "repository:tag", or "repository:*" for all tags under a repository. |

Modulo di webhook di esempio:

![Interfaccia utente per la creazione di webhook di Registro Azure Container nel portale di Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Create webhook - Azure CLI

Per creare un webhook usando l'interfaccia della riga di comando di Azure, usare il comando [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create). The following command creates a webhook for all image delete events in the registry *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testare un webhook

### <a name="azure-portal"></a>Portale di Azure

Prior to using the webhook, you can test it with the **Ping** button. Il comando Ping invia una richiesta POST generica all'endpoint specificato e registra la risposta. L'uso del comando Ping consente di verificare se la configurazione del webhook è corretta.

1. Selezionare il webhook da testare.
2. Nella barra degli strumenti superiore selezionare l'azione **Ping**.
3. Controllare la risposta dell'endpoint nella colonna **Stato HTTP**.

![Interfaccia utente per la creazione di webhook di Registro Azure Container nel portale di Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per testare un webhook di Registro Azure Container con l'interfaccia della riga di comando di Azure, usare il comando [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Per visualizzare i risultati, usare il comando [az acr webhook list-events](/cli/azure/acr/webhook).

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Eliminare un webhook

### <a name="azure-portal"></a>Portale di Azure

Per eliminare un webhook, è possibile selezionarlo e usare il pulsante **Elimina** nel portale di Azure.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Passaggi successivi

### <a name="webhook-schema-reference"></a>Informazioni di riferimento per lo schema di webhook

Per informazioni dettagliate sul formato e sulle proprietà dei payload degli eventi JSON generati da Registro Azure Container, vedere le informazioni di riferimento per lo schema di webhook:

[Informazioni di riferimento sullo schema del webhook di Registro Azure Container](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Eventi di Griglia di eventi

Oltre agli eventi di webhook del registro nativo descritti in questo articolo, Registro Azure Container può trasmettere eventi a Griglia di eventi:

[Guida introduttiva: inviare eventi del registro contenitori a Griglia di eventi ](container-registry-event-grid-quickstart.md)
