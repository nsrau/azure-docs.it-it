---
title: Iniziare a usare PIM - Azure | Microsoft Docs
description: Informazioni su come iniziare a usare Azure AD Privileged Identity Management (PIM) nel portale di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5b3bff27821964648713b02589c941c99e3eb03d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190090"
---
# <a name="start-using-pim"></a>Iniziare a usare PIM

Con Azure Active Directory Privileged Identity Management (PIM) è possibile gestire, controllare e monitorare l'accesso nell'organizzazione. Questo ambito include l'accesso alle risorse di Azure, Azure AD e altri servizi Microsoft online, ad esempio Office 365 o Microsoft Intune.

Questo articolo descrive come aggiungere l'app Azure AD PIM al dashboard del portale di Azure.

## <a name="first-person-to-use-pim"></a>Prima persona a usare PIM

Alla prima persona che usa PIM nella directory vengono assegnati automaticamente i ruoli di [amministratore della sicurezza](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) e [amministratori dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) nella directory. Solo gli amministratori del ruolo con privilegi possono gestire le assegnazioni di ruoli della directory Azure AD degli utenti. È anche possibile scegliere di eseguire la [procedura guidata per la sicurezza](pim-security-wizard.md), che illustra in modo dettagliato l'esperienza di individuazione e assegnazione iniziale.

## <a name="add-pim-tile-to-the-dashboard"></a>Aggiungere il riquadro PIM al dashboard

Per semplificare l'apertura di PIM, è necessario aggiungere un riquadro PIM al dashboard del portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale della directory.

1. Fare clic su **Tutti i servizi** e trovare il servizio **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management in Tutti i servizi](./media/pim-getting-started/pim-all-services-find.png)

1. Fare clic per aprire il riquadro di avvio rapido di PIM.

1. Selezionare **Pin blade to dashboard** (Aggiungi pannello al dashboard) per aggiungere il pannello di avvio rapido di PIM al dashboard.

    ![Aggiungi pannello al dashboard](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Nel dashboard di Azure viene visualizzato un riquadro simile al seguente:

    ![Riquadro di avvio rapido di PIM](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="navigate-to-your-tasks"></a>Accedere alle attività

Dopo aver configurato PIM, è possibile usare questo pannello per eseguire le attività di gestione delle identità.

![Attività principali per PIM - Screenshot](./media/pim-getting-started/pim-quickstart-tasks.png)

| Attività e gestione | DESCRIZIONE |
| --- | --- |
| **Ruoli personali**  | Visualizza un elenco di ruoli idonei e attivi assegnati all'utente. Qui è possibile attivare tutti i ruoli idonei assegnati. |
| **Richieste personali** | Visualizza le richieste in sospeso per attivare le assegnazioni di ruolo idonee. |
| **Accesso a un'applicazione** | Consente di ridurre possibili ritardi e di usare un ruolo immediatamente dopo l'attivazione. |
| **Approvare le richieste** | Visualizza un elenco di richieste per attivare i ruoli idonei per gli utenti della directory che possono essere approvati. |
| **Verificare l'accesso** | Elenca le verifiche di accesso attive assegnate all'utente per essere completate, sia per se stesso che per altri utenti. |
| **Ruoli della directory di Azure AD** | Visualizza un dashboard e le impostazioni per gli amministratori dei ruoli con privilegi per gestire le assegnazioni dei ruoli della directory di Azure AD. Per gli utenti che non sono amministratori dei ruoli con privilegi, questo dashboard è disabilitato. Questi utenti possono accedere a un dashboard speciale denominato My view (Visualizzazione personalizzata). Il dashboard My view (Visualizzazione personalizzata) visualizza solo informazioni relative all'accesso dell'utente al dashboard, non l'intero tenant. |
| **Risorse di Azure** | Visualizza un dashboard e le impostazioni per gli amministratori dei ruoli con privilegi per gestire le assegnazioni dei ruoli della risorsa di Azure AD. Per gli utenti che non sono amministratori dei ruoli con privilegi, questo dashboard è disabilitato. Questi utenti possono accedere a un dashboard speciale denominato My view (Visualizzazione personalizzata). Il dashboard My view (Visualizzazione personalizzata) visualizza solo informazioni relative all'accesso dell'utente al dashboard, non l'intero tenant. |

## <a name="next-steps"></a>Passaggi successivi

- [Attivare i ruoli della directory di Azure AD in PIM](pim-how-to-activate-role.md)
- [Attivare i ruoli di risorse di Azure in PIM](pim-resource-roles-activate-your-roles.md)
