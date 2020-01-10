---
title: Creare o modificare un peering diretto usando PowerShell
titleSuffix: Azure
description: Creare o modificare un peering diretto usando PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774237"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Creare o modificare un peering diretto usando PowerShell

Questo articolo descrive come creare un peering Microsoft diretto usando i cmdlet di PowerShell e il modello di distribuzione Gestione risorse. Questo articolo illustra anche come controllare lo stato della risorsa, aggiornarla o eliminarla ed effettuarne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-direct-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata di peering diretto](walkthrough-direct-all.md) .
* Se si ha già il peering diretto con Microsoft, che non vengono convertite in risorse di Azure, vedere [convertire un peering diretto legacy in una risorsa di Azure usando PowerShell](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Creare ed effettuare il provisioning di un peering diretto

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=direct-location></a>Ottenere l'elenco dei percorsi di peering supportati per il peering diretto
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name=create></a>Creare un peering diretto
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name=get></a>Verificare il peering diretto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify"></a>Modificare un peering diretto
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="delete"></a>Effettuare il deprovisioning di un peering diretto
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange usando PowerShell](howto-exchange-powershell.md).
* [Convertire un peering di Exchange legacy in una risorsa di Azure usando PowerShell](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per altre informazioni, vedere [domande frequenti sul peering Internet](faqs.md)
