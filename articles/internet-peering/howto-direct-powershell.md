---
title: Creare o modificare un peering diretto usando PowerShell
titleSuffix: Azure
description: Creare o modificare un peering diretto usando PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 076332ac61359bc793615c2f7c9ea0e22c667bcd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700298"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Creare o modificare un peering diretto usando PowerShell

Questo articolo descrive come creare un peering Microsoft diretto usando i cmdlet di PowerShell e il modello di distribuzione Azure Resource Manager. Questo articolo illustra anche come controllare lo stato della risorsa, aggiornarla o eliminarla ed effettuarne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-direct-portal.md)di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata per il peering di Direct](walkthrough-direct-all.md).
* Se si hanno già connessioni peering dirette con Microsoft che non vengono convertite in risorse di Azure, vedere [convertire un peering diretto legacy in una risorsa di Azure usando PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Usare Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Creare un peering di Direct ed effettuarne il provisioning

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Ottenere l'elenco dei percorsi di peering supportati per il peering diretto
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Creare un peering di Direct
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificare il peering di Direct
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificare un peering di Direct
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Effettuare il deprovisioning di un peering di Direct
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange tramite PowerShell](howto-exchange-powershell.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure tramite PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per altre informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).
