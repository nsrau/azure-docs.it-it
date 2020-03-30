---
title: Configurare la sicurezza per concedere l'accesso ai dati - Azure Time Series Insights Preview Documenti Microsoft
description: Informazioni su come configurare la sicurezza, le autorizzazioni e gestire i criteri di accesso ai dati nell'ambiente Azure Time Series Insights Preview.Learn how to configure security, permissions, and manage data access policies in your Azure Time Series Insights Preview environment.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: 1c8f14bb1bca082a9d887e5d6d88aec213448c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254352"
---
# <a name="grant-data-access-to-an-environment"></a>Concedere l'accesso ai dati in un ambiente

Questo articolo illustra i due tipi di criteri di accesso all'anteprima di Azure Time Series Insights.

> [!TIP]
> Leggere i passaggi di [autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md) per i passaggi di registrazione dell'app Azure Active Directory.Read Authentication and Authorization for Azure Active Directory app registration steps.

## <a name="sign-in-to-time-series-insights"></a>Accedi a Time Series Insights

1. Accedere al [portale](https://portal.azure.com/)di Azure .
1. Individuare l'ambiente Time Series Insights. Immettere `Time Series` nella casella **Cerca**. Selezionare **ambiente Time Series** nei risultati della ricerca.
1. Selezionare l'ambiente Time Series Insights nell'elenco.

## <a name="grant-data-access"></a>Concedere l'accesso ai dati

Per concedere l'accesso ai dati per un'entità utente, eseguire queste operazioni.

1. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Aggiungi**.

    [![Selezionare e aggiungere un criterio di accesso ai dati](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Scegliere **Seleziona utente**. Cercare il nome utente o l'indirizzo di posta elettronica per individuare l'utente che si vuole aggiungere. Selezionare **Seleziona** per confermare la selezione.

    [![Selezionare un utente da aggiungere](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Scegliere **Seleziona ruolo**. Scegliere il ruolo di accesso appropriato per l'utente:

    * Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente.

    * In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali non condivise nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

    [![Confermare il ruolo selezionato](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Selezionare **OK** nella pagina **Selezionare il ruolo utente**.

    [![Selezionare OK nella pagina Seleziona ruolo utente](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Verificare che nella pagina **Criteri di accesso ai dati** siano elencati gli utenti e i ruoli per ogni utente.

    [![Verificare gli utenti e i ruoli corretti](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Fornire l'accesso guest da un altro tenant di Azure ADProvide guest access from another Azure AD tenant

Il `Guest` ruolo non è un ruolo di gestione. ma un termine usato per un account che viene invitato da un tenant a un altro. Dopo che l'account guest viene invitato nella directory del tenant, può disporre dello stesso controllo di accesso applicato alla directory come qualsiasi altro account. È possibile concedere l'accesso per la gestione a un ambiente Time Series Insights tramite il pannello di gestione delle identità e degli accessi. In alternativa, è possibile concedere l'accesso ai dati nell'ambiente tramite il pannello dei criteri di accesso ai dati. Per altre informazioni sull'accesso guest al tenant di Azure Active Directory (Azure AD), leggere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Per concedere l'accesso guest in un ambiente Time Series Insights a un utente AAD di un altro tenant, eseguire queste operazioni.

1. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Invita**.

    [![Seleziona Criteri di accesso ai dati, quindi](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Immettere l'indirizzo di posta elettronica per l'utente da invitare. Tale indirizzo deve essere associato ad Azure AD. Aggiungere un messaggio personale all'invito (facoltativo).

    [![Immettere l'indirizzo di posta elettronica per trovare l'utente selezionato](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Cercare il messaggio di conferma visualizzato sullo schermo.

    [![Cercare il fumetto di conferma da visualizzare](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Scegliere **Seleziona utente**. Cercare l'indirizzo di posta elettronica dell'utente guest invitato per individuare l'utente da aggiungere. Quindi, **selezionare** per confermare la selezione.

    [![Selezionare l'utente e confermare la selezione](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Scegliere **Seleziona ruolo**. Scegliere il ruolo di accesso appropriato per l'utente guest:

    * Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente.

    * In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali non condivise nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

    [![Confermare la scelta del ruolo](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Selezionare **OK** nella pagina **Selezionare il ruolo utente**.

1. Verificare che nella pagina **Criteri di accesso ai dati** vengano elencati l'utente guest e i ruoli per ogni utente guest.

    [![Verificare che gli utenti e i ruoli siano assegnati correttamente](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. A questo punto, l'utente guest riceverà un'e-mail di invito all'indirizzo di posta elettronica specificato in precedenza. L'utente guest selezionerà Inizia per confermare l'accettazione e connettersi al cloud di Azure.The guest user will select **Get Started** to confirm their acceptance and connect to Azure Cloud.

    [![Guest seleziona Inizia ad accettare](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Dopo aver selezionato **Inizia**, all'utente guest verrà visualizzata una casella di autorizzazioni associata all'organizzazione dell'amministratore. Dopo aver concesso l'autorizzazione selezionando **Accetta**, verrà effettuato l'accesso.

    [![Gli ospiti esaminano le autorizzazioni e accetta](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. L'amministratore [condivide l'URL dell'ambiente](time-series-insights-parameterized-urls.md) con il guest.

1. Dopo che l'utente guest ha eseguito l'accesso all'indirizzo di posta elettronica usato per invitarlo e accetta l'invito, verrà indirizzato al portale di Azure.After the guest user is signed in to the email address you used to invite them, and they accept the invitation, they will be directed to Azure portal. 

1. Il guest può ora accedere all'ambiente condiviso utilizzando l'URL dell'ambiente fornito dall'amministratore. Possono inserire l'URL nel browser web per l'accesso immediato.

1. Il tenant dell'amministratore verrà visualizzato all'utente guest dopo aver selezionato l'icona del profilo nell'angolo superiore destro di Esplora serie temporali.

    [![Selezione Avatar su insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    Dopo che l'utente guest ha selezionato il tenant dell'amministratore, avrà la possibilità di selezionare l'ambiente di Time Series Insights condiviso. 
    
    Ora hanno tutte le funzionalità associate al ruolo che è stato fornito con nel **passaggio 5**.

    [![L'utente guest seleziona il tenant di Azure dall'elenco a discesaGuest user selects your Azure tenant from drop-down](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [come aggiungere un'origine evento di Hub eventi di Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) all'ambiente Time Series Insights.

* Inviare [eventi all'origine evento](./time-series-insights-send-events.md).

* Visualizzare [l'ambiente nell'anteprima di Time Series Insights Explorer](./time-series-insights-update-explorer.md).
