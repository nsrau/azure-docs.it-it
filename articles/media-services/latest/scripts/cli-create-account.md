---
title: "Esempio di script dell'interfaccia della riga di comando di Azure: creare un account di Servizi multimediali di Azure | Microsoft Docs"
description: Usare lo script dell'interfaccia della riga di comando di Azure per creare un account di Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 783dc0fd37f98a12d9240ad4b3ee72aa98212eff
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159714"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>Esempio dell'interfaccia della riga di comando: creare un account di Servizi multimediali di Azure

Lo script dell'interfaccia della riga di comando di Azure in questo argomento mostra come creare un account di Servizi multimediali di Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.20 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Crea un account di archiviazione. |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | Crea un account di Servizi multimediali. |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | Crea un'entità servizio con password e configura l'accesso a un account di Servizi multimediali di Azure. 
| [az group delete](/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |


## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere [Azure CLI samples](../cli-samples.md) (Esempi dell'interfaccia della riga di comando di Azure).
