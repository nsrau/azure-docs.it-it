---
title: Creare o modificare un peering diretto con il portale
titleSuffix: Azure
description: Creare o modificare un peering diretto con il portale
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775329"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Creare o modificare un peering diretto con il portale

In questo articolo viene descritto come creare un peering Microsoft Direct tramite il portale. In questo articolo viene inoltre illustrato come controllare lo stato della risorsa, aggiornarla o eliminarla ed eseguirne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Esaminare [i prerequisiti](prerequisites.md) e la procedura dettagliata di [peering diretto](walkthrough-direct-all.md) prima di iniziare la configurazione.
* Nel caso in cui si disponga già di Peering diretto con Microsoft, che non vengono convertiti in risorse di Azure, vedere [Convertire un peering diretto legacy in risorse](howto-legacy-direct-portal.md) di Azure usando il portale

## <a name="create-and-provision-a-direct-peering"></a>Creare ed eseguire il provisioning di un peering direttoCreate and provision a Direct peering

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Creare un peering direttoCreate a Direct peering

È possibile creare una nuova richiesta di peering usando la risorsa **Peering.You** can create a new peering request by using Peering resource.

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

* Creare o modificare il [peering di Exchange utilizzando il portale](howto-exchange-portal.md).
* [Convertire un peering di Exchange legacy in una risorsa](howto-legacy-exchange-portal.md)di Azure usando il portale .

## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni, visita Le domande frequenti sul [peering Internet](faqs.md)
