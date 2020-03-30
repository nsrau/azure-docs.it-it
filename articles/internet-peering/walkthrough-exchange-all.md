---
title: Procedura dettagliata per il peering di Exchange
titleSuffix: Azure
description: Procedura dettagliata per il peering di Exchange
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775173"
---
# <a name="exchange-peering-walkthrough"></a>Procedura dettagliata per il peering di Exchange

In questa sezione vengono illustrati i passaggi da seguire per configurare e gestire un peering di Exchange.

## <a name="create-an-exchange-peering"></a>Creare un peering di Exchange
> [!div class="mx-imgBorder"]
> ![Flusso di lavoro di peering di Exchange e stati di connessione](./media/exchange-peering.png)

Per eseguire il provisioning di un peering di Exchange, è necessario eseguire i passaggi seguenti:
1. Esaminare i criteri di peering Microsoft per comprendere i requisiti per il peering di Exchange.Review Microsoft [peering policy](https://peering.azurewebsites.net/peering) to understand requirements for Exchange peering.
1. Trovare la posizione di peering Microsoft e l'ID della struttura di peering in [PeeringDBFind](https://www.peeringdb.com/net/694) Microsoft peering location and peering facility id in PeeringDB
1. Richiedere il peering di Exchange per un percorso di peering utilizzando le istruzioni in [Creare e modificare un peering di Exchange utilizzando](howto-exchange-powershell.md) l'articolo di PowerShell per ulteriori dettagli.
1. Dopo aver inviato una richiesta di peering, Microsoft esaminerà la richiesta e contatterà l'utente, se necessario.
1. Una volta approvato, lo stato della connessione diventa Approvato
1. Configurare la sessione BGP alla fine e inviare una notifica a Microsoft
1. Verrà eseguito il provisioning della sessione BGP con i criteri DENY ALL e verrà convalidato end-to-end.
1. In caso di esito positivo, si riceverà una notifica che lo stato della connessione di peering è Attivo.If successful, you will receive a notification that peering connection state is Active.
1. Il traffico sarà quindi consentito tramite il nuovo peering.

Si noti che gli stati di connessione non devono essere confusi con gli stati di sessione [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) standard.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Convertire un peering di Exchange legacy in una risorsa di Azure
Per convertire un peering di Exchange legacy in una risorsa di Azure, è necessario eseguire i passaggi seguenti:The following steps must be followed in order to convert a legacy Exchange peering to Azure resource:
1. Seguire le istruzioni in [Convertire un peering](howto-legacy-exchange-powershell.md) di Exchange legacy in una risorsa di Azure
1. Dopo aver inviato la richiesta di conversione, Microsoft esaminerà la richiesta e contatterà l'utente, se necessario.
1. Una volta approvato, il peering di Exchange con stato di connessione verrà visualizzato come Attivo.

## <a name="deprovision-exchange-peering"></a>Eseguire il deprovisioning del peering di ExchangeDeprovision Exchange peering
Contattare [il peering Microsoft](mailto:peering@microsoft.com) per eseguire il deprovisioning del peering di Exchange.

Quando un peering di Exchange è impostato per il deprovisioning, lo stato della connessione sarà **PendingRemove**

> [!NOTE]
> Se si esegue il cmdlet PowerShell per eliminare il peering di Exchange quando lo stato della connessione è ProvisioningStarted o ProvisioningCompleted, l'operazione avrà esito negativo.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [prerequisiti per configurare](prerequisites.md)il peering con Microsoft .
