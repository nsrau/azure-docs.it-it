---
title: Pubblicare app predefinite nel desktop virtuale di Windows-Azure
description: Come pubblicare app predefinite nel desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b088f1f58e26dca854c17b0765607ebe76017dfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009495"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Pubblicare app predefinite nel desktop virtuale di Windows

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/publish-apps-2019.md).

Questo articolo descrive come pubblicare app nell'ambiente desktop virtuale di Windows.

## <a name="publish-built-in-apps"></a>Pubblicare app predefinite

Per pubblicare un'app predefinita:

1. Connettersi a una delle macchine virtuali nel pool host.
2. Ottenere il **packageFamilyName** dell'app che si vuole pubblicare seguendo le istruzioni riportate in [questo articolo](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Infine, eseguire il cmdlet seguente con `<PackageFamilyName>` sostituito dal **packageFamilyName** trovato nel passaggio precedente:

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> Desktop virtuale Windows supporta solo la pubblicazione di app con i percorsi di installazione che iniziano con `C:\Program Files\WindowsApps` .

## <a name="update-app-icons"></a>Aggiorna icone app

Dopo la pubblicazione di un'app, questa avrà l'icona dell'app di Windows predefinita invece dell'immagine normale dell'icona. Per impostare l'icona sull'icona normale, inserire l'immagine dell'icona desiderata in una condivisione di rete. I formati di immagine supportati sono PNG, BMP, GIF, JPG, JPEG e ICO.

## <a name="publish-microsoft-edge"></a>Pubblicare Microsoft Edge

Il processo usato per pubblicare Microsoft Edge è leggermente diverso dal processo di pubblicazione per altre app. Per pubblicare Microsoft Edge con la Home page predefinita, eseguire il cmdlet seguente:

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare i feed per organizzare la visualizzazione delle app per gli utenti in [personalizzazione del feed per gli utenti di desktop virtuali Windows](customize-feed-for-virtual-desktop-users.md).
- Informazioni sulla funzionalità di connessione dell'app MSIX in [configurare la connessione dell'app MSIX](app-attach.md).

