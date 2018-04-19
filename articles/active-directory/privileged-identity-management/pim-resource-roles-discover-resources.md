---
title: Privileged Identity Management per le risorse di Azure - Individuare e gestire le risorse di Azure | Microsoft Docs
description: Descrive come proteggere le risorse di Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>Individuare e gestire le risorse di Azure

Questo articolo offre informazioni utili se l'organizzazione usa già Azure AD Privileged Identity Management per proteggere gli amministratori nella directory oppure per i proprietari di una sottoscrizione con l'esigenza di proteggere le risorse di produzione.

Quando si abilita per la prima volta PIM per le risorse di Azure, è necessario individuare e selezionare le risorse da proteggere con PIM. Non esiste un limite al numero di risorse che è possibile gestire con PIM, ma è consigliabile iniziare con le risorse più critiche (produzione).

> [!Note]
> Solo le risorse della sottoscrizione possono essere cercate e selezionate per la gestione. La gestione verrà abilitata anche per tutte le risorse figlio quando si sceglie di gestire una sottoscrizione in PIM.

## <a name="discover-resources"></a>Individuare le risorse

Passare ad Azure AD Privileged Identity Management e selezionare le risorse di Azure nella sezione Gestisci del menu di spostamento a sinistra.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Per il primo uso di PIM per le risorse di Azure sarà necessario eseguire l'individuazione per trovare le risorse da gestire.
Fare clic sul pulsante "Discover resources" (Individua risorse) al centro dello schermo per avviare l'esperienza di individuazione.

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Se un altro amministratore delle risorse o della directory nell'organizzazione sta già gestendo una risorsa di Azure con PIM o è disponibile un'assegnazione di ruolo idonea per una risorsa, la visualizzazione elenco conterrà il messaggio: "Discover resources or activate an eligible role assignment to continue" (Individuare le risorse o attivare un'assegnazione di ruolo idonea per continuare). 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Quando si seleziona il pulsante nella barra delle azioni o al centro dello schermo per individuare le risorse, verrà visualizzato un elenco delle sottoscrizioni disponibili per la gestione. A questo punto, se vengono visualizzate sottoscrizioni evidenziate ciò indica che sono protette da PIM.

> [!Note]
> Per impedire a un altro amministratore delle risorse di rimuovere impostazioni di PIM, una sottoscrizione aggiunta alla gestione non può essere rimossa dalla gestione.

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Passare il mouse su una sottoscrizione che si vuole proteggere con PIM e selezionare la casella all'estrema sinistra della riga. È possibile selezionare più sottoscrizioni alla volta.

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Per avviare il processo di onboarding, selezionare il pulsante "Gestisci la risorsa" nella barra nella parte superiore della schermata.

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Le risorse selezionate sono ora gestite da PIM. Chiudere la schermata di individuazione mediante la "X" nell'angolo superiore destro della pagina e fare clic su Aggiorna nella barra nella parte superiore della schermata di gestione delle risorse di Azure per iniziare a gestire le impostazioni di PIM e l'assegnazione di membri.

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Passaggi successivi

[Configurare le impostazioni dei ruoli](pim-resource-roles-configure-role-settings.md)

[Assegnare i ruoli in PIM](pim-resource-roles-assign-roles.md)
