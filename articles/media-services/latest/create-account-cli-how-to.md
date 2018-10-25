---
title: Creare un account Servizi multimediali di Azure con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Seguire i passaggi di questa guida introduttiva per creare un account di Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 988b56569c2537f9f0c74b1bcc2bf5e2a9400a80
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378806"
---
# <a name="create-an-azure-media-services-account"></a>Creare un account di Servizi multimediali di Azure

Per avviare le operazioni di crittografia, codifica, analisi, gestione e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Durante la creazione di un account di Servizi multimediali, viene creato l'account di archiviazione associato (se non si desidera utilizzarne uno esistente) nella stessa area geografica dell'account di Servizi multimediali.

Questo articolo descrive i passaggi per la creazione di un account di Servizi multimediali di Azure tramite l'interfaccia della riga di comando di Azure.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](http://portal.azure.com) e avviare **CloudShell** per eseguire i comandi dell'interfaccia della riga di comando, come illustrato nei passaggi successivi.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessaria l'interfaccia della riga di comando di Azure 2.0 o versione successiva. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Impostare la sottoscrizione di Azure

Nel comando seguente specificare l'ID della sottoscrizione di Azure che si vuole usare per l'account di Servizi multimediali. È possibile visualizzare un elenco di sottoscrizioni a cui è possibile accedere passando a [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
