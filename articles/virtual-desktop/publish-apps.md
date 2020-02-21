---
title: Pubblicare app predefinite nel desktop virtuale di Windows-Azure
description: Come pubblicare app predefinite nel desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
ms.openlocfilehash: e8e8512fa9f6a64fb0db688b7badcb699f6ccaa5
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484622"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Pubblicare app predefinite nel desktop virtuale di Windows

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
> Desktop virtuale Windows supporta solo la pubblicazione di app con i percorsi di installazione che iniziano con `C:\Program Files\Windows Apps`.

## <a name="update-app-icons"></a>Aggiorna icone app

Dopo la pubblicazione di un'app, questa avrà l'icona dell'app di Windows predefinita invece dell'immagine normale dell'icona. Per impostare l'icona sull'icona normale, inserire l'immagine dell'icona desiderata in una condivisione di rete. I formati di immagine supportati sono PNG, BMP, GIF, JPG, JPEG e ICO.

## <a name="publish-microsoft-edge"></a>Pubblicare Microsoft Edge

Il processo usato per pubblicare Microsoft Edge è leggermente diverso dal processo di pubblicazione per altre app. Per pubblicare Microsoft Edge con la Home page predefinita, eseguire il cmdlet seguente:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare i feed per organizzare la visualizzazione delle app per gli utenti in [personalizzazione del feed per gli utenti di desktop virtuali Windows](customize-feed-for-virtual-desktop-users.md).
- Informazioni sulla funzionalità di connessione dell'app MSIX in [configurare la connessione dell'app MSIX](app-attach.md).

