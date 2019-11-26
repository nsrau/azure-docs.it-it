---
title: Configurare la sicurezza per concedere l'accesso ai dati-Azure Time Series Insights anteprima | Microsoft Docs
description: Informazioni su come configurare la sicurezza, le autorizzazioni e gestire i criteri di accesso ai dati nell'ambiente di Azure Time Series Insights Preview.
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
> Leggere [l'autenticazione e l'autorizzazione](time-series-insights-authentication-and-authorization.md) per la procedura di registrazione dell'app Azure Active Directory.

## <a name="sign-in-to-time-series-insights"></a>Accedi per Time Series Insights

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Individuare l'ambiente Time Series Insights. Immettere `Time Series` nella casella **Cerca**. Selezionare **ambiente Time Series** nei risultati della ricerca.
1. Selezionare l'ambiente Time Series Insights nell'elenco.

## <a name="grant-data-access"></a>Concedere l'accesso ai dati

Per concedere l'accesso ai dati per un'entità utente, eseguire queste operazioni.

1. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Aggiungi**.

    [![selezionare e aggiungere un criterio di accesso ai dati](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Scegliere **Seleziona utente**. Cercare il nome utente o l'indirizzo di posta elettronica per individuare l'utente che si vuole aggiungere. Selezionare **Seleziona** per confermare la selezione.

    [![selezionare un utente da aggiungere](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Scegliere **Seleziona ruolo**. Scegliere il ruolo di accesso appropriato per l'utente:

    * Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente.

    * In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali non condivise nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

    [![confermare il ruolo selezionato](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Selezionare **OK** nella pagina **Selezionare il ruolo utente**.

    [![selezionare OK nella pagina Selezione ruolo utente](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Verificare che nella pagina **Criteri di accesso ai dati** siano elencati gli utenti e i ruoli per ogni utente.

    [![verificare gli utenti e i ruoli corretti](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Fornire l'accesso Guest da un altro tenant Azure AD

Il ruolo `Guest` non è un ruolo di gestione. ma un termine usato per un account che viene invitato da un tenant a un altro. Dopo che l'account guest viene invitato nella directory del tenant, può disporre dello stesso controllo di accesso applicato alla directory come qualsiasi altro account. È possibile concedere l'accesso per la gestione a un ambiente Time Series Insights tramite il pannello di gestione delle identità e degli accessi. In alternativa, è possibile concedere l'accesso ai dati nell'ambiente tramite il pannello dei criteri di accesso ai dati. Per altre informazioni sull'accesso guest al tenant di Azure Active Directory (Azure AD), leggere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Per concedere l'accesso guest in un ambiente Time Series Insights a un utente AAD di un altro tenant, eseguire queste operazioni.

1. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Invita**.

    [![selezionare i criteri di accesso ai dati, quindi + invita](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Immettere l'indirizzo di posta elettronica per l'utente da invitare. Tale indirizzo deve essere associato ad Azure AD. Aggiungere un messaggio personale all'invito (facoltativo).

    [![immettere l'indirizzo di posta elettronica per trovare l'utente selezionato](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Cercare il messaggio di conferma visualizzato sullo schermo.

    [![cercare la bolla di conferma da visualizzare](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Scegliere **Seleziona utente**. Cercare l'indirizzo di posta elettronica dell'utente guest invitato per individuare l'utente da aggiungere. **Selezionare** quindi per confermare la selezione.

    [![selezionare l'utente e confermare la selezione](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Scegliere **Seleziona ruolo**. Scegliere il ruolo di accesso appropriato per l'utente guest:

    * Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente.

    * In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali non condivise nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

    [![confermare la scelta del ruolo](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Selezionare **OK** nella pagina **Selezionare il ruolo utente**.

1. Verificare che nella pagina **Criteri di accesso ai dati** vengano elencati l'utente guest e i ruoli per ogni utente guest.

    [![verificare che utenti e ruoli siano assegnati correttamente](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. A questo punto, l'utente Guest riceverà un messaggio di posta elettronica di invito all'indirizzo di posta elettronica specificato in precedenza. L'utente Guest seleziona **inizia** a confermare l'accettazione e si connette al cloud di Azure.

    [![Guest seleziona inizia a accettare](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Dopo aver selezionato **inizia** , l'utente guest verrà visualizzato con una casella autorizzazioni associata all'organizzazione dell'amministratore. Quando si concede l'autorizzazione selezionando **accetta**, l'accesso verrà eseguito.

    [![le autorizzazioni e accetta le revisioni Guest](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. L'amministratore [condivide l'URL dell'ambiente](time-series-insights-parameterized-urls.md) con il proprio Guest.

1. Dopo che l'utente Guest ha eseguito l'accesso all'indirizzo di posta elettronica usato per invitare i clienti e accetta l'invito, viene indirizzato a portale di Azure. 

1. Il Guest ora può accedere all'ambiente condiviso usando l'URL dell'ambiente fornito dall'amministratore. Possono immettere tale URL nel Web browser per l'accesso immediato.

1. L'utente Guest visualizzerà il tenant dell'amministratore selezionando l'icona del profilo nell'angolo superiore destro di Esplora serie temporali.

    [![selezione avatar in insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    Dopo che l'utente Guest ha selezionato il tenant dell'amministratore, potrà selezionare l'ambiente di Time Series Insights condiviso. 
    
    Ora hanno tutte le funzionalità associate al ruolo a cui sono state fornite nel **passaggio 5**.

    [![utente Guest seleziona il tenant di Azure dall'elenco a discesa](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [come aggiungere un'origine evento di Hub eventi di Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) all'ambiente Time Series Insights.

* Inviare [eventi all'origine evento](./time-series-insights-send-events.md).

* Visualizzare [l'ambiente nell'anteprima di Time Series Insights Explorer](./time-series-insights-update-explorer.md).
