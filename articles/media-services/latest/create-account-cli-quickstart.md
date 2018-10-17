---
title: "Guida introduttiva: Creare un account di Servizi multimediali di Azure con l'interfaccia della riga di comando di Azure | Microsoft Docs"
description: Seguire i passaggi di questa guida introduttiva per creare un account di Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: abed9fd8d466b582b534b365f4be4257f4986435
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736003"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Guida introduttiva: Creare un account di Servizi multimediali di Azure

> [!NOTE]
> La versione più recente di Servizi multimediali (2018-03-30) è disponibile in anteprima. Questa versione è chiamata anche v3. 

Gli sviluppatori o gli autori di contenuti multimediali devono creare un account di Servizi multimediali di Azure per archiviare, crittografare, codificare, gestire ed eseguire lo streaming di contenuti multimediali in Azure. Quando si crea un account di Servizi multimediali, è necessario specificare l'ID di una risorsa dell'account di archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. Questa risorsa dell'account di archiviazione deve trovarsi nella stessa area geografica dell'account di Servizi multimediali.  

Questa guida introduttiva descrive i passaggi per la creazione di un nuovo account di Servizi multimediali di Azure tramite l'interfaccia della riga di comando di Azure.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](http://portal.azure.com) e avviare **CloudShell** per eseguire i comandi dell'interfaccia della riga di comando, come illustrato nei passaggi successivi.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessaria la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Impostare la sottoscrizione di Azure

Nel comando seguente specificare l'ID della sottoscrizione di Azure che si vuole usare per l'account di Servizi multimediali. È possibile visualizzare un elenco di sottoscrizioni a cui è possibile accedere passando a [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Creare un gruppo di risorse di Azure

Il comando seguente crea un gruppo di risorse in cui si troverà l'account di Servizi multimediali e di archiviazione. Sostituire il segnaposto *myresourcegroup* con il nome che si vuole usare per il gruppo di risorse.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure

Quando si crea un account di Servizi multimediali, è necessario specificare l'ID di una risorsa dell'account di archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. 

È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. Servizi multimediali supporta account **Utilizzo generico v2** o **Utilizzo generico v1**. Gli account di archiviazione BLOB non sono consentiti come **primari**. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../../storage/common/storage-account-overview.md). 

Il comando seguente crea l'account di archiviazione che verrà associato all'account di Servizi multimediali (primario). Nello script seguente sostituire il segnaposto *storageaccountforams*. L'oggetto "account_name" deve avere lunghezza inferiore a 24 caratteri.

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Creare un account di Servizi multimediali di Azure

Di seguito è possibile trovare i comandi dell'interfaccia della riga di comando di Azure per creare un nuovo account di Servizi multimediali. È sufficiente sostituire i valori evidenziati seguenti:

* *myamsaccountname*
* *myresourcegroup*
* *storageaccountforams*

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'intero contenuto del gruppo di risorse non è più necessario, compreso l'account di Servizi multimediali creato per questa guida introduttiva, eliminare il gruppo di risorse.

In **CloudShell** eseguire il comando seguente:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
