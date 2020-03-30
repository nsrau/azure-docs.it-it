---
title: Creare o modificare un peering di Exchange con il portale
titleSuffix: Azure
description: Creare o modificare un peering di Exchange con il portale
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774575"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Creare o modificare un peering di Exchange con il portale

In questo articolo viene descritto come creare un peering di Microsoft Exchange utilizzando il portale. In questo articolo viene inoltre illustrato come controllare lo stato della risorsa, aggiornarla o eliminarla ed eseguirne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare [i prerequisiti](prerequisites.md) e la procedura dettagliata di [peering di Exchange](walkthrough-exchange-all.md) prima di iniziare la configurazione.
* Nel caso in cui si dispone già di peering di Exchange con Microsoft, che non vengono convertiti in risorse di Azure, vedere Convertire un peering di Exchange legacy in risorse di [Azure tramite il portale](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Creare ed eseguire il provisioning di un peering di ExchangeCreate and provision an Exchange peering

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Creare un peering di Exchange

È possibile creare una nuova richiesta di peering usando la risorsa **Peering.You** can create a new peering request by using Peering resource.

#### <a name="launch-resource-and-configure-basic-settings"></a>Avviare la risorsa e configurare le impostazioni di base
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

* [Creare o modificare un peering diretto con il portale](howto-direct-portal.md)
* [Convertire un peering diretto legacy in una risorsa di Azure con il portale](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni, visita Le domande frequenti sul [peering Internet](faqs.md)
