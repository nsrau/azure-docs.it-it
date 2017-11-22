---
title: Rendere persistenti i file in PowerShell in Azure Cloud Shell (anteprima) | Microsoft Docs
description: Procedura dettagliata su come Azure Cloud Shell rende persistenti i file.
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: damaerte
ms.openlocfilehash: 66d0e20d670e49cdfe64614d1fc6f5739fde6155
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Come funziona PowerShell in Azure Cloud Shell (anteprima)
PowerShell in Cloud Shell (anteprima) rende persistenti i file tramite il metodo seguente: 
* Montaggio della condivisione file specificata come `clouddrive` nella directory `$Home` per l'interazione diretta con la condivisione file.

## <a name="list-cloud-drive-file-shares"></a>Elencare le condivisioni file sull'unità Cloud
Il comando `Get-CloudDrive` consente di recuperare le informazioni sulle condivisioni file attualmente montate dall'unità Cloud in Cloud Shell. <br>
![Esecuzione di Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>Smontare l'unità Cloud
È possibile smontare in qualsiasi momento una condivisione file montata in Cloud Shell. Se la condivisione file è stata rimossa, alla sessione successiva verrà chiesto di crearne e montarne una nuova.

Il comando `Dismount-CloudDrive` consente di smontare una condivisione file dall'account di archiviazione corrente. Se si smonta l'unità Cloud, viene terminata la sessione corrente. All'utente viene chiesto di creare e montare una nuova condivisione file durante la sessione successiva.
![Esecuzione di Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di PowerShell](quickstart-powershell.md) <br>
[Informazioni sull'archiviazione file di Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Informazioni sui tag di archiviazione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>