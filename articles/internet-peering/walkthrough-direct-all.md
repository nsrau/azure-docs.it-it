---
title: Procedura dettagliata per il peering diretto
titleSuffix: Azure
description: Procedura dettagliata per il peering diretto
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e27fa26514d27d68aecdf9e28b36e2747dc8ffe2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84710763"
---
# <a name="direct-peering-walkthrough"></a>Procedura dettagliata per il peering diretto

In questa sezione vengono illustrati i passaggi da seguire per configurare e gestire un peering diretto.

## <a name="create-a-direct-peering"></a>Creare un peering di Direct
> [!div class="mx-imgBorder"]
> ![Flusso di lavoro del peering diretto e Stati di connessione](./media/direct-peering.png)

Per eseguire il provisioning di un peering diretto, è necessario seguire la procedura seguente:
1. Esaminare i [criteri di peering](https://peering.azurewebsites.net/peering) Microsoft per comprendere i requisiti per il peering diretto.
1. Per inviare una richiesta di peering, seguire le istruzioni riportate in [creare o modificare un peering diretto](howto-direct-powershell.md) .
1. Dopo l'invio di una richiesta di peering, Microsoft contatterà l'utente utilizzando il proprio indirizzo di posta elettronica registrato per fornire a LOA (lettera di autorizzazione) o per altre informazioni.
1. Una volta approvata la richiesta di peering, lo stato della connessione passa a ProvisioningStarted.
1. È necessario:
    1. completare il cablaggio in base alla LOA
    1. (facoltativamente) eseguire il test di collegamento utilizzando 169.254.0.0/16
    1. configurare la sessione BGP e quindi inviarne una notifica.
1. Microsoft effettua il provisioning della sessione BGP con nega tutti i criteri e convalida end-to-end.
1. Se l'esito è positivo, si riceverà una notifica che indica che lo stato di connessione del peering è attivo
1. Il traffico sarà quindi consentito tramite il nuovo peering.

Si noti che gli Stati di connessione non devono essere confusi con gli Stati di sessione [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) standard.

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Convertire un peering diretto legacy in una risorsa di Azure
È necessario seguire questa procedura per convertire un peering diretto legacy in una risorsa di Azure:
1. Seguire le istruzioni riportate in [convertire un peering diretto legacy in una risorsa di Azure](howto-legacy-direct-powershell.md)
1. Dopo aver inviato la richiesta di conversione, Microsoft esaminerà la richiesta e contatterà l'utente se necessario.
1. Una volta approvata, il peering diretto sarà visualizzato con uno stato di connessione attivo.

## <a name="deprovision-direct-peering"></a>Deprovisioning diretto del peering
Contattare il team del [peering Microsoft](mailto:peering@microsoft.com) per effettuare il deprovisioning diretto del peering.

Quando viene impostato un peering diretto per il deprovisioning, lo stato della connessione verrà visualizzato come **PendingRemove**

> [!NOTE]
> Se si esegue il cmdlet di PowerShell per eliminare il peering diretto quando ConnectionState è ProvisioningStarted o ProvisioningCompleted, l'operazione avrà esito negativo.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [prerequisiti per la configurazione del peering con Microsoft](prerequisites.md).
