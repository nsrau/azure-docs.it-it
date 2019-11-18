---
title: Spostare le risorse di distribuzione classica di Azure
description: Usare Azure Resource Manager per spostare le risorse di distribuzione classica in un nuovo gruppo di risorse o una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: b97496e4abfdf248b9f5010417e9284c643a74ad
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150847"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Spostare le linee guida per le risorse del modello di distribuzione classica

I passaggi per spostare le risorse distribuite con il modello classico variano a seconda che si stiano spostando le risorse all'interno di una sottoscrizione o a una nuova sottoscrizione.

## <a name="move-in-the-same-subscription"></a>Sposta nella stessa sottoscrizione

Quando si spostano risorse da un gruppo di risorse a un altro gruppo di risorse nella stessa sottoscrizione, sono valide le restrizioni seguenti:

* Le reti virtuali (classiche) non possono essere spostate.
* Le macchine virtuali (classiche) devono essere spostate con il servizio cloud.
* Il servizio cloud può essere spostato solo quando lo spostamento include tutte le macchine virtuali del servizio stesso.
* È possibile spostare un solo servizio cloud alla volta.
* È possibile spostare un solo account di archiviazione (classico) alla volta.
* Un account di archiviazione (classico) non può essere spostato nella stessa operazione di spostamento che include una macchina virtuale o un servizio cloud.

Per spostare le risorse classiche in un nuovo gruppo di risorse all'interno della stessa sottoscrizione, usare le [operazioni di spostamento standard](../resource-group-move-resources.md) tramite il portale, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Usare le stesse operazioni eseguite per lo spostamento di risorse di Resource Manager.

## <a name="move-across-subscriptions"></a>Sposta tra sottoscrizioni

Quando si spostano risorse in una nuova sottoscrizione, sono valide le restrizioni seguenti:

* Tutte le risorse classiche della sottoscrizione vanno spostate nella stessa operazione.
* La sottoscrizione di destinazione non deve contenere nessuna delle altre risorse classiche.
* Lo spostamento può essere richiesto solo tramite un'API REST separata per gli spostamenti di risorse classiche. I comandi di spostamento standard di Resource Manager non funzionano quando si spostano risorse classiche a una nuova sottoscrizione.

Per spostare le risorse classiche in una nuova sottoscrizione, usare le operazioni REST specifiche per le risorse classiche. Per usare REST, seguire questa procedura:

1. Controllare se la sottoscrizione di origine può partecipare a un'operazione di spostamento tra sottoscrizioni. Usare l'operazione seguente:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     Nel corpo della richiesta includere:

   ```json
   {
    "role": "source"
   }
   ```

     La risposta per l'operazione di convalida ha il formato seguente:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Controllare se la sottoscrizione di destinazione può partecipare a un'operazione di spostamento tra sottoscrizioni. Usare l'operazione seguente:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     Nel corpo della richiesta includere:

   ```json
   {
    "role": "target"
   }
   ```

     La risposta ha lo stesso formato della convalida della sottoscrizione di origine.
1. Se entrambe le sottoscrizioni superano la convalida, spostare tutte le risorse classiche da una sottoscrizione a un'altra usando l'operazione seguente:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    Nel corpo della richiesta includere:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Questa operazione potrebbe richiedere alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

Se si riscontrano problemi durante lo trasferimento delle risorse classiche, contattare il [supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../resource-group-move-resources.md).
