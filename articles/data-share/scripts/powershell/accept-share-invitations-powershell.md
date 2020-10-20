---
title: "Script di PowerShell: accettare l'invito da una condivisione dati di Azure"
description: Questo script di PowerShell accetta gli inviti da una condivisione dati esistente.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 24091bafac7ad5c558b975d52064f12715b3d622
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221401"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Usare PowerShell per accettare un invito alla condivisione dati

Questo script di PowerShell accetta gli inviti inviati a un consumer.

## <a name="sample-script"></a>Script di esempio
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Ottenere ed elencare gli inviti di condivisione dati inviati. |
| [New-AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription) | Creare una sottoscrizione per la condivisione di dati. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).

Altri esempi di script di PowerShell per la condivisione di dati di Azure sono disponibili negli esempi di PowerShell per la [condivisione di dati di Azure](../../samples-powershell.md).
