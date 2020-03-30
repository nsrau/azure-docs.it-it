---
title: Convertire un peering diretto legacy in una risorsa di Azure con il portale
titleSuffix: Azure
description: Convertire un peering diretto legacy in una risorsa di Azure con il portale
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775199"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Convertire un peering di Exchange legacy in una risorsa di Azure con il portale

In questo articolo viene descritto come convertire un peering di Exchange legacy esistente in risorse di Azure usando il portale.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare [i prerequisiti](prerequisites.md) e la procedura dettagliata di [peering di Exchange](walkthrough-exchange-all.md) prima di iniziare la configurazione.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Convertire un peering di Exchange legacy in una risorsa di Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Convertire il peering legacy di Exchange

È possibile convertire le connessioni peering legacy usando la risorsa **peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Avviare la risorsa e configurare le impostazioni di base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Verificare il peering di Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni, visita Le domande frequenti sul [peering Internet](faqs.md)

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering di Exchange con il portale](howto-exchange-portal.md)
