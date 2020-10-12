---
title: Installare il pacchetto Rendering remoto per Unity
description: Spiega come installare le dll client per il rendering remoto per Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3704d1a418baeec18c3303b8203a0185790cbcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564310"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Installare il pacchetto Rendering remoto per Unity

Il rendering remoto di Azure usa un pacchetto Unity per incapsulare l'integrazione in Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Gestire i pacchetti di rendering remoto in Unity

I pacchetti Unity sono contenitori che possono essere gestiti tramite [Gestione pacchetti](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)di Unity.
Questo pacchetto contiene l'intera API C# e tutti i file binari dei plug-in necessari per usare il rendering remoto di Azure con Unity.
In seguito allo schema di denominazione di Unity per i pacchetti, il pacchetto è denominato **com. Microsoft. Azure. Remote-rendering**.

Il pacchetto non fa parte del [repository di esempi arr](https://github.com/Azure/azure-remote-rendering)e non è disponibile nel registro di sistema dei pacchetti interni di Unity. Per aggiungerlo a un progetto, è necessario modificare manualmente il file del progetto `manifest.md` per aggiungere quanto segue:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.31",
    ...existing dependencies...
  }
}
```

Una volta aggiunta, è possibile usare Gestione pacchetti Unity per assicurarsi di avere la versione più recente.
Nell'esercitazione vengono fornite istruzioni più complete [: Visualizza modelli remoti](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="unity-render-pipelines"></a>Pipeline di rendering Unity

Il rendering remoto funziona sia con sia con **:::no-loc text="Universal render pipeline":::** **:::no-loc text="Standard render pipeline":::** . Per motivi di prestazioni, è consigliabile usare la pipeline di rendering universale.

Per usare **:::no-loc text="Universal render pipeline":::** , il pacchetto deve essere installato in Unity. Questa operazione può essere eseguita nell'interfaccia utente di **Gestione pacchetti** di Unity (nome pacchetto **universale RP**, versione 7.3.1 o successiva) o tramite il `Packages/manifest.json` file, come descritto nell' [esercitazione sull'installazione del progetto Unity](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Passaggi successivi

* [Oggetti e componenti di gioco Unity](objects-components.md)
* [Esercitazione: visualizzare i modelli remoti](../../tutorials/unity/view-remote-models/view-remote-models.md)
