---
title: Configurare la sicurezza per concedere l'accesso ai dati-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come configurare la sicurezza, le autorizzazioni e gestire i criteri di accesso ai dati nell'ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: shipra1mishra
ms.author: shmishr
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 35a2be5c9f418bc423ae56c456db0062677a33c2
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856123"
---
# <a name="grant-data-access-to-an-environment"></a>Concedere l'accesso ai dati in un ambiente

Questo articolo illustra i due tipi di criteri di accesso Azure Time Series Insights.

## <a name="sign-in-to-azure-time-series-insights"></a>Accedi per Azure Time Series Insights

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Individuare l'ambiente di Azure Time Series Insights immettendo `Time Series Insights environments` nella casella di **ricerca** . Selezionare `Time Series Insights environments` nei risultati della ricerca.
1. Selezionare l'ambiente Azure Time Series Insights dall'elenco.

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

    [![Fare clic su OK nella pagina Selezione ruolo utente](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Verificare che nella pagina **Criteri di accesso ai dati** siano elencati gli utenti e i ruoli per ogni utente.

    [![Verificare gli utenti e i ruoli corretti](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Fornire l'accesso Guest da un altro tenant Azure AD

Il `Guest` ruolo non è un ruolo di gestione. Si tratta di un termine usato per un account che viene invitato da un tenant a un altro. Dopo che l'account Guest è stato invitato nella directory del tenant, è possibile che venga applicato lo stesso controllo di accesso come qualsiasi altro account. È possibile concedere l'accesso di gestione a un ambiente Azure Time Series Insights usando il pannello controllo di accesso (IAM). In alternativa, è possibile concedere l'accesso ai dati nell'ambiente tramite il pannello dei criteri di accesso ai dati. Per altre informazioni sull'accesso guest al tenant di Azure Active Directory (Azure AD), leggere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Seguire questa procedura per concedere l'accesso Guest a un ambiente Azure Time Series Insights a un utente Azure AD da un altro tenant.

1. Passare a portale di Azure, fare clic su  **Azure Active Directory**, scorrere verso il basso nella scheda **Panoramica** e quindi selezionare **utente Guest**.

    [![Selezionare i criteri di accesso ai dati, quindi + invita](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Immettere l'indirizzo di posta elettronica per l'utente da invitare. Tale indirizzo deve essere associato ad Azure AD. Aggiungere un messaggio personale all'invito (facoltativo).

    [![Immettere l'indirizzo di posta elettronica per trovare l'utente selezionato](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Cercare il messaggio di conferma visualizzato sullo schermo. È anche possibile fare clic su **notifiche** per confermare che l'utente Guest è stato aggiunto.

    [![Cerca la bolla di conferma da visualizzare](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Tornare all'ambiente di Time Series Insights per aggiungere l'utente guest appena creato. Fare clic su **criteri di accesso ai dati** come descritto in **concedere l'accesso ai dati**. **Selezionare utente**. Cercare l'indirizzo di posta elettronica dell'utente guest invitato per individuare l'utente da aggiungere. **Selezionare** quindi per confermare la selezione.

    [![Selezionare l'utente e confermare la selezione](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Scegliere **Seleziona ruolo**. Scegliere il ruolo di accesso appropriato per l'utente guest:

    * Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente.

    * In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali non condivise nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

    [![Confermare la scelta del ruolo](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Selezionare **OK** nella pagina **Selezionare il ruolo utente**.

1. Verificare che nella pagina **Criteri di accesso ai dati** vengano elencati l'utente guest e i ruoli per ogni utente guest.

    [![Verificare che gli utenti e i ruoli siano assegnati correttamente](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. A questo punto, l'utente Guest riceverà un messaggio di posta elettronica di invito all'indirizzo di posta elettronica specificato in precedenza. L'utente Guest seleziona **inizia** a confermare l'accettazione e si connette al cloud di Azure.

    [![Guest Select get started to Accept](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Dopo aver selezionato **Introduzione**, l'utente guest verrà visualizzato con una casella autorizzazioni associata all'organizzazione dell'amministratore. Quando si concede l'autorizzazione selezionando **accetta**, l'accesso verrà eseguito.

    [![Revisioni Guest autorizzazioni e accetta](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. L'amministratore [condivide l'URL dell'ambiente](time-series-insights-parameterized-urls.md) con il proprio Guest.

1. Dopo che l'utente Guest ha eseguito l'accesso all'indirizzo di posta elettronica usato per invitare i clienti e accetta l'invito, viene indirizzato a portale di Azure.

1. Il Guest ora può accedere all'ambiente condiviso usando l'URL dell'ambiente fornito dall'amministratore. Possono immettere tale URL nel Web browser per l'accesso immediato.

1. Il tenant dell'amministratore verrà visualizzato all'utente guest dopo aver selezionato l'icona del profilo nell'angolo superiore destro di Esplora serie temporali.

    [![Selezione avatar in insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    Dopo che l'utente Guest ha selezionato il tenant dell'amministratore, potrà selezionare l'ambiente di Azure Time Series Insights condiviso.

    Ora hanno tutte le funzionalità associate al ruolo a cui sono state fornite nel **passaggio 5**.

    [![L'utente Guest seleziona il tenant di Azure dall'elenco a discesa](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Leggere [l'autenticazione e l'autorizzazione](time-series-insights-authentication-and-authorization.md) per la procedura di registrazione dell'app Azure Active Directory.

* Visualizzare [l'ambiente in Azure Time Series Insights TSI Explorer](./time-series-insights-update-explorer.md).
