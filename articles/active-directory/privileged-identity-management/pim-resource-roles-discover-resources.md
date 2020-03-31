---
title: Individuale le risorse di Azure da gestire in PIM - Azure AD Documenti Microsoft
description: Informazioni su come scoprire risorse di Azure da gestire in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022882"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Individuazione delle risorse di Azure da gestire in Gestione identità con privilegiDiscover Azure resources to manage in Privileged Identity Management

Usando Azure Active Directory (Azure AD) Privileged Identity Management (PIM), è possibile migliorare la protezione delle risorse di Azure.Using Azure Active Directory (Azure AD) Privileged Identity Management (PIM), you can improve the protection of your Azure resources. Ciò è utile per le organizzazioni che usano già la gestione delle identità con privilegi per proteggere i ruoli di Azure AD e per i proprietari di gruppi di gestione e sottoscrizioni che desiderano proteggere le risorse di produzione.

Quando si configura per la prima volta La gestione delle identità con privilegi per le risorse di Azure, è necessario individuare e selezionare le risorse da proteggere con Gestione identità con privilegi. Non esiste alcun limite al numero di risorse che è possibile gestire con Gestione identità con privilegi. È tuttavia consigliabile iniziare con le risorse più cruciali, ossia quelle di produzione.

## <a name="discover-resources"></a>Individuare le risorse

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Risorse di Azure**.

    Se è la prima volta che usi Privileged Identity Management per le risorse di Azure, verrà visualizzata una pagina **Scopri risorse.**

    ![Riquadro Delle risorse di individuazione senza risorse elencate per la prima esperienza](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Se un altro amministratore dell'organizzazione gestisce già le risorse di Azure in Gestione delle identità con privilegi, verrà visualizzato un elenco delle risorse attualmente gestite.

    ![Riquadro risorse in cui sono elencate le risorse attualmente gestite](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Selezionare **Individua risorse** per avviare l'esperienza di individuazione.

    ![Riquadro di individuazione in cui sono elencate le risorse che possono essere gestite, ad esempio sottoscrizioni e gruppi di gestione](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Nella pagina **Individuazione** usare **Filtro stato risorse** e Seleziona tipo di risorsa per **filtrare** i gruppi di gestione o le sottoscrizioni a cui si dispone dell'autorizzazione di scrittura. Probabilmente è più semplice iniziare selezionando **Tutti**.

    È possibile cercare e selezionare solo le risorse di gruppo di gestione o di sottoscrizione da gestire tramite Gestione identità con privilegi. Quando si gestisce un gruppo di gestione o una sottoscrizione in Gestione identità con privilegi, è anche possibile gestire le relative risorse figlio.

1. Selezionare la casella di controllo accanto alle risorse non gestite che si desidera gestire.

1. Selezionare **Gestisci risorsa** per iniziare a gestire le risorse selezionate.

    > [!NOTE]
    > Una volta gestito un gruppo di gestione o una sottoscrizione, non può essere gestita. Ciò impedisce a un altro amministratore delle risorse di rimuovere le impostazioni di Gestione identità con privilegi.

    ![Riquadro di individuazione con una risorsa selezionata e l'opzione Gestisci risorsa evidenziata](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Se viene visualizzato un messaggio per confermare l'onboarding della risorsa selezionata per la gestione, selezionare **Sì**.

    ![Messaggio di conferma dell'onboarding delle risorse selezionate per la gestione](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo delle risorse di Azure in Gestione delle identità con privilegiConfigure Azure resource role settings in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Assegnare ruoli delle risorse di Azure in Gestione identità con privilegiAssign Azure resource roles in Privileged Identity Management](pim-resource-roles-assign-roles.md)
