---
title: Spostare le risorse del servizio app Azure
description: Usare Azure Resource Manager per spostare le risorse del servizio app in un nuovo gruppo di risorse o una nuova sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 04ddf644c58434531dde708ee3b6432b1fce8f91
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533537"
---
# <a name="move-guidance-for-app-service-resources"></a>Spostare le linee guida per le risorse del servizio app

I passaggi per spostare le risorse del servizio app variano a seconda che si stiano spostando le risorse all'interno di una sottoscrizione o a una nuova sottoscrizione.

## <a name="move-in-same-subscription"></a>Sposta nella stessa sottoscrizione

Quando si sposta un'app Web _nella stessa sottoscrizione_, non è possibile spostare i certificati SSL di terze parti. È comunque possibile spostare un'app Web nel nuovo gruppo di risorse senza spostare il relativo certificato di terze parti mantenendo effettiva la funzionalità SSL dell'app.

Se si desidera spostare il certificato SSL con l'app Web, attenersi alla procedura seguente:

1. Eliminare il certificato di terze parti dall'app Web, ma conservare una copia del certificato
2. Spostare l'app Web.
3. Caricare il certificato di terze parti nell'app Web spostata.

## <a name="move-across-subscriptions"></a>Sposta tra sottoscrizioni

Quando si sposta un'app Web _tra sottoscrizioni_, si applicano le limitazioni seguenti:

- Il gruppo di risorse di destinazione non deve contenere risorse del servizio app esistenti. Le risorse del servizio app includono:
    - App Web
    - Piani del servizio app
    - Certificati SSL importati o caricati
    - Ambienti di servizio app
- Tutte le risorse del servizio app nel gruppo di risorse devono essere spostate insieme.
- Le risorse del servizio app possono essere spostate solo dal gruppo di risorse in cui sono state originariamente create. Se una risorsa del servizio app non si trova più nel gruppo di risorse originale, spostarla di nuovo nel gruppo di risorse originale. Spostare quindi la risorsa tra le sottoscrizioni.

Se non si ricorda il gruppo di risorse originale, è possibile trovarlo tramite la diagnostica. Per l'app Web, selezionare **diagnostica e Risolvi i problemi**. Selezionare quindi **configurazione e gestione**.

![Seleziona diagnostica](./media/app-service-move-limitations/select-diagnostics.png)

Selezionare **Opzioni di migrazione**.

![Selezionare le opzioni di migrazione](./media/app-service-move-limitations/select-migration.png)

Selezionare l'opzione per la procedura consigliata per spostare l'app Web.

![Selezionare i passaggi consigliati](./media/app-service-move-limitations/recommended-steps.png)

Verranno visualizzate le azioni consigliate da eseguire prima di trasferire le risorse. Le informazioni includono il gruppo di risorse originale per l'app Web.

![Raccomandazioni](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Sposta certificato del servizio app

È possibile spostare il certificato del servizio app in un nuovo gruppo di risorse oppure in una nuova sottoscrizione. Se il certificato del servizio app è associato a un'app Web, è necessario eseguire alcuni passaggi prima di spostare le risorse in una nuova sottoscrizione. Eliminare l'associazione SSL e il certificato privato dall'app Web prima di spostare le risorse. Il certificato del servizio app non deve essere eliminato, è sufficiente eliminare solo il certificato privato nell'app Web.

## <a name="move-support"></a>Supporto per l'operazione di spostamento

Per determinare quali risorse del servizio app possono essere spostate, vedere spostare lo stato di supporto per:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../resource-group-move-resources.md).
