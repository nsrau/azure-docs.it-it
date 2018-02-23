---
title: Webhook del Registro contenitori di Azure
description: Informazioni su come usare i webhook per attivare eventi specifici quando nei repository del registro si verificano determinate azioni.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: nepeters
ms.openlocfilehash: 915f90fd5d969d5544d56e5bec754b799f349015
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="using-azure-container-registry-webhooks"></a>Uso dei webhook del Registro contenitori di Azure

Un registro contenitori di Azure archivia e gestisce le immagini dei contenitori Docker private, in modo analogo a come Docker Hub archivia le immagini Docker pubbliche. È possibile usare i webhook per attivare eventi specifici quando in uno dei repository del registro si verificano determinate azioni. I webhook possono rispondere agli eventi a livello di registro oppure possono essere limitati a un tag di repository specifico.

Per informazioni dettagliate sulle richieste di webhook, vedere le [informazioni di riferimento sullo schema del webhook del registro contenitori di Azure](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>prerequisiti

* Registro di contenitori di Azure: creare un registro di contenitori nella sottoscrizione di Azure. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md).
* Interfaccia della riga di comando di Docker: per configurare il computer locale come host Docker e accedere ai comandi della riga di comando di Docker, installare [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Creare un webhook tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Passare al registro contenitori in cui si intende creare un webhook.
1. In **Servizi** selezionare **Webhook**.
1. Selezionare **Aggiungi** nella barra degli strumenti relativa al webhook.
1. Completare il modulo *Crea webhook* con le informazioni seguenti:

| Valore | DESCRIZIONE |
|---|---|
| NOME | Il nome da assegnare al webhook. Può contenere solo caratteri minuscoli e numeri e deve avere una lunghezza di 5-50 caratteri. |
| URI del servizio | L'URI in cui il webhook deve inviare le notifiche POST. |
| Intestazioni personalizzate | Le intestazioni che si vuole passare insieme alla richiesta POST. Devono essere nel formato "chiave: valore". |
| Azioni trigger | Le azioni che attivano il webhook. Attualmente i webhook possono essere attivati tramite azioni di push e/o eliminazione di immagini. |
| Status | Lo stato del webhook dopo che è stato creato. Questa opzione è abilitata per impostazione predefinita. |
| Scope | L'ambito in cui viene applicato il webhook. Per impostazione predefinita, l'ambito comprende tutti gli eventi del registro. Può essere limitato a un repository o un tag usando il formato "repository:tag". |

Modulo di webhook di esempio:

![Interfaccia utente per la creazione di webhook del Registro contenitori di Azure nel portale di Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Creare un webhook usando l'interfaccia della riga di comando di Azure

Per creare un webhook usando l'interfaccia della riga di comando di Azure, usare il comando [az acr webhook create](/cli/azure/acr/webhook#az_acr_webhook_create).

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testare un webhook

### <a name="azure-portal"></a>Portale di Azure

Prima di usare il webhook in azioni di push o eliminazione di un'immagine del contenitore, è possibile testarlo tramite il pulsante **Ping**. Il comando Ping invia una richiesta POST generica all'endpoint specificato e registra la risposta. L'uso del comando Ping consente di verificare se la configurazione del webhook è corretta.

1. Selezionare il webhook da testare.
2. Nella barra degli strumenti superiore selezionare l'azione **Ping**.
3. Controllare la risposta dell'endpoint nella colonna **Stato HTTP**.

![Interfaccia utente per la creazione di webhook del Registro contenitori di Azure nel portale di Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per testare un webhook del registro contenitori di Azure con l'interfaccia della riga di comando di Azure, usare il comando [az acr webhook ping](/cli/azure/acr/webhook#az_acr_webhook_ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Per visualizzare i risultati, usare il comando [az acr webhook list-events](/cli/azure/acr/webhook#list-events).

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

[Informazioni di riferimento sullo schema del webhook del registro contenitori di Azure](container-registry-webhook-reference.md)