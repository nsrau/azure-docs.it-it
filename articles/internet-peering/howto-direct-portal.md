---
title: Creare o modificare un peering diretto usando il portale di Azure
titleSuffix: Azure
description: Creare o modificare un peering diretto usando il portale di Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681038"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Creare o modificare un peering diretto usando il portale di Azure

Questo articolo descrive come creare un peering Microsoft diretto usando il portale di Azure. Questo articolo illustra anche come controllare lo stato della risorsa, aggiornarla o eliminarla ed effettuarne il deprovisioning.

Se si preferisce, è possibile completare questa guida usando Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata relativa al peering diretto](walkthrough-direct-all.md) .
* Se si hanno già connessioni peering dirette con Microsoft che non vengono convertite in risorse di Azure, vedere [convertire un peering diretto legacy in una risorsa di Azure usando il portale](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Creare ed effettuare il provisioning di un peering diretto

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Creare un peering diretto

È possibile creare una nuova richiesta di peering usando la risorsa **peering** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Avviare la risorsa e configurare le impostazioni di base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificare il peering diretto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificare un peering diretto
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Effettuare il deprovisioning di un peering diretto
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare il peering di Exchange tramite il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure tramite il portale](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [domande frequenti sul peering Internet](faqs.md).
