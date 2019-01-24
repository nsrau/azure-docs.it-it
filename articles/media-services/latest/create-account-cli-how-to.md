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
ms.custom: seodec18
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: 9c026eb9a74cbba2ff188a3f08be625043ede474
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352054"
---
# <a name="create-an-azure-media-services-account"></a>Creare un account di Servizi multimediali di Azure

Per avviare le operazioni di crittografia, codifica, analisi, gestione e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Durante la creazione di un account di Servizi multimediali, viene creato anche l'account di archiviazione associato (se non si desidera usarne uno esistente).  

L'account di Servizi multimediali e l'account di archiviazione associato devono far parte dello stesso data center e dello stesso gruppo di risorse.

Questo articolo descrive i passaggi per la creazione di un account di Servizi multimediali di Azure tramite l'interfaccia della riga di comando di Azure.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- Installare e usare l'interfaccia della riga di comando in locale. Per questo articolo è necessaria l'interfaccia della riga di comando di Azure 2.0 o versioni successive. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

    Attualmente, non tutti i comandi dell'[interfaccia della riga di comando di Servizi multimediali v3](https://aka.ms/ams-v3-cli-ref) funzionano in Azure Cloud Shell. È consigliabile usare l'interfaccia della riga di comando in locale.

## <a name="set-the-azure-subscription"></a>Impostare la sottoscrizione di Azure

Nel comando seguente specificare l'ID della sottoscrizione di Azure che si vuole usare per l'account di Servizi multimediali. È possibile visualizzare un elenco di sottoscrizioni a cui è possibile accedere passando a [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Vedere anche 

[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

