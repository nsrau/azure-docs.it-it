---
title: Configurare la sicurezza per l'accesso e la gestione dell'anteprima di Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come configurare la sicurezza e le autorizzazioni come criteri di accesso per la gestione e di accesso ai dati per proteggere Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 670615980b7fd78441a08ba987073dc139b3792a
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274447"
---
# <a name="grant-data-access-to-an-environment"></a>Concedere l'accesso ai dati in un ambiente

Questo articolo illustra i due tipi di criteri di accesso all'anteprima di Azure Time Series Insights.

## <a name="sign-in-to-time-series-insights"></a>Accedi per Time Series Insights

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Individuare l'ambiente Time Series Insights. Immettere `Time Series` nella casella **Cerca**. Selezionare **ambiente Time Series** nei risultati della ricerca.
1. Selezionare l'ambiente Time Series Insights nell'elenco.

## <a name="grant-data-access"></a>Concedere l'accesso ai dati

Per concedere l'accesso ai dati per un'entità utente, eseguire queste operazioni.

1. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Aggiungi**.

    [![Data-accesso-uno](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Scegliere **Seleziona utente**. Cercare il nome utente o l'indirizzo di posta elettronica per individuare l'utente che si vuole aggiungere. Selezionare **Seleziona** per confermare la selezione.

    [![Data-access-two](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Scegliere **Seleziona ruolo**. Scegliere il ruolo di accesso appropriato per l'utente:

    * Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente.

    * In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali non condivise nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

    [![Data-Access-tre](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Selezionare **OK** nella pagina **Selezionare il ruolo utente**.

    [![Data-Access-quattro](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Verificare che nella pagina **Criteri di accesso ai dati** siano elencati gli utenti e i ruoli per ogni utente.

    [![Data-Access-Five](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Fornire l'accesso Guest da un altro tenant di AAD

`Guest` non è un ruolo di gestione, ma un termine usato per un account che viene invitato da un tenant a un altro. Dopo che l'account guest viene invitato nella directory del tenant, può disporre dello stesso controllo di accesso applicato alla directory come qualsiasi altro account. È possibile concedere l'accesso per la gestione a un ambiente Time Series Insights tramite il pannello di gestione delle identità e degli accessi. In alternativa, è possibile concedere l'accesso ai dati nell'ambiente tramite il pannello dei criteri di accesso ai dati. Per altre informazioni sull'accesso guest al tenant di Azure Active Directory (Azure AD), leggere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Per concedere l'accesso guest in un ambiente Time Series Insights a un utente AAD di un altro tenant, eseguire queste operazioni.

1. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Invita**.

    [![Data-Access-sei](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Immettere l'indirizzo di posta elettronica per l'utente da invitare. Tale indirizzo deve essere associato ad Azure AD. Aggiungere un messaggio personale all'invito (facoltativo).

    [![Data-Access-Seven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Cercare il messaggio di conferma visualizzato sullo schermo.

    [![Data-Access-otto](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Scegliere **Seleziona utente**. Cercare l'indirizzo di posta elettronica dell'utente guest invitato per individuare l'utente da aggiungere. **Selezionare** quindi per confermare la selezione.

    [![Data-access-nine](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Scegliere **Seleziona ruolo**. Scegliere il ruolo di accesso appropriato per l'utente guest:

    * Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente.

    * In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali non condivise nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

    [![Data-Access-dieci](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Selezionare **OK** nella pagina **Selezionare il ruolo utente**.

1. Verificare che nella pagina **Criteri di accesso ai dati** vengano elencati l'utente guest e i ruoli per ogni utente guest.

    [![Data-Access-Eleven](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. A questo punto l'utente guest deve seguire questa procedura per accedere all'ambiente presente nel tenant di Azure in cui è stato invitato. In primo luogo l'utente accetta l'invito che ha ricevuto. Tale invito viene inviato tramite posta elettronica all'indirizzo usato nel passaggio 5. L'utente seleziona **Get Started** (Inizia) per accettare.

    [![Data-Access-dodici](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. Successivamente, l'utente guest accetta le autorizzazioni associate all'organizzazione dell'amministratore.

    [![Data-Access-tredici](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. Dopo che l'utente guest ha eseguito l'accesso all'indirizzo di posta elettronica usato per invitarlo e dopo che ha accettato l'invito, accede al sito insights.azure.com. A questo punto seleziona l'avatar accanto all'indirizzo di posta elettronica nell'angolo superiore destro dello schermo.

    [![Data-Access-quattordici](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. Successivamente, l'utente guest seleziona il tenant di Azure nel menu a discesa della directory. Questo tenant è quello a cui l'utente è stato invitato.

    [![Data-Access-quindici](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

Dopo che l'utente guest ha selezionato il tenant, viene visualizzato l'ambiente Time Series Insights per cui è stato concesso l'accesso. Ora hanno tutte le funzionalità associate al ruolo a cui sono state fornite nel **passaggio 5**.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [come aggiungere un'origine evento di Hub eventi di Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) all'ambiente Time Series Insights.

* Inviare [eventi all'origine evento](./time-series-insights-send-events.md).

* Visualizzare [l'ambiente nell'anteprima di Time Series Insights Explorer](./time-series-insights-update-explorer.md).
