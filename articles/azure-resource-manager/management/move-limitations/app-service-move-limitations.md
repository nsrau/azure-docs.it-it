---
title: Spostare le risorse del servizio app Azure
description: Usare Azure Resource Manager per spostare le risorse del servizio app in un nuovo gruppo di risorse o una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531373"
---
# <a name="move-guidance-for-app-service-resources"></a>Spostare le linee guida per le risorse del servizio app

Questo articolo descrive i passaggi per spostare le risorse del servizio app. Esistono requisiti specifici per lo trasferimento di risorse del servizio app a una nuova sottoscrizione.

## <a name="move-across-subscriptions"></a>Sposta tra sottoscrizioni

Quando si trasferisce un'app Web tra sottoscrizioni, si applicano le indicazioni seguenti:

- Il gruppo di risorse di destinazione non deve contenere risorse del servizio app esistenti. Le risorse del servizio app includono:
    - App Web
    - Piani di servizio app
    - Certificati TLS/SSL caricati o importati
    - Ambienti del servizio app
- Tutte le risorse del servizio app nel gruppo di risorse devono essere spostate insieme.
- Gli ambienti del servizio app non possono essere spostati in un nuovo gruppo di risorse o in una nuova sottoscrizione. Tuttavia, è possibile spostare un'app Web e un piano di servizio app in una nuova sottoscrizione senza spostare il ambiente del servizio app. Dopo lo spostamento, l'app Web non è più ospitata nel ambiente del servizio app.
- È possibile spostare un certificato associato a un Web senza eliminare i binding TLS, purché il certificato venga spostato con tutte le altre risorse nel gruppo di risorse.
- Le risorse del servizio app possono essere spostate solo dal gruppo di risorse in cui sono state originariamente create. Se una risorsa del servizio app non si trova più nel gruppo di risorse originale, spostarla di nuovo nel gruppo di risorse originale. Spostare quindi la risorsa tra le sottoscrizioni.

Se non si ricorda il gruppo di risorse originale, è possibile trovarlo tramite la diagnostica. Per l'app Web, selezionare **diagnostica e Risolvi i problemi**. Selezionare quindi **configurazione e gestione**.

![Seleziona diagnostica](./media/app-service-move-limitations/select-diagnostics.png)

Selezionare **Opzioni di migrazione**.

![Selezionare le opzioni di migrazione](./media/app-service-move-limitations/select-migration.png)

Selezionare l'opzione per la procedura consigliata per spostare l'app Web.

![Selezionare i passaggi consigliati](./media/app-service-move-limitations/recommended-steps.png)

Verranno visualizzate le azioni consigliate da eseguire prima di trasferire le risorse. Le informazioni includono il gruppo di risorse originale per l'app Web.

![L'acquisizione schermo Mostra i passaggi consigliati per lo stato di trasferimento delle risorse Web di Microsoft dot.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Supporto per l'operazione di spostamento

Per determinare quali risorse del servizio app possono essere spostate, vedere spostare lo stato di supporto per:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../move-resource-group-and-subscription.md).
