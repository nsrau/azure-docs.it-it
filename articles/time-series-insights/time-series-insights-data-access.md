---
title: Configure security to grant data access - Azure Time Series Insights Preview | Microsoft Docs
description: Learn how to configure security, permissions, and manage data access policies in your Azure Time Series Insights Preview environment.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/20/2019
ms.custom: seodec18
ms.openlocfilehash: b79ca1d93baf1941d5de8db0c314f9cd21e51056
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328146"
---
# <a name="grant-data-access-to-an-environment"></a>Concedere l'accesso ai dati in un ambiente

Questo articolo illustra i due tipi di criteri di accesso all'anteprima di Azure Time Series Insights.

> [!TIP]
> Read [Authentication and Authorization](time-series-insights-authentication-and-authorization.md) for Azure Active Directory app registration steps.

## <a name="sign-in-to-time-series-insights"></a>Sign in to Time Series Insights

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Individuare l'ambiente Time Series Insights. Immettere `Time Series` nella casella **Cerca**. Selezionare **ambiente Time Series** nei risultati della ricerca.
1. Selezionare l'ambiente Time Series Insights nell'elenco.

## <a name="grant-data-access"></a>Concedere l'accesso ai dati

Per concedere l'accesso ai dati per un'entità utente, eseguire queste operazioni.

1. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Aggiungi**.

    [![Select and add a Data Access Policy](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Scegliere **Seleziona utente**. Cercare il nome utente o l'indirizzo di posta elettronica per individuare l'utente che si vuole aggiungere. Select **Select** to confirm the selection.

    [![Select a user to add](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Scegliere **Seleziona ruolo**. Scegliere il ruolo di accesso appropriato per l'utente:

    * Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente.

    * In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali non condivise nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

    [![Confirm the selected role](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Selezionare **OK** nella pagina **Selezionare il ruolo utente**.

    [![Select OK on the Select User Role page](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Verificare che nella pagina **Criteri di accesso ai dati** siano elencati gli utenti e i ruoli per ogni utente.

    [![Verify the correct users and roles](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Provide guest access from another Azure AD tenant

The `Guest` role isn’t a management role. ma un termine usato per un account che viene invitato da un tenant a un altro. Dopo che l'account guest viene invitato nella directory del tenant, può disporre dello stesso controllo di accesso applicato alla directory come qualsiasi altro account. È possibile concedere l'accesso per la gestione a un ambiente Time Series Insights tramite il pannello di gestione delle identità e degli accessi. In alternativa, è possibile concedere l'accesso ai dati nell'ambiente tramite il pannello dei criteri di accesso ai dati. Per altre informazioni sull'accesso guest al tenant di Azure Active Directory (Azure AD), leggere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Per concedere l'accesso guest in un ambiente Time Series Insights a un utente AAD di un altro tenant, eseguire queste operazioni.

1. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Invita**.

    [![Select Data Access Polices, then + Invite](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Immettere l'indirizzo di posta elettronica per l'utente da invitare. Tale indirizzo deve essere associato ad Azure AD. Aggiungere un messaggio personale all'invito (facoltativo).

    [![Enter the email address to find the selected user](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Cercare il messaggio di conferma visualizzato sullo schermo.

    [![Look for the confirmation bubble to appear](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Scegliere **Seleziona utente**. Cercare l'indirizzo di posta elettronica dell'utente guest invitato per individuare l'utente da aggiungere. Then, **Select** to confirm the selection.

    [![Select the user and confirm the selection](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Scegliere **Seleziona ruolo**. Scegliere il ruolo di accesso appropriato per l'utente guest:

    * Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente.

    * In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali non condivise nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

    [![Confirm the role choice](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Selezionare **OK** nella pagina **Selezionare il ruolo utente**.

1. Verificare che nella pagina **Criteri di accesso ai dati** vengano elencati l'utente guest e i ruoli per ogni utente guest.

    [![Verify that users and roles are correctly assigned](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Now, the guest user will receive an invitation email at the email address specified above. The guest user will select **Get Started** to confirm their acceptance and connect to Azure Cloud.

    [![Guest selects Get Started to accept](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. After selecting **Get Started** the guest user will be presented with a permissions box associated with the administrator's organization. Upon granting permission by selecting **Accept**, they will be signed in.

    [![Guest reviews permissions and accepts](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. The administrator [shares the environment URL](time-series-insights-parameterized-urls.md) with their guest.

1. After the guest user is signed in to the email address you used to invite them, and they accept the invitation, they will be directed to Azure portal. 

1. The guest can now access the shared environment using the environment URL provided by the administrator. They can enter that URL into their web browser for immediate access.

1. The guest user will see the administrator's tenant by selecting their profile icon in the upper-right corner of the Time Series explorer.

    [![Avatar selection on insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    After the guest user selects the administrator's tenant, they will have the ability to select the shared Time Series Insights environment. 
    
    They now have all the capabilities associated with the role that you provided them with in **step 5**.

    [![Guest user selects your Azure tenant from drop-down](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [come aggiungere un'origine evento di Hub eventi di Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) all'ambiente Time Series Insights.

* Inviare [eventi all'origine evento](./time-series-insights-send-events.md).

* Visualizzare [l'ambiente nell'anteprima di Time Series Insights Explorer](./time-series-insights-update-explorer.md).
