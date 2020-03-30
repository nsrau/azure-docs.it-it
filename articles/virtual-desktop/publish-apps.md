---
title: Pubblicare app predefinite in Windows Virtual Desktop - Azure
description: Come pubblicare app predefinite in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127746"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Pubblicare app predefinite in Windows Virtual Desktop

Questo articolo ti dirà come pubblicare app nel tuo ambiente Windows Virtual Desktop.

## <a name="publish-built-in-apps"></a>Pubblicare app predefinite

Per pubblicare un'app predefinita:

1. Connettersi a una delle macchine virtuali nel pool host.
2. Ottenere **PackageFamilyName** dell'app che si desidera pubblicare seguendo le istruzioni riportate in [questo articolo.](/powershell/module/appx/get-appxpackage?view=win10-ps/)
3. Infine, eseguire il `<PackageFamilyName>` cmdlet seguente con sostituito dal **PackageFamilyName** trovato nel passaggio precedente:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows Virtual Desktop supporta solo la pubblicazione `C:\Program Files\Windows Apps`di app con percorsi di installazione che iniziano con .

## <a name="update-app-icons"></a>Aggiornare le icone delle app

Dopo aver pubblicato un'app, avrà l'icona predefinita dell'app di Windows anziché l'immagine dell'icona normale. Per modificare l'icona con l'icona normale, inserire l'immagine dell'icona desiderata in una condivisione di rete. I formati di immagine supportati sono PNG, BMP, GIF, JPG, JPEG e ICO.

## <a name="publish-microsoft-edge"></a>Pubblicare Microsoft Edge

Il processo utilizzato per pubblicare Microsoft Edge è leggermente diverso dal processo di pubblicazione per altre app. Per pubblicare Microsoft Edge con la home page predefinita, eseguire questo cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare i feed per organizzare la modalità di visualizzazione delle app per gli utenti in [Customize feed for Windows Virtual Desktop users](customize-feed-for-virtual-desktop-users.md).
- Per informazioni sulla funzionalità di collegamento dell'app MSIX, [vedere Configurare l'app MSIX attach](app-attach.md).

