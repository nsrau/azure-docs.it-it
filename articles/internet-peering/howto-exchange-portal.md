---
title: Creare o modificare un peering di Exchange tramite il portale di AzureCreate or modify an Exchange peering by using the Azure portal
titleSuffix: Azure
description: Creare o modificare un peering di Exchange tramite il portale di AzureCreate or modify an Exchange peering by using the Azure portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680916"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Creare o modificare un peering di Exchange tramite il portale di AzureCreate or modify an Exchange peering by using the Azure portal

In questo articolo viene descritto come creare un peering di Microsoft Exchange tramite il portale di Azure.This article describes how to create a Microsoft Exchange peering by using the Azure portal. In questo articolo viene inoltre illustrato come controllare lo stato della risorsa, aggiornarla o eliminarla ed eseguirne il deprovisioning.

Se si preferisce, è possibile completare questa guida utilizzando [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare i [prerequisiti](prerequisites.md) e la procedura dettagliata di [peering di Exchange](walkthrough-exchange-all.md) prima di iniziare la configurazione.
* Se si dispone già di peering di Exchange con Microsoft che non sono convertiti in risorse di Azure, vedere Convertire un peering di Exchange legacy in una risorsa di [Azure tramite il portale](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Creare ed eseguire il provisioning di un peering di ExchangeCreate and provision an Exchange peering

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Creare un peering di Exchange

È possibile creare una nuova richiesta di peering usando la risorsa **Peering.You** can create a new peering request by using the Peering resource.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Avviare la risorsa e configurare le impostazioni di base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Verificare un peering di Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificare un peering di Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Eseguire il deprovisioning di un peering di ExchangeDeprovision an Exchange peering
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto tramite il portaleCreate or modify a Direct peering by using the portal](howto-direct-portal.md)
* [Convertire un peering diretto legacy in una risorsa di Azure tramite il portaleConvert a legacy Direct peering to an Azure resource by using the portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).
