---
title: Convertire un peering diretto legacy in una risorsa di Azure usando il portale di Azure
titleSuffix: Azure
description: Convertire un peering diretto legacy in una risorsa di Azure usando il portale di Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678840"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Convertire un peering diretto legacy in una risorsa di Azure usando il portale di Azure

Questo articolo descrive come convertire un peering diretto legacy esistente in una risorsa di Azure usando il portale di Azure.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata relativa al peering diretto](walkthrough-direct-all.md) .


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Convertire un peering diretto legacy in una risorsa di Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Convertire un peering diretto legacy

È possibile convertire le connessioni di peering legacy usando la risorsa **peering** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Avviare la risorsa e configurare le impostazioni di base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificare il peering diretto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [domande frequenti sul peering Internet](faqs.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto usando il portale](howto-direct-portal.md)
