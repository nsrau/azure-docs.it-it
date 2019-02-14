---
title: Individuare le risorse di Azure per la gestione in PIM | Microsoft Docs
description: Informazioni su come scoprire risorse di Azure da gestire in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/23/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90bd76470f5c06eb0e73615a358312b46d8be4a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56195397"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Individuare le risorse di Azure per la gestione in PIM

Usando Azure Active Directory Privileged Identity Management (PIM) è possibile migliorare la protezione delle risorse di Azure. Questo è utile alle organizzazioni che usano già PIM per proteggere i ruoli di directory di Azure AD, al gruppo di gestione e ai proprietari di sottoscrizioni che vogliono proteggere le risorse di produzione.

Quando si configura per la prima volta PIM per le risorse di Azure, è necessario individuare e selezionare le risorse da proteggere con PIM. Non ci sono limiti al numero di risorse che è possibile gestire con PIM. È tuttavia consigliabile iniziare con le risorse più cruciali, ossia quelle di produzione.

## <a name="discover-resources"></a>Individuare le risorse

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Risorse di Azure**.

    Se si usa PIM per la prima volta per le risorse di Azure, sarà mostrato il riquadro Individua le risorse.

    ![Individua le risorse - primo utilizzo](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Se un altro amministratore di risorse o di directory dell'organizzazione sta già gestendo risorse di Azure in PIM, verrà visualizzato un elenco delle risorse che sono attualmente gestite.

    ![Riquadro Individua le risorse](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Fare clic su **Individua le risorse** per avviare l'individuazione.

    ![Riquadro di individuazione](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Nel riquadro di individuazione usare il **filtro relativo allo stato della risorsa** e **Selezionare il tipo di risorsa** per filtrare i gruppi di gestione o le sottoscrizioni per cui si ha l'autorizzazione di scrittura. Probabilmente è più semplice iniziare selezionando **Tutti**.

    È possibile cercare e selezionare solo risorse per gruppi di gestione o per una sottoscrizione da gestire tramite PIM. Quando si gestisce un gruppo di gestione o una sottoscrizione in PIM, è possibile gestire anche le risorse figlio.

1. Aggiungere un segno di spunta accanto a tutte le risorse non gestite che si vuole gestire.

1. Fare clic su **Gestisci risorsa** per iniziare a gestire le risorse selezionate.

    > [!NOTE]
    > Una volta che una sottoscrizione o un gruppo di gestione è stato impostato come gestito, non può più essere non gestito. Questo impedisce che un altro amministratore delle risorse rimuova impostazioni di PIM.

    ![Individuazione - Gestisci risorsa](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Se viene visualizzato un messaggio per confermare l'onboarding della risorsa selezionata per la gestione, fare clic su **Sì**.

    ![Individuazione - Messaggio Gestisci risorsa](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni dei ruoli delle risorse di Azure in PIM](pim-resource-roles-configure-role-settings.md)
- [Assegnare i ruoli delle risorse di Azure in PIM](pim-resource-roles-assign-roles.md)
