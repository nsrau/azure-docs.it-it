---
title: Convertire un peering di Exchange legacy in una risorsa di Azure usando il portale di AzureConvert a legacy Exchange peering to an Azure resource by using the Azure portal
titleSuffix: Azure
description: Convertire un peering di Exchange legacy in una risorsa di Azure usando il portale di AzureConvert a legacy Exchange peering to an Azure resource by using the Azure portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678576"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Convertire un peering di Exchange legacy in una risorsa di Azure usando il portale di AzureConvert a legacy Exchange peering to an Azure resource by using the Azure portal

Questo articolo descrive come convertire un peering di Exchange legacy esistente in una risorsa di Azure usando il portale di Azure.This article describes how to convert an existing legacy Exchange peering to an Azure resource by using the Azure portal.

Se si preferisce, è possibile completare questa guida utilizzando [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare i [prerequisiti](prerequisites.md) e la procedura dettagliata di [peering di Exchange](walkthrough-exchange-all.md) prima di iniziare la configurazione.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Convertire un peering di Exchange legacy in una risorsa di AzureConvert a legacy Exchange peering to an Azure resource

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Convertire il peering legacy di Exchange

È possibile convertire le connessioni peering legacy usando la risorsa **Peering.You** can convert legacy peering connections by using the Peering resource.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Avviare la risorsa e configurare le impostazioni di base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Verificare il peering di Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering di Exchange tramite il portale](howto-exchange-portal.md)
