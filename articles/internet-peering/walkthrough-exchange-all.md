---
title: Procedura dettagliata per il peering di Exchange
titleSuffix: Azure
description: Procedura dettagliata per il peering di Exchange
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ec83778d034cfc512582eddf79995412dad405c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710729"
---
# <a name="exchange-peering-walkthrough"></a>Procedura dettagliata per il peering di Exchange

In questa sezione vengono illustrati i passaggi da seguire per configurare e gestire un peering di Exchange.

## <a name="create-an-exchange-peering"></a>Creare un peering di Exchange
> [!div class="mx-imgBorder"]
> ![Flusso di lavoro e Stati di connessione del peering di Exchange](./media/exchange-peering.png)

Per eseguire il provisioning di un peering di Exchange, è necessario attenersi alla procedura seguente:
1. Esaminare i [criteri di peering](https://peering.azurewebsites.net/peering) Microsoft per comprendere i requisiti per il peering di Exchange.
1. Trovare il percorso di peering Microsoft e l'ID della funzionalità di peering in [PeeringDB](https://www.peeringdb.com/net/694)
1. Richiedere il peering di Exchange per una località di peering seguendo le istruzioni riportate nell'articolo [creare e modificare un peering di Exchange con PowerShell](howto-exchange-powershell.md) per altri dettagli.
1. Dopo aver inviato una richiesta di peering, Microsoft esaminerà la richiesta e contatterà l'utente se necessario.
1. Una volta approvata, lo stato della connessione passa ad approvato
1. Configurare la sessione BGP alla fine e inviare una notifica a Microsoft
1. Si eseguirà il provisioning della sessione BGP con nega tutti i criteri e la convalida end-to-end.
1. Se l'esito è positivo, si riceverà una notifica che indica che lo stato di connessione del peering è attivo
1. Il traffico sarà quindi consentito tramite il nuovo peering.

Si noti che gli Stati di connessione non devono essere confusi con gli Stati di sessione [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) standard.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Convertire un peering di Exchange legacy in una risorsa di Azure
Per convertire un peering di Exchange legacy in una risorsa di Azure, è necessario seguire la procedura seguente:
1. Seguire le istruzioni riportate in [convertire un peering Exchange legacy in una risorsa di Azure](howto-legacy-exchange-powershell.md)
1. Dopo aver inviato la richiesta di conversione, Microsoft esaminerà la richiesta e contatterà l'utente se necessario.
1. Una volta approvata, il peering di Exchange viene visualizzato con lo stato di connessione attivo.

## <a name="deprovision-exchange-peering"></a>Deprovisioning del peering di Exchange
Contattare il [peering Microsoft](mailto:peering@microsoft.com) per effettuare il deprovisioning del peering di Exchange.

Quando viene impostato un peering di Exchange per il deprovisioning, lo stato della connessione verrà visualizzato come **PendingRemove**

> [!NOTE]
> Se si esegue il cmdlet di PowerShell per eliminare il peering di Exchange quando lo stato della connessione è ProvisioningStarted o ProvisioningCompleted, l'operazione avrà esito negativo.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [prerequisiti per la configurazione del peering con Microsoft](prerequisites.md).
