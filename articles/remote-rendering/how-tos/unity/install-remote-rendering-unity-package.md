---
title: Installare il pacchetto di rendering remoto per UnityInstall the Remote Rendering package for Unity
description: Viene illustrato come installare le DLL del client di rendering remoto per Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681181"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Installare il pacchetto di rendering remoto per UnityInstall the Remote Rendering package for Unity

Azure Remote Rendering uses a Unity package to encapsulate the integration into Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Gestire i pacchetti di rendering remoto in UnityManage the remote rendering packages in Unity

I pacchetti Unity sono contenitori che possono essere gestiti tramite [Gestione pacchetti](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)di Unity.
Questo pacchetto contiene l'intera API di C, nonché tutti i file binari del plug-in necessari per usare il rendering remoto di Azure con Unity.This package contains the all' API as' and all plugin binaries required to use Azure Remote Rendering with Unity.
Seguendo lo schema di denominazione di Unity per i pacchetti, il pacchetto è denominato **com.microsoft.azure.remote-rendering**.

Il pacchetto non fa parte del repository di [esempi ARR](https://github.com/Azure/azure-remote-rendering)e non è disponibile nel registro dei pacchetti interni di Unity. Per aggiungerlo a un progetto, è necessario `manifest.md` modificare manualmente il file del progetto per aggiungere quanto segue:
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
Una volta che questo è stato aggiunto, è possibile utilizzare Unity Package Manager per assicurarsi di avere la versione più recente.
Istruzioni più complete sono fornite nel [Tutorial: Impostazione](../../tutorials/unity/project-setup.md)di un progetto Unity da zero .

## <a name="unity-render-pipelines"></a>Pipeline di rendering Unity

Il rendering remoto funziona sia con la pipeline di **rendering universale** che con la pipeline di **rendering standard.** Per motivi di prestazioni, è consigliabile la pipeline di rendering universale.

Per utilizzare la **pipeline di rendering universale**, il relativo pacchetto deve essere installato in Unity. Questa operazione può essere eseguita nell'interfaccia utente di **Gestione pacchetti** di Unity (nome del `Packages/manifest.json` pacchetto Universal **RP**, versione 7.2.1 o successiva) o tramite il file, come descritto nell'esercitazione sull'installazione del progetto [Unity](../../tutorials/unity/project-setup.md#configure-the-projects-manifest).

## <a name="next-steps"></a>Passaggi successivi

* [Unity oggetti di gioco e componenti](objects-components.md)
* [Esercitazione: Impostazione di un progetto Unity da zeroTutorial: Setting up a Unity project from scratch](../../tutorials/unity/project-setup.md)
