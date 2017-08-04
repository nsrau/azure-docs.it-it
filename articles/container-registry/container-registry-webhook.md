---
title: Webhook del registro contenitori di Azure | Microsoft Docs
description: Webhook del registro contenitori di Azure
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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d0190f5725671c320d92b897f0dcef7a526a86e3
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="using-azure-container-registry-webhooks---azure-portal"></a>Utilizzo dei webhook del registro contenitori di Azure - Portale di Azure

Un registro contenitori di Azure archivia e gestisce le immagini dei contenitori Docker private, in modo analogo a come Docker Hub archivia le immagini Docker pubbliche. Usando i webhook, è possibile attivare eventi specifici quando in uno dei repository del registro si verificano determinate azioni. I webhook possono rispondere agli eventi a livello di registro oppure possono essere limitati a un tag di repository specifico. 

Per altre informazioni di base e concetti, vedere la [panoramica](./container-registry-intro.md).

## <a name="prerequisites"></a>Prerequisiti 

- Un registro contenitori gestito in Azure: creare un registro contenitori gestito nella sottoscrizione di Azure usando, ad esempio, il portale di Azure o l'interfaccia della riga di comando di Azure 2.0. 
- Interfaccia della riga di comando di Docker: per configurare il computer locale come host Docker e accedere ai comandi della riga di comando di Docker, installare Docker Engine. 

## <a name="create-webhook-azure-portal"></a>Creare un webhook tramite il portale di Azure

1. Accedere al portale di Azure e passare al registro in cui si vuole creare i webhook. 

2. Nel pannello del contenitore selezionare la scheda "Webhook". 

3. Selezionare "Aggiungi" nella barra degli strumenti del pannello relativo al webhook. 

4. Completare il modulo *Create Webhook* (Crea webhook) con le informazioni seguenti:

| Valore | Descrizione |
|---|---|
| Nome | Il nome da assegnare al webhook. Può contenere solo lettere minuscole e numeri e avere una lunghezza compresa tra 5 e 50 caratteri. |
| URI del servizio | L'URI in cui il webhook deve inviare le notifiche POST. |
| Intestazioni personalizzate | Le intestazioni che si vuole passare insieme alla richiesta POST. Devono essere nel formato "chiave: valore". |
| Azioni trigger | Le azioni che attivano il webhook. Attualmente i webhook possono essere attivati tramite azioni di push e/o eliminazione in un'immagine. |
| Stato | Lo stato del webhook dopo che è stato creato. Questa opzione è abilitata per impostazione predefinita. |
| Scope | L'ambito in cui viene applicato il webhook. Per impostazione predefinita, l'ambito comprende tutti gli eventi del registro. Può essere limitato a un repository o un tag usando il formato "repository: tag". |

Modulo di webhook di esempio:

![Interfaccia utente di DC/OS](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Creare un webhook usando l'interfaccia della riga di comando di Azure

Per creare un webhook usando l'interfaccia della riga di comando di Azure, usare il comando [az acr webhook create](/cli/azure/acr/webhook#create).

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testare un webhook

### <a name="azure-portal"></a>Portale di Azure

Prima di usare il webhook in azioni di push o eliminazione di un'immagine del contenitore, è possibile testarlo tramite il pulsante **Ping**. Quando viene usato, il comando Ping invia una richiesta POST generica all'endpoint specificato e registra la risposta. In questo modo è possibile verificare che il webhook sia stato configurato correttamente.

1. Selezionare il webhook da testare. 
2. Nella barra degli strumenti superiore selezionare l'azione "Ping". 
3. Verificare la richiesta e la risposta.

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

Per eliminare un webhook, è possibile selezionarlo e usare il pulsante di eliminazione disponibile nel portale di Azure.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```
