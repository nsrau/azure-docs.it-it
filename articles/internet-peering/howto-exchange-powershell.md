---
title: Creare o modificare un peering di Exchange tramite PowerShell
titleSuffix: Azure
description: Creare o modificare un peering di Exchange tramite PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 23c905f148da614c7785b61b76abed191206cd90
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678634"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Creare o modificare un peering di Exchange tramite PowerShell

Questo articolo descrive come creare un peering di Microsoft Exchange usando i cmdlet di PowerShell e il modello di distribuzione Gestione risorse. Questo articolo illustra anche come controllare lo stato della risorsa, aggiornarla o eliminarla ed effettuarne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-exchange-portal.md)di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata per il peering di Exchange](walkthrough-exchange-all.md) .
* Se si dispone già di peering di Exchange con Microsoft che non vengono convertite in risorse di Azure, vedere [convertire un peering Exchange legacy in una risorsa di Azure tramite PowerShell](howto-legacy-exchange-powershell.md).

### <a name="work-with-azure-powershell"></a>Usare Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Creare ed effettuare il provisioning di un peering Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Ottenere l'elenco dei percorsi di peering supportati per il peering di Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Creare un peering di Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Ottenere il peering di Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificare un peering di Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Effettuare il deprovisioning di un peering di Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto usando PowerShell](howto-direct-powershell.md)
* [Convertire un peering diretto legacy in una risorsa di Azure tramite PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per altre informazioni, vedere [domande frequenti sul peering Internet](faqs.md).
