---
title: Ridimensionamento di Media Reserved Unit - Azure | Microsoft Docs
description: Questo argomento presenta una panoramica del ridimensionamento dell'elaborazione multimediale con Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: dd587e5fc2082d1e496fbc05d5b25cf6692413bc
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713062"
---
# <a name="scaling-media-processing"></a>Ridimensionamento dell'elaborazione di contenuti multimediali

Servizi multimediali di Azure consente di ridimensionare l'elaborazione di contenuti multimediali nell'account gestendo le Media Reserved Unit (MRU). Per una panoramica dettagliata, vedere [Ridimensionamento dell'elaborazione di contenuti multimediali](../previous/media-services-scale-media-processing-overview.md). 

Questo articolo illustra come usare l'[interfaccia della riga di comando di Servizi multimediali v3](https://aka.ms/ams-v3-cli-ref) per ridimensionare le MRU.

> [!NOTE]
> Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, è fortemente consigliato effettuare il provisioning dell'account con 10 MRU S3. <br/>Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Prerequisiti 

- Installare e usare l'interfaccia della riga di comando in locale. Per questo articolo è necessaria l'interfaccia della riga di comando di Azure 2.0 o versioni successive. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

    Attualmente, non tutti i comandi dell'[interfaccia della riga di comando di Servizi multimediali v3](https://aka.ms/ams-v3-cli-ref) funzionano in Azure Cloud Shell. È consigliabile usare l'interfaccia della riga di comando in locale.

- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Ridimensionare le Media Reserved Unit con l'interfaccia della riga di comando

Il comando [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) seguente imposta le Media Reserved Unit sull'account "amsaccount" usando i parametri **count** e **type**.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fatturazione

L'addebito è basato sul numero, il tipo e il periodo di tempo in cui viene effettuato il provisioning delle MRU nell'account. I costi si applicano indipendentemente dal fatto che si eseguano processi o meno. Per una spiegazione dettagliata, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Passaggio successivo

[Analizzare i video](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Vedere anche 

[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
