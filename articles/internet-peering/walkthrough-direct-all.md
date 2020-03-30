---
title: Procedura dettagliata per il peering diretto
titleSuffix: Azure
description: Procedura dettagliata per il peering diretto
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775511"
---
# <a name="direct-peering-walkthrough"></a>Procedura dettagliata per il peering diretto

In questa sezione vengono illustrati i passaggi da seguire per configurare e gestire un peering diretto.

## <a name="create-a-direct-peering"></a>Creare un peering direttoCreate a Direct peering
> [!div class="mx-imgBorder"]
> ![Flusso di lavoro di peering diretto e stati di connessioneDirect peering workflow and connection states](./media/direct-peering.png)

Per eseguire il provisioning di un peering diretto, è necessario eseguire i passaggi seguenti:The following steps must be followed in order to provision a Direct peering:
1. Esaminare i [criteri di peering](https://peering.azurewebsites.net/peering) Microsoft per comprendere i requisiti per il peering diretto.
1. Seguire le istruzioni in [Creare o modificare un peering diretto](howto-direct-powershell.md) per inviare una richiesta di peering.
1. Dopo aver inviato una richiesta di peering, Microsoft contatterà utilizzando l'indirizzo di posta elettronica registrato per fornire LOA (lettera di autorizzazione) o per altre informazioni.
1. Una volta approvata la richiesta di peering, lo stato della connessione passa a ProvisioningStarted.Once peering request is approved, connection state changes to ProvisioningStarted.
1. È necessario:
    1. cablaggio completo secondo la LOA
    1. (opzionalmente) eseguire il test del collegamento utilizzando 169.254.0.0/16
    1. configurare la sessione BGP e quindi avvisarci.
1. Microsoft esegue le disposizioni della sessione BGP con i criteri DENY ALL e convalida end-to-end.
1. In caso di esito positivo, si riceverà una notifica che lo stato della connessione di peering è Attivo.If successful, you will receive a notification that peering connection state is Active.
1. Il traffico sarà quindi consentito tramite il nuovo peering.

Si noti che gli stati di connessione non devono essere confusi con gli stati di sessione [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) standard.

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Convertire un peering diretto legacy in una risorsa di Azure
Per convertire un peering diretto legacy in una risorsa di Azure, è necessario eseguire i passaggi seguenti:The following steps must be followed in order to convert a legacy Direct peering to Azure resource:
1. Seguire le istruzioni in Convertire un peering diretto legacy in una risorsa di AzureFollow the instructions in [Convert a legacy Direct peering to Azure resource](howto-legacy-direct-powershell.md)
1. Dopo aver inviato la richiesta di conversione, Microsoft esaminerà la richiesta e contatterà l'utente, se necessario.
1. Una volta approvato, vedrai il tuo peering diretto con uno stato di connessione attivo.

## <a name="deprovision-direct-peering"></a>Eseguire il deprovisioning del peering direttoDeprovision Direct peering
Contattare il team di [peering Microsoft](mailto:peering@microsoft.com) per eseguire il deprovisioning del peering diretto.

Quando un peering diretto è impostato per il deprovisioning, verrà visualizzato lo stato della connessione come **PendingRemoveWhen** when a Direct peering is set for deprovision, you will see the connection state as PendingRemove

> [!NOTE]
> Se si esegue il cmdlet PowerShell per eliminare il peering diretto quando ConnectionState è ProvisioningStarted o ProvisioningCompleted, l'operazione avrà esito negativo.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [prerequisiti per configurare](prerequisites.md)il peering con Microsoft .
