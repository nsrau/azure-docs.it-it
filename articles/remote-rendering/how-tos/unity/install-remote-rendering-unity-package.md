---
title: Installare il pacchetto Rendering remoto per Unity
description: Spiega come installare le dll client per il rendering remoto per Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681181"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Installare il pacchetto Rendering remoto per Unity

Il rendering remoto di Azure usa un pacchetto Unity per incapsulare l'integrazione in Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Gestire i pacchetti di rendering remoto in Unity

I pacchetti Unity sono contenitori che possono essere gestiti tramite [Gestione pacchetti](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)di Unity.
Questo pacchetto contiene l'intera API C# e tutti i file binari dei plug-in necessari per usare il rendering remoto di Azure con Unity.
In seguito allo schema di denominazione di Unity per i pacchetti, il pacchetto è denominato **com. Microsoft. Azure. Remote-rendering**.

Il pacchetto non fa parte del [repository di esempi arr](https://github.com/Azure/azure-remote-rendering)e non è disponibile nel registro di sistema dei pacchetti interni di Unity. Per aggiungerlo a un progetto, è necessario modificare manualmente il `manifest.md` file del progetto per aggiungere quanto segue:
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
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
Una volta aggiunta, è possibile usare Gestione pacchetti Unity per assicurarsi di avere la versione più recente.
Nell'esercitazione vengono fornite istruzioni più complete [: configurazione di un progetto Unity da zero](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Pipeline di rendering Unity

Il rendering remoto funziona sia con la pipeline di rendering **universale** sia con la **pipeline di rendering standard**. Per motivi di prestazioni, è consigliabile usare la pipeline di rendering universale.

Per usare la **pipeline di rendering universale**, il pacchetto deve essere installato in Unity. Questa operazione può essere eseguita nell'interfaccia utente di **Gestione pacchetti** di Unity (nome pacchetto **universale RP**, versione 7.2.1 o successiva) o `Packages/manifest.json` tramite il file, come descritto nell' [esercitazione sull'installazione del progetto Unity](../../tutorials/unity/project-setup.md#configure-the-projects-manifest).

## <a name="next-steps"></a>Passaggi successivi

* [Oggetti e componenti di gioco Unity](objects-components.md)
* [Esercitazione: Configurazione di un progetto Unity da zero](../../tutorials/unity/project-setup.md)
