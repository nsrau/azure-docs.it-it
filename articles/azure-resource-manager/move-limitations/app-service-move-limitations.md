---
title: Spostare le risorse servizio App di Azure alla nuova sottoscrizione o gruppo di risorse
description: Usare Azure Resource Manager per spostare le risorse del servizio App in un nuovo gruppo di risorse o sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: c1a09ff4c29a2fedfea2c165a95c042985b3c83a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723571"
---
# <a name="move-guidance-for-app-service-resources"></a>Spostare linee guida per le risorse del servizio App

I passaggi per spostare le risorse del servizio App sono diversi in base se si spostano le risorse all'interno di una sottoscrizione o in una nuova sottoscrizione.

## <a name="move-in-same-subscription"></a>Sposta nella stessa sottoscrizione

Quando si sposta un'app Web _nella stessa sottoscrizione_, non è possibile spostare i certificati SSL di terze parti. È comunque possibile spostare un'app Web nel nuovo gruppo di risorse senza spostare il relativo certificato di terze parti mantenendo effettiva la funzionalità SSL dell'app.

Se si desidera spostare il certificato SSL con l'app Web, attenersi alla procedura seguente:

1. Eliminare il certificato di terze parti dall'app Web, ma conservare una copia del certificato
2. Spostare l'app Web.
3. Caricare il certificato di terze parti nell'app Web spostata.

## <a name="move-across-subscriptions"></a>Spostamento tra sottoscrizioni

Quando si sposta un'app Web _tra sottoscrizioni_, si applicano le limitazioni seguenti:

- Il gruppo di risorse di destinazione non deve contenere risorse del servizio app esistenti. Le risorse del servizio app includono:
    - App Web
    - Piani di servizio app
    - Certificati SSL importati o caricati
    - Ambienti del servizio app
- Tutte le risorse del servizio app nel gruppo di risorse devono essere spostate insieme.
- Le risorse del servizio app possono essere spostate solo dal gruppo di risorse in cui sono state originariamente create. Se una risorsa del servizio App non è più nel relativo gruppo di risorse originale, spostarlo di nuovo al gruppo di risorse originale. Quindi, spostare le risorse tra sottoscrizioni.

Se non si ricorda il gruppo di risorse originale, è possibile trovarlo tramite diagnostica. Per l'app web, selezionare **diagnosticare e risolvere i problemi**. Quindi, selezionare **configurazione e gestione**.

![Selezionare la diagnostica](./media/app-service-move-limitations/select-diagnostics.png)

Selezionare **opzioni di migrazione**.

![Selezionare le opzioni di migrazione](./media/app-service-move-limitations/select-migration.png)

Selezionare l'opzione per procedure consigliate per spostare l'app web.

![Selezionare passaggi consigliati](./media/app-service-move-limitations/recommended-steps.png)

Noterete che le azioni consigliate da intraprendere prima di spostare le risorse. Le informazioni includono il gruppo di risorse originale per l'app web.

![Consigli](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Spostare il certificato di servizio App

È possibile spostare il certificato del servizio app in un nuovo gruppo di risorse oppure in una nuova sottoscrizione. Se il certificato del servizio app è associato a un'app Web, è necessario eseguire alcuni passaggi prima di spostare le risorse in una nuova sottoscrizione. Eliminare l'associazione SSL e il certificato privato dall'app Web prima di spostare le risorse. Il certificato del servizio app non deve essere eliminato, è sufficiente eliminare solo il certificato privato nell'app Web.

## <a name="move-support"></a>Supporto per l'operazione di spostamento

Per determinare quali risorse del servizio App possono essere spostate, vedere spostare lo stato di supporto per:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../resource-group-move-resources.md).
