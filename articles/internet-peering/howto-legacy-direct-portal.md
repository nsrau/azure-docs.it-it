---
title: Convertire un peering diretto legacy in una risorsa di Azure tramite il portale
titleSuffix: Azure
description: Convertire un peering diretto legacy in una risorsa di Azure tramite il portale
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775056"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Convertire un peering diretto legacy in una risorsa di Azure tramite il portale

Questo articolo descrive come convertire un peering diretto legacy esistente in una risorsa di Azure usando il portale.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata di peering diretto](walkthrough-direct-all.md) .


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Convertire il peering diretto legacy in una risorsa di Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Converti peering diretto legacy

È possibile convertire le connessioni peer legacy usando la risorsa **peering** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Avviare la risorsa e configurare le impostazioni di base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name=get></a>Verificare il peering diretto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [domande frequenti sul peering Internet](faqs.md)

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto usando il portale](howto-direct-portal.md).
