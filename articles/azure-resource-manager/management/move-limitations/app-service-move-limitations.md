---
title: Spostare le risorse del servizio app di AzureMove Azure App Service resources
description: Usare Azure Resource Manager per spostare le risorse del servizio app in un nuovo gruppo di risorse o in una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: d0ecd117bdcda9238e310a3020dba19a6871a3fc
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655778"
---
# <a name="move-guidance-for-app-service-resources"></a>Spostare le indicazioni per le risorse del servizio appMove guidance for App Service resources

Questo articolo descrive i passaggi per spostare le risorse del servizio app. Esistono requisiti specifici per spostare le risorse del servizio app in una nuova sottoscrizione.

## <a name="move-across-subscriptions"></a>Spostarsi tra gli abbonamenti

Quando si sposta un'app Web tra sottoscrizioni, vengono fornite le indicazioni seguenti:

- Il gruppo di risorse di destinazione non deve contenere risorse del servizio app esistenti. Le risorse del servizio app includono:
    - App Web
    - Piani di servizio app
    - Certificati TLS/SSL caricati o importati
    - Ambienti del servizio app
- Tutte le risorse del servizio app nel gruppo di risorse devono essere spostate insieme. Si noti che gli ambienti del servizio app non possono essere spostati in un nuovo gruppo di risorse né in una nuova sottoscrizione.
- È possibile spostare un certificato associato a un Web senza eliminare i binding TLS, purché il certificato venga spostato con tutte le altre risorse nel gruppo di risorse.
- Le risorse del servizio app possono essere spostate solo dal gruppo di risorse in cui sono state originariamente create. Se una risorsa del servizio app non è più nel gruppo di risorse originale, spostarla di nuovo nel gruppo di risorse originale. Spostare quindi la risorsa tra le sottoscrizioni.

Se non si ricorda il gruppo di risorse originale, è possibile trovarlo tramite la diagnostica. Per l'app Web, selezionare **Diagnostica e risolvi problemi.** Selezionare **quindi Configurazione e gestione**.

![Selezionare la diagnostica](./media/app-service-move-limitations/select-diagnostics.png)

Selezionare **Opzioni di migrazione**.

![Selezionare le opzioni di migrazione](./media/app-service-move-limitations/select-migration.png)

Selezionare l'opzione per i passaggi consigliati per spostare l'app Web.

![Selezionare i passaggi consigliati](./media/app-service-move-limitations/recommended-steps.png)

Vengono visualizzate le azioni consigliate da eseguire prima di spostare le risorse. Le informazioni includono il gruppo di risorse originale per l'app Web.

![Consigli](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Supporto per l'operazione di spostamento

Per determinare quali risorse del servizio app possono essere spostate, vedere spostare lo stato del supporto per:To determine which App Service resources can be moved, see move support status for:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../move-resource-group-and-subscription.md).
