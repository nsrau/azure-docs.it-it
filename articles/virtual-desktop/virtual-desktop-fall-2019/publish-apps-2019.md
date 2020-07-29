---
title: Pubblicare app predefinite in desktop virtuale di Windows (versione classica)-Azure
description: Come pubblicare app predefinite in desktop virtuale di Windows (versione classica).
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f204bc6d2595e7f18e59bae15ff35d49e1801e0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284932"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop-classic"></a>Pubblicare app predefinite in desktop virtuale di Windows (versione classica)

>[!IMPORTANT]
>Questo contenuto si applica a desktop virtuale Windows (classico), che non supporta Azure Resource Manager oggetti desktop virtuali di Windows. Se si sta tentando di gestire Azure Resource Manager oggetti desktop virtuali di Windows, vedere [questo articolo](../publish-apps.md).

Questo articolo descrive come pubblicare app nell'ambiente desktop virtuale di Windows.

## <a name="publish-built-in-apps"></a>Pubblicare app predefinite

Per pubblicare un'app predefinita:

1. Connettersi a una delle macchine virtuali nel pool host.
2. Ottenere il **packageFamilyName** dell'app che si vuole pubblicare seguendo le istruzioni riportate in [questo articolo](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Infine, eseguire il cmdlet seguente con `<PackageFamilyName>` sostituito dal **packageFamilyName** trovato nel passaggio precedente:

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Desktop virtuale Windows supporta solo la pubblicazione di app con i percorsi di installazione che iniziano con `C:\Program Files\Windows Apps` .

## <a name="update-app-icons"></a>Aggiorna icone app

Dopo la pubblicazione di un'app, questa avrà l'icona dell'app di Windows predefinita invece dell'immagine normale dell'icona. Per impostare l'icona sull'icona normale, inserire l'immagine dell'icona desiderata in una condivisione di rete. I formati di immagine supportati sono PNG, BMP, GIF, JPG, JPEG e ICO.

## <a name="publish-microsoft-edge"></a>Pubblicare Microsoft Edge

Il processo usato per pubblicare Microsoft Edge è leggermente diverso dal processo di pubblicazione per altre app. Per pubblicare Microsoft Edge con la Home page predefinita, eseguire il cmdlet seguente:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge"
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare i feed per organizzare la visualizzazione delle app per gli utenti in [personalizzazione del feed per gli utenti di desktop virtuali Windows](customize-feed-virtual-desktop-users-2019.md).
- Informazioni sulla funzionalità di connessione dell'app MSIX in [configurare la connessione dell'app MSIX](../app-attach.md).

