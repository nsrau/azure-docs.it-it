---
title: 'Script di PowerShell: elencare gli inviti di condivisione dati di Azure inviati a un consumer'
description: Informazioni su come questo script di PowerShell riceve gli inviti inviati a un consumer e visualizza un esempio dello script che è possibile usare.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 5f75894094fa2a15dbc9e2809ed6c3631df96c3f
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221214"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Usare PowerShell per ottenere un invito alla condivisione dati

Questo script di PowerShell consente di ottenere gli inviti inviati a un consumer.

## <a name="sample-script"></a>Script di esempio
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Ottenere ed elencare gli inviti di condivisione dati inviati. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).

Altri esempi di script di PowerShell per la condivisione di dati di Azure sono disponibili negli esempi di PowerShell per la [condivisione di dati di Azure](../../samples-powershell.md).
