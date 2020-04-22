---
title: Creare o modificare un peering diretto tramite il portale di AzureCreate or modify a Direct peering by using the Azure portal
titleSuffix: Azure
description: Creare o modificare un peering diretto tramite il portale di AzureCreate or modify a Direct peering by using the Azure portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681038"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Creare o modificare un peering diretto tramite il portale di AzureCreate or modify a Direct peering by using the Azure portal

Questo articolo descrive come creare un peering Microsoft Direct tramite il portale di Azure.This article describes how to create a Microsoft Direct peering by using the Azure portal. In questo articolo viene inoltre illustrato come controllare lo stato della risorsa, aggiornarla o eliminarla ed eseguirne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando Azure [PowerShell.](howto-direct-powershell.md)

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata di peering diretto](walkthrough-direct-all.md) prima di iniziare la configurazione.
* Se si dispone già di connessioni di peering diretto con Microsoft che non sono convertite in risorse di Azure, vedere [Convertire un peering diretto legacy in](howto-legacy-direct-portal.md)una risorsa di Azure tramite il portale .

## <a name="create-and-provision-a-direct-peering"></a>Creare ed eseguire il provisioning di un peering direttoCreate and provision a Direct peering

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Creare un peering direttoCreate a Direct peering

È possibile creare una nuova richiesta di peering usando la risorsa **Peering.You** can create a new peering request by using the Peering resource.

#### <a name="launch-resource-and-configure-basic-settings"></a>Avviare la risorsa e configurare le impostazioni di base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificare il peering direttoVerify Direct peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificare un peering direttoModify a Direct peering
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Eseguire il deprovisioning di un peering direttoDeprovision a Direct peering
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange tramite il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure tramite il portaleConvert a legacy Exchange peering to an Azure resource by using the portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).
