---
title: Creare un registro per contenitori Docker privati - Portale di Azure | Microsoft Docs
description: Introduzione alla creazione e gestione dei registri per contenitori Docker privati con il portale di Azure
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>Creare un registro contenitori gestito con il portale di Azure

Registro contenitori di Azure è un servizio gestito di registri contenitori Docker usato per l'archiviazione di immagini di un contenitore Docker privato. Questa guida descrive la creazione di un'istanza gestita di Registro contenitori di Azure con il portale di Azure.

I registri contenitori di Azure gestiti sono in anteprima e non disponibili in tutte le aree di Azure.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-container-registry"></a>Creare un registro di contenitori

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

2. Cercare **Registro contenitori di Azure** nel Marketplace e selezionarlo.

3. Fare clic su **Crea** per visualizzare il pannello di creazione dell'istanza di Registro contenitori di Azure.

    ![Impostazioni di Registro contenitori](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. Nel pannello **Registro contenitori di Azure** immettere le informazioni seguenti. Al termine dell'operazione fare clic su **Crea**.

    a. **Registry name** (Nome registro): nome di dominio di primo livello univoco globale per il registro. In questo esempio, il nome del registro è *myAzureContainerRegistry1*, ma è necessario sostituirlo con un nome univoco personalizzato. Il nome può contenere solo lettere e numeri.

    b. **Resource group** (Gruppo di risorse): selezionare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo.

    c. **Location** (Posizione): selezionare la posizione di un data center di Azure in cui il servizio è [disponibile](https://azure.microsoft.com/regions/services/), ad esempio **Stati Uniti centro-meridionali**.

    d. **Admin user** (Utente amministratore): consentire eventualmente a un utente amministratore di accedere al registro. È possibile modificare questa impostazione dopo aver creato il registro.

    e. **Usa registro gestito**: selezionare Sì in modo che Registro contenitori di Azure gestisca automaticamente l'archiviazione del registro e usi webhook e l'autenticazione AAD.

    f. **Piano tariffario**: selezionare un piano tariffario. Vedere le informazioni sui prezzi di Registro contenitori di Azure per altre informazioni.

## <a name="log-in-to-acr-instance"></a>Accedere all'istanza di Registro contenitori di Azure

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere all'istanza di Registro contenitori di Azure. 

A tale scopo usare l'interfaccia della riga di comando di Azure 2.0. Se necessario, accedere prima ad Azure con il comando [az login](/cli/azure/#login). 

```azurecli
az login
```

Usare quindi il comando [az acr login](/cli/azure/acr#login) per accedere a Registro contenitori di Azure.

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

## <a name="use-azure-container-registry"></a>Usare Registro contenitori di Azure

### <a name="list-container-images"></a>Elencare le immagini del contenitore

Usare i comandi dell'interfaccia della riga di comando `az acr` per eseguire query su immagini e tag in un repository.

> [!NOTE]
> Attualmente, il servizio Container Registry non supporta il comando `docker search` per eseguire query relative a immagini e tag.

### <a name="list-repositories"></a>Elencare repository

L'esempio seguente elenca i repository in un registro, in formato JSON (JavaScript Object Notation):

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Elencare tag

L'esempio seguente elenca i tag sul repository **samples/nginx**, in formato JSON:

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'istanza gestita di Registro contenitori di Azure con il portale di Azure.

> [!div class="nextstepaction"]
> [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md)
