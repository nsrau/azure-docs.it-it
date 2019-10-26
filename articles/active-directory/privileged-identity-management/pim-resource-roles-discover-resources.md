---
title: Individuare le risorse di Azure da gestire in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Informazioni su come scoprire risorse di Azure da gestire in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7e401426e1c9448caaae28648abce481f426d38
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895696"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Individuare le risorse di Azure da gestire in Privileged Identity Management

Usando Azure Active Directory (Azure AD) Privileged Identity Management (PIM), è possibile migliorare la protezione delle risorse di Azure. Questa operazione è utile per le organizzazioni che usano già Privileged Identity Management per proteggere i ruoli di Azure AD e per i proprietari di gruppi di gestione e sottoscrizioni che vogliono proteggere le risorse di produzione.

Quando si configurano per la prima volta Privileged Identity Management per le risorse di Azure, è necessario individuare e selezionare le risorse da proteggere con Privileged Identity Management. Non esiste alcun limite al numero di risorse che è possibile gestire con Privileged Identity Management. È tuttavia consigliabile iniziare con le risorse più cruciali, ossia quelle di produzione.

## <a name="discover-resources"></a>Individuare le risorse

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Risorse di Azure**.

    Se è la prima volta che si usa Privileged Identity Management per le risorse di Azure, verrà visualizzata una pagina **Discover Resources** .

    ![Riquadro individua risorse senza risorse elencate per la prima volta](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Se un altro amministratore dell'organizzazione sta già gestendo le risorse di Azure in Privileged Identity Management, verrà visualizzato un elenco delle risorse attualmente gestite.

    ![Riquadro individua risorse che elenca le risorse attualmente gestite](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Selezionare **individua risorse** per avviare l'esperienza di individuazione.

    ![Riquadro individuazione che elenca le risorse che possono essere gestite, ad esempio le sottoscrizioni e i gruppi di gestione](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Nella pagina **individuazione** usare **filtro stato risorsa** e selezionare il **tipo di risorsa** per filtrare i gruppi di gestione o le sottoscrizioni di cui si dispone dell'autorizzazione di scrittura. Probabilmente è più semplice iniziare selezionando **Tutti**.

    È possibile cercare e selezionare solo le risorse del gruppo di gestione o della sottoscrizione per gestire utilizzando Privileged Identity Management. Quando si gestisce un gruppo di gestione o una sottoscrizione in Privileged Identity Management, è anche possibile gestire le relative risorse figlio.

1. Selezionare la casella di controllo accanto a tutte le risorse non gestite che si desidera gestire.

1. Selezionare **Gestisci risorsa** per iniziare a gestire le risorse selezionate.

    > [!NOTE]
    > Una volta gestita, un gruppo di gestione o una sottoscrizione non può essere non gestita. In questo modo si impedisce a un altro amministratore di risorse di rimuovere Privileged Identity Management impostazioni.

    ![Riquadro individuazione con una risorsa selezionata e l'opzione Gestisci risorsa evidenziata](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Se viene visualizzato un messaggio per confermare l'onboarding della risorsa selezionata per la gestione, selezionare **Sì**.

    ![Messaggio che conferma l'onboarding delle risorse selezionate per la gestione](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo delle risorse di Azure in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Assegnare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-assign-roles.md)
