---
title: Creare o modificare un peering diretto tramite PowerShellCreate or modify a Direct peering using PowerShell
titleSuffix: Azure
description: Creare o modificare un peering diretto tramite PowerShellCreate or modify a Direct peering using PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774237"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Creare o modificare un peering diretto tramite PowerShellCreate or modify a Direct peering using PowerShell

In questo articolo viene descritto come creare un peering Microsoft Direct utilizzando i cmdlet di PowerShell e il modello di distribuzione di Resource Manager.This article describes how to create a Microsoft Direct peering by using PowerShell cmdlets and the Resource Manager deployment model. In questo articolo viene inoltre illustrato come controllare lo stato della risorsa, aggiornarla o eliminarla ed eseguirne il deprovisioning.

Se si preferisce, è possibile completare questa guida utilizzando il [portale](howto-direct-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare [i prerequisiti](prerequisites.md) e la procedura dettagliata di [peering diretto](walkthrough-direct-all.md) prima di iniziare la configurazione.
* Nel caso in cui si disponga già di peering diretto con Microsoft, che non vengono convertiti in risorse di Azure, vedere Convertire un peering diretto legacy in risorse di Azure usando PowerShellIn case you have Direct peering with Microsoft already, which are not converted to Azure resources, refer to [Convert a legacy Direct peering to Azure resource using PowerShell](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Creare ed eseguire il provisioning di un peering direttoCreate and provision a Direct peering

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Ottenere l'elenco delle posizioni di peering supportate per il peering direttoGet the list of supported peering locations for Direct peering
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Creare un peering direttoCreate a Direct peering
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificare il peering direttoVerify Direct peering
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificare un peering direttoModify a Direct peering
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Eseguire il deprovisioning di un peering direttoDeprovision a Direct peering
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange tramite PowerShell](howto-exchange-powershell.md).
* [Convertire un peering di Exchange legacy in una risorsa di Azure usando PowerShell.](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per ulteriori informazioni, visita Le domande frequenti sul [peering Internet](faqs.md)
