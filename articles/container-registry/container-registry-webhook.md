---
title: Webhook del Registro contenitori di Azure
description: Informazioni su come usare i webhook per attivare eventi specifici quando nei repository del registro si verificano determinate azioni.
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acr, azure-container-registry
keywords: Docker, contenitori, registro contenitori di Azure
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2017
ms.author: nepeters
ms.openlocfilehash: 5a9dab91aafb92f944b473f05144242143e36477
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2017
---
# <a name="using-azure-container-registry-webhooks"></a>Uso dei webhook del Registro contenitori di Azure

Un registro contenitori di Azure archivia e gestisce le immagini dei contenitori Docker private, in modo analogo a come Docker Hub archivia le immagini Docker pubbliche. Usando i webhook, è possibile attivare eventi specifici quando in uno dei repository del registro si verificano determinate azioni. I webhook possono rispondere agli eventi a livello di registro oppure possono essere limitati a un tag di repository specifico.

Per altri concetti e informazioni di base, vedere [Informazioni sul Registro contenitori di Azure](./container-registry-intro.md).

## <a name="prerequisites"></a>Prerequisiti

- Un registro contenitori gestito in Azure: creare un registro contenitori gestito nella sottoscrizione di Azure Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md).
- Interfaccia della riga di comando di Docker: per configurare il computer locale come host Docker e accedere ai comandi della riga di comando di Docker, installare [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Creare un webhook tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al registro in cui si vuole creare i webhook.

2. Nel pannello del contenitore selezionare **Webhook** in **SERVIZI**.

3. Selezionare **Aggiungi** nella barra degli strumenti del pannello relativo al webhook.

4. Completare il modulo *Crea webhook* con le informazioni seguenti:

| Valore | Descrizione |
|---|---|
| Nome | Il nome da assegnare al webhook. Può contenere solo caratteri minuscoli e numeri e deve avere una lunghezza di 5-50 caratteri. |
| URI del servizio | L'URI in cui il webhook deve inviare le notifiche POST. |
| Intestazioni personalizzate | Le intestazioni che si vuole passare insieme alla richiesta POST. Devono essere nel formato "chiave: valore". |
| Azioni trigger | Le azioni che attivano il webhook. Attualmente i webhook possono essere attivati tramite azioni di push e/o eliminazione di immagini. |
| Stato | Lo stato del webhook dopo che è stato creato. Questa opzione è abilitata per impostazione predefinita. |
| Scope | L'ambito in cui viene applicato il webhook. Per impostazione predefinita, l'ambito comprende tutti gli eventi del registro. Può essere limitato a un repository o un tag usando il formato "repository:tag". |

Modulo di webhook di esempio:

![Interfaccia utente per la creazione di webhook del Registro contenitori di Azure nel portale di Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Creare un webhook usando l'interfaccia della riga di comando di Azure

Per creare un webhook usando l'interfaccia della riga di comando di Azure, usare il comando [az acr webhook create](/cli/azure/acr/webhook#create).

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testare un webhook

### <a name="azure-portal"></a>Portale di Azure

Prima di usare il webhook in azioni di push o eliminazione di un'immagine del contenitore, è possibile testarlo tramite il pulsante **Ping**. Il comando Ping invia una richiesta POST generica all'endpoint specificato e registra la risposta. Ciò consente di verificare se la configurazione del webhook è corretta.

1. Selezionare il webhook da testare.
2. Nella barra degli strumenti superiore selezionare l'azione **Ping**.
3. Controllare la risposta dell'endpoint nella colonna **Stato HTTP**.

![Interfaccia utente per la creazione di webhook del Registro contenitori di Azure nel portale di Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per testare un webhook del registro contenitori di Azure con l'interfaccia della riga di comando di Azure, usare il comando [az acr webhook ping](/cli/azure/acr/webhook#ping).

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
