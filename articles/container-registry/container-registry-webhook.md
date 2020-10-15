---
title: Webhook per rispondere alle azioni del registro
description: Informazioni su come usare i webhook per attivare eventi specifici quando nei repository del registro si verificano azioni push o pull.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 1db1098da81e6cf9ecb262c99f705b77af2efd26
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253395"
---
# <a name="using-azure-container-registry-webhooks"></a>Uso dei webhook di Registro Azure Container

Un Registro Azure Container archivia e gestisce le immagini dei contenitori Docker private, in modo analogo a come Docker Hub archivia le immagini Docker pubbliche. Può anche ospitare repository per [grafici Helm](container-registry-helm-repos.md) (anteprima), un formato di creazione di pacchetti per la distribuzione di applicazioni in Kubernetes. È possibile usare i webhook per attivare eventi specifici quando in uno dei repository del registro si verificano determinate azioni. I webhook possono rispondere agli eventi a livello di registro oppure possono essere limitati a un tag di repository specifico. Con un registro di [replica geografica](container-registry-geo-replication.md), ogni webhook viene configurato per rispondere agli eventi in una replica a livello di area specifica.

Per informazioni dettagliate sulle richieste di webhook, vedere le [informazioni di riferimento sullo schema del webhook di Registro Azure Container](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Prerequisiti

* Registro Azure Container: creare un registro contenitori nella sottoscrizione di Azure. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md). I [livelli di servizio di Registro Azure Container](container-registry-skus.md) hanno quote di Webhook differenti.
* Interfaccia della riga di comando di Docker: per configurare il computer locale come host Docker e accedere ai comandi della riga di comando di Docker, installare [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Creare un webhook - Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al registro contenitori in cui si intende creare un webhook.
1. In **Servizi** selezionare **Webhook**.
1. Selezionare **Aggiungi** nella barra degli strumenti relativa al webhook.
1. Completare il modulo *Crea webhook* con le informazioni seguenti:

| valore | Descrizione |
|---|---|
| Nome webhook | Il nome da assegnare al webhook. Può contenere solo lettere e numeri e deve avere una lunghezza di 5-50 caratteri. |
| Location | Per un registro di [replica geografica ](container-registry-geo-replication.md), specificare l'area di Azure della replica del registro. 
| URI del servizio | L'URI in cui il webhook deve inviare le notifiche POST. |
| Intestazioni personalizzate | Le intestazioni che si vuole passare insieme alla richiesta POST. Devono essere nel formato "chiave: valore". |
| Azioni trigger | Le azioni che attivano il webhook. Le azioni includono push dell'immagine, eliminazione di immagini, push del grafico Helm, eliminazione del grafico Helm e quarantena delle immagini. È possibile scegliere una o più azioni per attivare il webhook. |
| Stato | Lo stato del webhook dopo che è stato creato. Questa opzione è abilitata per impostazione predefinita. |
| Scope | L'ambito in cui viene applicato il webhook. Se non specificato, l'ambito comprende tutti gli eventi del registro. Può essere specificato per un repository o un tag usando il formato "repository:tag" o "repository:*" per tutti i tag in un repository. |

Modulo di webhook di esempio:

![Screenshot che mostra la creazione di un webhook ACR nella portale di Azure.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Creare un webhook - Interfaccia della riga di comando di Azure

Per creare un webhook usando l'interfaccia della riga di comando di Azure, usare il comando [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create). Il comando seguente crea un webhook per tutti gli eventi di eliminazione di immagini nel registro *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testare un webhook

### <a name="azure-portal"></a>Portale di Azure

Prima di usare il webhook, è possibile testarlo con il pulsante **Ping**. Il comando Ping invia una richiesta POST generica all'endpoint specificato e registra la risposta. L'uso del comando Ping consente di verificare se la configurazione del webhook è corretta.

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

[Avvio rapido: Inviare eventi del registro contenitori a Griglia di eventi](container-registry-event-grid-quickstart.md)
