---
title: Creare o modificare un peering di Exchange tramite PowerShellCreate or modify an Exchange peering using PowerShell
titleSuffix: Azure
description: Creare o modificare un peering di Exchange tramite PowerShellCreate or modify an Exchange peering using PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774406"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Creare o modificare un peering di Exchange tramite PowerShellCreate or modify an Exchange peering using PowerShell

In questo articolo viene descritto come creare un peering di Microsoft Exchange utilizzando i cmdlet di PowerShell e il modello di distribuzione di Resource Manager. In questo articolo viene inoltre illustrato come controllare lo stato della risorsa, aggiornarla o eliminarla ed eseguirne il deprovisioning.

Se si preferisce, è possibile completare questa guida utilizzando il [portale](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare [i prerequisiti](prerequisites.md) e la procedura dettagliata di [peering di Exchange](walkthrough-exchange-all.md) prima di iniziare la configurazione.
* Nel caso in cui si dispone già di peering di Exchange con Microsoft, che non vengono convertiti in risorse di Azure, vedere Convertire un peering di Exchange legacy in risorse di [Azure tramite PowerShell](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Creare ed eseguire il provisioning di un peering di ExchangeCreate and provision an Exchange peering

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Ottenere l'elenco dei percorsi di peering supportati per il peering di ExchangeGet the list of supported peering locations for Exchange peering
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Creare un peering di Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Ottenere il peering di Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificare un peering di Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Eseguire il deprovisioning di un peering di ExchangeDeprovision an Exchange peering

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto tramite PowerShellCreate or modify a Direct peering using PowerShell](howto-direct-powershell.md)
* [Convertire un peering diretto legacy in risorse di Azure usando PowerShellConvert a legacy Direct peering to Azure resource using PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per ulteriori informazioni, visita Le domande frequenti sul [peering Internet](faqs.md)
