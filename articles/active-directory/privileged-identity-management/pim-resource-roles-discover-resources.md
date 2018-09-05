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
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: b5d48b3f854afaa79574e0ec13cff91f60396ac6
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190659"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Individuare le risorse di Azure per la gestione in PIM

Informazioni su come individuare e gestire le risorse di Azure quando si usa Privileged Identity Management (PIM) in Azure Active Directory (Azure AD). Queste informazioni possono essere utili alle organizzazioni che usano già PIM per proteggere le risorse amministrative e per i proprietari di sottoscrizioni che vogliono proteggere le risorse di produzione.

Quando si configura per la prima volta PIM per le risorse di Azure, è necessario individuare e selezionare le risorse da proteggere con PIM. Non ci sono limiti al numero di risorse che è possibile gestire con PIM. È tuttavia consigliabile iniziare con le risorse più cruciali, ossia quelle di produzione.

> [!NOTE]
> È possibile cercare e selezionare solo risorse per gruppi di gestione o per una sottoscrizione da gestire tramite PIM. Quando si gestisce un gruppo di gestione o una sottoscrizione in PIM, è possibile gestire anche le risorse figlio.

## <a name="discover-resources"></a>Individuare le risorse

Nel portale di Azure passare al riquadro **Privileged Identity Management**. Nel menu a sinistra, nella sezione **GESTISCI**, selezionare **Risorse di Azure**.

![Riquadro Privileged Identity Management - Risorse di Azure](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Se è la prima volta che si usa PIM per le risorse di Azure, eseguire prima l'individuazione per trovare le risorse da gestire. Nel riquadro **Risorse di Azure** selezionare il pulsante **Individua le risorse** per avviare l'esperienza di individuazione.

![Riquadro "Individua le risorse"](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Se un altro amministratore delle risorse o della directory nell'organizzazione sta già gestendo una risorsa di Azure con PIM o se è disponibile un'assegnazione di ruolo idonea per una risorsa, la visualizzazione elenco conterrà il messaggio **Individuare risorse o attivare un'assegnazione di ruolo idonea per continuare**. 

![Pulsante "Individua le risorse" nel riquadro "Privileged Identity Manager - Risorse di Azure"](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Quando si seleziona il pulsante **Individua le risorse**, sia dal menu in alto che nel centro del riquadro, viene visualizzato un elenco delle sottoscrizioni che è possibile gestire. Le sottoscrizioni evidenziate sono già protette con PIM.

> [!NOTE]
> Per impedire a un altro amministratore delle risorse di rimuovere impostazioni di PIM, una sottoscrizione impostata come gestita non può essere rimossa dalla gestione.

![Riquadro "Risorse di Azure - Individuazione"](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Nella colonna **RISORSA** posizionare il puntatore del mouse su una sottoscrizione che si vuole proteggere con PIM. Selezionare quindi la casella di controllo a sinistra del nome della risorsa. È possibile selezionare più sottoscrizioni alla volta.

![Elenco delle risorse nel riquadro "Risorse di Azure - Individuazione"](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Per iniziare il processo di onboarding, nel menu superiore selezionare **Gestisci risorsa**.

![Pulsante "Gestisci risorsa" nel riquadro "Risorse di Azure - Individuazione"](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Le risorse selezionate sono ora gestite da PIM. Per chiudere la schermata di individuazione, selezionare la **X** nell'angolo in alto a destra. Per iniziare a gestire le impostazioni di PIM e assegnare i membri, nel menu superiore del riquadro **Privileged Identity Management - Risorse di Azure** selezionare il pulsante **Aggiorna**.

![Pulsante "Aggiorna" nel menu superiore del riquadro "Privileged Identity Management - Risorse di Azure"](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni dei ruoli delle risorse di Azure in PIM](pim-resource-roles-configure-role-settings.md)
- [Assegnare i ruoli delle risorse di Azure in PIM](pim-resource-roles-assign-roles.md)
