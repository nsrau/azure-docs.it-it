---
title: Rendere persistenti i file in PowerShell in Azure Cloud Shell (anteprima) | Microsoft Docs
description: Procedura dettagliata su come Azure Cloud Shell rende persistenti i file.
services: azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: b87c4a408393e4ae341898e8cfa23e9acbcb4fc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154399"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Come funziona PowerShell in Azure Cloud Shell (anteprima)
PowerShell in Cloud Shell (anteprima) rende persistenti i file tramite il metodo seguente: 
* Montaggio della condivisione file di Azure specificata come `clouddrive` nella directory `$Home` per l'interazione diretta con la condivisione file.

## <a name="list-clouddrive-azure-file-shares"></a>Elencare le condivisioni file di Azure `clouddrive`
Il comando `Get-CloudDrive` recupera le informazioni sulle condivisioni file di Azure attualmente montate da `clouddrive` in Cloud Shell. <br>
![Esecuzione di Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-clouddrive"></a>Smontare `clouddrive`
È possibile smontare in qualsiasi momento una condivisione file di Azure montata in Cloud Shell. Se la condivisione file di Azure è stata rimossa, alla sessione successiva verrà chiesto di crearne e montarne una nuova.

Il comando `Dismount-CloudDrive` smonta una condivisione file di Azure dall'account di archiviazione corrente. Se si smonta `clouddrive`, viene terminata la sessione corrente. All'utente viene chiesto di creare e montare una nuova condivisione file di Azure durante la sessione successiva.
![Esecuzione di Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di PowerShell](quickstart-powershell.md) <br>
[Informazioni su File di Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Informazioni sui tag di archiviazione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>