---
title: Iniziare a usare PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come iniziare a usare Azure AD Privileged Identity Management (PIM) nel portale di Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: how-to
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bcfb21ab15355653780355f1b5e459bc806ec8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600722"
---
# <a name="start-using-privileged-identity-management"></a>Iniziare a usare Privileged Identity Management

Questo articolo descrive come abilitare Privileged Identity Management (PIM) e iniziare a usarlo.

Usare Privileged Identity Management (PIM) per gestire, controllare e monitorare l'accesso all'interno dell'organizzazione di Azure Active Directory (Azure AD). Con PIM è possibile fornire l'accesso JIT (just-in-Time) alle risorse di Azure, Azure AD risorse e altri Servizi online Microsoft, ad esempio Microsoft 365 o Microsoft Intune.

## <a name="prerequisites"></a>Prerequisiti

Per usare Privileged Identity Management, è necessario disporre di una delle licenze seguenti:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Per ulteriori informazioni, vedere [requisiti di licenza per l'utilizzo di Privileged Identity Management](subscription-requirements.md).

> [!Note]
> Quando un utente che è attivo in un ruolo con privilegi in un'organizzazione Azure AD con una licenza Premium P2 passa a **ruoli e amministratori** in Azure ad e seleziona un ruolo (o anche solo visite Privileged Identity Management):
>
> - PIM viene abilitato automaticamente per l'organizzazione
> - La loro esperienza è ora che possono assegnare un'assegnazione di ruolo "normale" o un'assegnazione di ruolo idonea
>
> Quando PIM è abilitato, non ha alcun effetto sull'organizzazione che è necessario preoccuparsi. Fornisce opzioni di assegnazione aggiuntive, ad esempio Active vs idonee con l'ora di inizio e di fine. PIM consente inoltre di definire l'ambito per le assegnazioni di ruolo utilizzando unità amministrative e ruoli personalizzati. Se si è un amministratore globale o un amministratore del ruolo con privilegi, è possibile iniziare a ricevere alcuni messaggi di posta elettronica aggiuntivi come il digest settimanale di PIM. È anche possibile vedere l'entità servizio MS-PIM nel log di controllo relativa all'assegnazione di ruolo. Si tratta di una modifica prevista che non dovrebbe avere alcun effetto sul flusso di lavoro.

## <a name="prepare-pim-for-azure-ad-roles"></a>Preparare PIM per i ruoli di Azure AD

Di seguito sono riportate le attività consigliate per preparare Privileged Identity Management per gestire i ruoli di Azure AD:

1. [Configurare le impostazioni del ruolo Azure ad](pim-how-to-change-default-settings.md).
1. [Assegnare le assegnazioni idonee](pim-how-to-add-role-to-user.md).
1. [Consentire agli utenti idonei di attivare il proprio ruolo di Azure ad JIT](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>Preparare PIM per i ruoli di Azure

Di seguito sono riportate le attività consigliate per preparare Privileged Identity Management per gestire i ruoli di Azure per una sottoscrizione:

1. [Individuare le risorse di Azure](pim-resource-roles-discover-resources.md)
1. [Configurare le impostazioni del ruolo di Azure](pim-resource-roles-configure-role-settings.md).
1. [Assegnare le assegnazioni idonee](pim-resource-roles-assign-roles.md).
1. [Consentire agli utenti idonei di attivare i ruoli di Azure just-in-Time](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>Accedere alle attività

Una volta configurata la Privileged Identity Management, è possibile ottenere informazioni sul percorso.

![Finestra di navigazione in Privileged Identity Management che mostra le attività e Gestisci le opzioni](./media/pim-getting-started/pim-quickstart-tasks.png)

| Attività e gestione | Descrizione |
| --- | --- |
| **Ruoli personali**  | Visualizza un elenco di ruoli idonei e attivi assegnati all'utente. Qui è possibile attivare tutti i ruoli idonei assegnati. |
| **Richieste personali** | Visualizza le richieste in sospeso per attivare le assegnazioni di ruolo idonee. |
| **Approvare le richieste** | Visualizza un elenco di richieste per attivare i ruoli idonei per gli utenti della directory che possono essere approvati. |
| **Verificare l'accesso** | Elenca le verifiche di accesso attive assegnate all'utente per essere completate, sia per se stesso che per altri utenti. |
| **Ruoli di Azure AD** | Visualizza un dashboard e le impostazioni per gli amministratori dei ruoli con privilegi per gestire le assegnazioni di ruolo Azure AD. Per gli utenti che non sono amministratori dei ruoli con privilegi, questo dashboard è disabilitato. Questi utenti possono accedere a un dashboard speciale denominato My view (Visualizzazione personalizzata). Il dashboard visualizzazione Visualizza solo le informazioni relative all'utente che accede al dashboard, non all'intera organizzazione. |
| **Risorse di Azure** | Visualizza un dashboard e le impostazioni per gli amministratori dei ruoli con privilegi per gestire le assegnazioni di ruolo delle risorse di Azure. Per gli utenti che non sono amministratori dei ruoli con privilegi, questo dashboard è disabilitato. Questi utenti possono accedere a un dashboard speciale denominato My view (Visualizzazione personalizzata). Il dashboard visualizzazione Visualizza solo le informazioni relative all'utente che accede al dashboard, non all'intera organizzazione. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Aggiungere un riquadro PIM al dashboard

Per semplificare l'apertura di Privileged Identity Management, aggiungere un riquadro PIM al dashboard di portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **tutti i servizi** e trovare il servizio **Azure ad Privileged Identity Management** .

    ![Azure AD Privileged Identity Management in Tutti i servizi](./media/pim-getting-started/pim-all-services-find.png)

1. Selezionare la Privileged Identity Management **avvio rapido**.

1. Selezionare **Aggiungi pannello al dashboard** per aggiungere la Privileged Identity Management pagina **avvio rapido** al dashboard.

    ![Icona puntina da disegno per aggiungere Privileged Identity Management pagina al dashboard](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Nel dashboard di Azure viene visualizzato un riquadro simile al seguente:

    ![Privileged Identity Management riquadro avvio rapido nel dashboard](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Gestire l'accesso alle risorse di Azure in Privileged Identity Management](pim-resource-roles-discover-resources.md)
