---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Aggiungere un'applicazione a Batch | Documentazione Microsoft
description: Esempio di script dell'interfaccia della riga di comando di Azure - Aggiungere un'applicazione a Batch
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: cbfe8ab565ecf7f298a9a6c0f0c8298c675f178c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Aggiunta di applicazioni ad Azure Batch con l'interfaccia della riga di comando di Azure

Questo script illustra come configurare un'applicazione per l'uso con un pool è un'attività di Azure Batch. Per configurare un'applicazione, creare il pacchetto dell'eseguibile, insieme a eventuali dipendenze, come file .zip. In questo esempio il file zip eseguibile è chiamato "my-application-exe.zip".

## <a name="prerequisites"></a>Prerequisiti

- Installare l'interfaccia della riga di comando di Azure usando le istruzioni presenti nella [Guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), se questa operazione non è stata ancora eseguita.
- Creare un account Batch, se non è già disponibile. Vedere [Creare un account Batch con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) per uno script di esempio che crea un account.

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-application"></a>Pulizia dell'applicazione

Dopo aver eseguito lo script di esempio precedente, eseguire i comandi seguenti per rimuovere l'applicazione e tutti i relativi pacchetti dell'applicazione caricati.

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un'applicazione e caricare un pacchetto dell'applicazione.
Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_create) | Crea un'applicazione.  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_set) | Aggiorna le proprietà di un'applicazione.  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#az_batch_application_package_create) | Aggiunge un pacchetto di applicazione per l'applicazione specificata.  |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Batch sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Batch](../batch-cli-samples.md).
