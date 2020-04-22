---
title: Creare o modificare un peering diretto tramite PowerShellCreate or modify a Direct peering by using PowerShell
titleSuffix: Azure
description: Creare o modificare un peering diretto tramite PowerShellCreate or modify a Direct peering by using PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680814"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Creare o modificare un peering diretto tramite PowerShellCreate or modify a Direct peering by using PowerShell

In questo articolo viene descritto come creare un peering Microsoft Direct usando i cmdlet di PowerShell e il modello di distribuzione di Azure Resource Manager.This article describes how to create a Microsoft Direct peering by using PowerShell cmdlets and the Azure Resource Manager deployment model. In questo articolo viene inoltre illustrato come controllare lo stato della risorsa, aggiornarla o eliminarla ed eseguirne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-direct-portal.md)di Azure .

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata di peering diretto](walkthrough-direct-all.md) prima di iniziare la configurazione.
* Se si dispone già di connessioni di peering diretto con Microsoft che non sono convertite in risorse di Azure, vedere Convertire un peering diretto legacy in una risorsa di Azure tramite PowerShell.If you already have Direct peering connections with Microsoft that aren't converted to Azure resources, see [Convert a legacy Direct peering to an Azure resource by using PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Usare Azure PowerShellWork with Azure PowerShell
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

* [Creare o modificare il peering di Exchange tramite PowerShellCreate or modify Exchange peering by using PowerShell](howto-exchange-powershell.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure tramite PowerShellConvert a legacy Exchange peering to an Azure resource by using PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```

Per ulteriori informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).
