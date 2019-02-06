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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: b01958dc320ff6c81bb27c85ff4a5e508eabb01b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209945"
---
# <a name="create-an-azure-media-services-account"></a>Creare un account di Servizi multimediali di Azure

Per avviare le operazioni di crittografia, codifica, analisi, gestione e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Durante la creazione di un account di Servizi multimediali, viene creato anche l'account di archiviazione associato (se non si desidera usarne uno esistente).  

> [!NOTE]
> L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione nella stessa posizione dell'account di Servizi multimediali.

Questo articolo descrive i passaggi per la creazione di un account di Servizi multimediali di Azure tramite l'interfaccia della riga di comando di Azure.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

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

