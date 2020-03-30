---
title: 'Script di PowerShell: elencare gli inviti alla condivisione dati di Azure inviati a un consumer. Documenti Microsoft'
description: Questo script di PowerShell accetta gli inviti da una condivisione dati esistente.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 157b844afd9d59b6c03c7ae8585bc2182370d3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307142"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Usare PowerShell per ottenere un invito alla condivisione datiUse PowerShell to get a data share invitation

Questo script di PowerShell ottiene gli inviti inviati a un consumer.

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
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Ottenere ed elencare gli inviti alla condivisione dati inviati. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per la condivisione dati di Azure sono disponibili negli esempi di [Azure Data Share powerShell.Additional Azure Data Share PowerShell](../../samples-powershell.md)script samples can be found in the Azure Data Share PowerShell samples.
