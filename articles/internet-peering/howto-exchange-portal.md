---
title: Creare o modificare un peering di Exchange tramite il portale
titleSuffix: Azure
description: Creare o modificare un peering di Exchange tramite il portale
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774575"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Creare o modificare un peering di Exchange tramite il portale

Questo articolo descrive come creare un peering di Microsoft Exchange usando il portale. Questo articolo illustra anche come controllare lo stato della risorsa, aggiornarla o eliminarla ed effettuarne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata per il peering di Exchange](walkthrough-exchange-all.md) prima di iniziare la configurazione.
* Se sono già presenti peering di Exchange con Microsoft, che non vengono convertiti in risorse di Azure, vedere [convertire un peering Exchange legacy in una risorsa di Azure tramite il portale](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Creare ed effettuare il provisioning di un peering Exchange

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Creare un peering di Exchange

È possibile creare una nuova richiesta di peering usando la risorsa **peering** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Avviare la risorsa e configurare le impostazioni di base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name=get></a>Verificare un peering di Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify"></a>Modificare un peering di Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="delete"></a>Effettuare il deprovisioning di un peering di Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto usando il portale](howto-direct-portal.md)
* [Convertire un peering diretto legacy in una risorsa di Azure tramite il portale](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [domande frequenti sul peering Internet](faqs.md)
