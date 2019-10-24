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
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0ba7846b60ca6649b4342d5096e92dfd8c96601
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756303"
---
# <a name="start-using-privileged-identity-management"></a>Iniziare a usare Privileged Identity Management

Con Privileged Identity Management (PIM), è possibile gestire, controllare e monitorare l'accesso all'interno dell'organizzazione di Azure Active Directory (Azure AD). Questo ambito include l'accesso alle risorse di Azure, Azure AD e altri Servizi online Microsoft, ad esempio Office 365 o Microsoft Intune.

Questo articolo descrive come abilitare e iniziare a usare Privileged Identity Management.

## <a name="prerequisites"></a>Prerequisiti

Per usare Privileged Identity Management, è necessario disporre di una delle licenze seguenti:

- Azure AD P2 Premium
- Enterprise Mobility + Security (EMS) E5

Per ulteriori informazioni, vedere [requisiti di licenza per l'utilizzo di Privileged Identity Management](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Prima persona a usare PIM

Se si è la prima persona a usare Privileged Identity Management nella directory, vengono assegnati automaticamente i ruoli [amministratore della sicurezza](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) e [amministratore del ruolo con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) nella directory. Solo gli amministratori dei ruoli con privilegi possono gestire Azure AD assegnazioni di ruolo degli utenti. È inoltre possibile scegliere di eseguire la [procedura guidata relativa alla sicurezza](pim-security-wizard.md) che guida nei passaggi inziali di individuazione e assegnazione.

## <a name="enable-pim"></a>Abilitare PIM

Per iniziare a usare Privileged Identity Management nella directory, è necessario abilitare prima Privileged Identity Management.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale della directory.

    Per abilitare Privileged Identity Management per una directory, è necessario essere un amministratore globale con un account aziendale, ad esempio @yourdomain.com, non un account Microsoft, ad esempio @outlook.com.

1. Fare clic su **Tutti i servizi** e trovare il servizio **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management in Tutti i servizi](./media/pim-getting-started/pim-all-services-find.png)

1. Fare clic per aprire la Guida introduttiva Privileged Identity Management.

1. Nell'elenco fare clic su **Concedi consenso per PIM**.

    ![Consenso a Privileged Identity Management per abilitare Privileged Identity Management](./media/pim-getting-started/consent-pim.png)

1. Fare clic su **Verifica l'identità dell'utente** per verificare l'identità con Azure MFA. Verrà chiesto di scegliere un account.

    ![Selezionare una finestra dell'account per verificare la propria identità](./media/pim-getting-started/pick-account.png)

1. Se sono necessarie altre informazioni per la verifica, si verrà guidati nel processo. Per altre informazioni, vedere [Informazioni sulla verifica in due passaggi](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Altre informazioni richiedono la finestra se l'organizzazione necessita di ulteriori informazioni](./media/pim-getting-started/more-information-required.png)

    Ad esempio, potrebbe venire chiesto di fornire la verifica telefonica.

    ![Pagina di verifica aggiuntiva di sicurezza che chiede come contattare l'utente](./media/pim-getting-started/additional-security-verification.png)

1. Dopo aver completato il processo di verifica, fare clic sul pulsante **Consenso**.

1. Nel messaggio visualizzato, fare clic su **Sì** per acconsentire al servizio Privileged Identity Management.

    ![Consenso a Privileged Identity Management messaggio per completare il processo di consenso](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Iscrivere PIM ai ruoli di Azure AD

Dopo aver abilitato Privileged Identity Management per la directory, è necessario iscriversi Privileged Identity Management per gestire i ruoli di Azure AD.

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD**.

    ![Iscriversi Privileged Identity Management per i ruoli di Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Fare clic su **Iscrizione**.

1. Nel messaggio visualizzato, fare clic su **Sì** per iscriversi Privileged Identity Management per gestire i ruoli di Azure ad.

    ![Messaggio di Privileged Identity Management di iscrizione per Azure AD Roles](./media/pim-getting-started/sign-up-pim-message.png)

    Al termine dell'iscrizione, verranno abilitate le opzioni di Azure AD. Potrebbe essere necessario aggiornare il portale.

    Per informazioni su come individuare e selezionare le risorse di Azure da proteggere con Privileged Identity Management, vedere [individuare le risorse di Azure da gestire in Privileged Identity Management](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Accedere alle attività

Una volta configurata la Privileged Identity Management, è possibile avviare le attività di gestione delle identità.

![Finestra di navigazione in Privileged Identity Management che mostra le attività e Gestisci le opzioni](./media/pim-getting-started/pim-quickstart-tasks.png)

| Attività e gestione | Description |
| --- | --- |
| **Ruoli personali**  | Visualizza un elenco di ruoli idonei e attivi assegnati all'utente. Qui è possibile attivare tutti i ruoli idonei assegnati. |
| **Richieste personali** | Visualizza le richieste in sospeso per attivare le assegnazioni di ruolo idonee. |
| **Approvare le richieste** | Visualizza un elenco di richieste per attivare i ruoli idonei per gli utenti della directory che possono essere approvati. |
| **Verificare l'accesso** | Elenca le verifiche di accesso attive assegnate all'utente per essere completate, sia per se stesso che per altri utenti. |
| **Ruoli di Azure AD** | Visualizza un dashboard e le impostazioni per gli amministratori dei ruoli con privilegi per gestire le assegnazioni di ruolo Azure AD. Per gli utenti che non sono amministratori dei ruoli con privilegi, questo dashboard è disabilitato. Questi utenti possono accedere a un dashboard speciale denominato My view (Visualizzazione personalizzata). Il dashboard My view (Visualizzazione personalizzata) visualizza solo informazioni relative all'accesso dell'utente al dashboard, non l'intero tenant. |
| **Risorse di Azure** | Visualizza un dashboard e le impostazioni per gli amministratori dei ruoli con privilegi per gestire le assegnazioni dei ruoli della risorsa di Azure AD. Per gli utenti che non sono amministratori dei ruoli con privilegi, questo dashboard è disabilitato. Questi utenti possono accedere a un dashboard speciale denominato My view (Visualizzazione personalizzata). Il dashboard My view (Visualizzazione personalizzata) visualizza solo informazioni relative all'accesso dell'utente al dashboard, non l'intero tenant. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Aggiungere un riquadro PIM al dashboard

Per semplificare l'apertura di Privileged Identity Management, è necessario aggiungere un riquadro Privileged Identity Management al dashboard di portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Fare clic su **Tutti i servizi** e trovare il servizio **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management in Tutti i servizi](./media/pim-getting-started/pim-all-services-find.png)

1. Fare clic per aprire la Guida introduttiva Privileged Identity Management.

1. Selezionare il **Pannello Aggiungi al dashboard** per aggiungere il pannello avvio rapido Privileged Identity Management al dashboard.

    ![Icona a puntina da disegno per aggiungere Privileged Identity Management pannello al dashboard](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Nel dashboard di Azure viene visualizzato un riquadro simile al seguente:

    ![Riquadro avvio rapido Privileged Identity Management nel dashboard](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Individuare le risorse di Azure da gestire in Privileged Identity Management](pim-resource-roles-discover-resources.md)
