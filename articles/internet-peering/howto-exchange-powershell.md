---
title: Creare o modificare un peering di Exchange con PowerShell
titleSuffix: Azure
description: Creare o modificare un peering di Exchange con PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774406"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Creare o modificare un peering di Exchange con PowerShell

Questo articolo descrive come creare un peering di Microsoft Exchange usando i cmdlet di PowerShell e il modello di distribuzione Gestione risorse. Questo articolo illustra anche come controllare lo stato della risorsa, aggiornarla o eliminarla ed effettuarne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata per il peering di Exchange](walkthrough-exchange-all.md) prima di iniziare la configurazione.
* Se sono già presenti peering di Exchange con Microsoft, che non vengono convertiti in risorse di Azure, vedere [convertire un peering Exchange legacy in una risorsa di Azure tramite PowerShell](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Creare ed effettuare il provisioning di un peering Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=exchange-location></a>Ottenere l'elenco dei percorsi di peering supportati per il peering di Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name=create></a>Creare un peering di Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name=get></a>Ottenere il peering di Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify"></a>Modificare un peering di Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name=delete></a>Effettuare il deprovisioning di un peering di Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto usando PowerShell](howto-direct-powershell.md)
* [Convertire un peering diretto legacy in una risorsa di Azure usando PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per altre informazioni, vedere [domande frequenti sul peering Internet](faqs.md)
