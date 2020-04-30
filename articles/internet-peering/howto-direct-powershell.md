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
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680814"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Creare o modificare un peering diretto usando PowerShell

Questo articolo descrive come creare un peering Microsoft diretto usando i cmdlet di PowerShell e il modello di distribuzione Azure Resource Manager. Questo articolo illustra anche come controllare lo stato della risorsa, aggiornarla o eliminarla ed effettuarne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-direct-portal.md)di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata relativa al peering diretto](walkthrough-direct-all.md) .
* Se si hanno già connessioni peering dirette con Microsoft che non vengono convertite in risorse di Azure, vedere [convertire un peering diretto legacy in una risorsa di Azure usando PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Usare Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Creare ed effettuare il provisioning di un peering diretto

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Ottenere l'elenco dei percorsi di peering supportati per il peering diretto
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Creare un peering diretto
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificare il peering diretto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificare un peering diretto
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Effettuare il deprovisioning di un peering diretto
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange tramite PowerShell](howto-exchange-powershell.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure tramite PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per altre informazioni, vedere [domande frequenti sul peering Internet](faqs.md).
