---
title: Uso dell'interfaccia della riga di comando per la creazione di filtri con Servizi multimediali di Azure | Microsoft Docs
description: In questo argomento illustra come usare l'interfaccia della riga di comando per creare filtri con Servizi multimediali.
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
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 471277433f0fc9a54a28baa158f1e20f1efb613f
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000519"
---
# <a name="creating-filters-with-cli"></a>Creazione di filtri con l'interfaccia della riga di comando 

Quando si distribuiscono contenuti ai clienti (flusso di eventi dal vivo o di video on demand), il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. Per altre informazioni, vedere [Filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).

Questo argomento illustra come configurare un filtro per un asset Video on demand e usare l'interfaccia della riga di comando per Servizi multimediali v3 per creare [filtri account](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [filtri asset](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Prerequisiti 

- Installare e usare l'interfaccia della riga di comando in locale. Per questo articolo è necessaria l'interfaccia della riga di comando di Azure 2.0 o versioni successive. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

    Attualmente, non tutti i comandi dell'[interfaccia della riga di comando di Servizi multimediali v3](https://aka.ms/ams-v3-cli-ref) funzionano in Azure Cloud Shell. È consigliabile usare l'interfaccia della riga di comando in locale.
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali. 
- Leggere [Filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).

## <a name="define-a-filter"></a>Definire un filtro 

Di seguito è riportato l'esempio che definisce le condizioni di selezione di traccia che vengono aggiunte al manifesto finale. Questo filtro include tracce audio EC-3 e tracce video con velocità in bit nell'intervallo 0-1000000.

I filtri definiti in REST includono l'oggetto JSON wrapper "Properties".  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>Creare filtri account

Il comando [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) seguente crea un filtro account con le selezioni di traccia del filtro [definite in precedenza](#define-a-filter). 

Questo comando consente di superare il parametro `--tracks` facoltativo che include JSON per le selezioni di traccia.  Usare @{file} per caricare JSON da un file. Se si usa l’interfaccia della riga di comando di Azure in locale, specificare il percorso completo del file:


```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @c:\tracks.json
```

Se si usa Azure Cloud Shell, caricare il file in Cloud Shell (cercare il pulsante Carica/Scarica file nella parte superiore della finestra di Shell). Quindi, è possibile fare riferimento al file come indicato di seguito:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Creare filtri asset

Il comando [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) seguente crea un filtro asset con le selezioni di traccia del filtro [definite in precedenza](#define-a-filter). 

> [!TIP]
> Vedere le informazioni su come specificare il percorso del nome del file nella sezione precedente.

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Vedere anche [Esempi JSON per i filtri](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="next-step"></a>Passaggio successivo

[Video in streaming](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Vedere anche 

[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
