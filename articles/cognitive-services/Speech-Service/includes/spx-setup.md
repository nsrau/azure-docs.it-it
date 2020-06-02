---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c57187ae15e5f15a601edbc74921b3c879abe715
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800684"
---
## <a name="prerequisites"></a>Prerequisiti

L'unico prerequisito è una sottoscrizione a Voce di Azure. Vedere la [guida ](../get-started.md#new-resource) sulla creazione di una nuova sottoscrizione, se non ne è già disponibile una.

## <a name="download-and-install"></a>Download e installazione

#### <a name="windows-install"></a>[Installazione di Windows](#tab/windowsinstall)

Per installare l’interfaccia della riga di comando di Voce su Windows, seguire questa procedura:

1. Installare [.NET Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) o [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Scaricare l'[archivio zip](https://aka.ms/speech/spx-zips.zip) dell’interfaccia della riga di comando di Voce, quindi estrarlo.
3. Passare alla directory radice `spx-zips` estratta dal download ed estrarre la sottodirectory necessaria (`spx-net471` per .NET Framework 4.7 o `spx-netcore-win-x64` per .NET Core 3.0 su una CPU x64).

Nel prompt dei comandi, passare alla directory in questo percorso, quindi digitare `spx` per visualizzare la guida per l'interfaccia della riga di comando di Voce.

#### <a name="linux-install"></a>[Installazione di Linux](#tab/linuxinstall)

Per installare l’interfaccia della riga di comando di Voce su Linux su una CPU x64, seguire questa procedura:

1. Installare [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Scaricare l'[archivio zip](https://aka.ms/speech/spx-zips.zip) dell’interfaccia della riga di comando di Voce, quindi estrarlo.
3. Passare alla directory radice `spx-zips` estratta dal download ed estrarre `spx-netcore-30-linux-x64` in una nuova directory `~/spx`.
4. In un terminale digitare i comandi seguenti:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Digitare `spx` per visualizzare la guida per l'interfaccia della riga di comando di Voce.

***

## <a name="create-subscription-config"></a>Creare la configurazione della sottoscrizione

Per iniziare a usare l'interfaccia della riga di comando di Voce, è necessario innanzitutto immettere le informazioni sull’area e la chiave di sottoscrizione a Voce. Per trovare l'identificatore di area, vedere la pagina del [supporto a livello di area](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk). Dopo aver ottenuto l’identificatore dell’area e della chiave di sottoscrizione (ad esempio, `eastus`, `westus`), eseguire i comandi seguenti.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

L'autenticazione della sottoscrizione è ora archiviata per richieste SPX future. Se è necessario rimuovere uno di questi valori archiviati, eseguire `spx config @region --clear` o `spx config @key --clear`.
