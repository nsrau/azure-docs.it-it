---
title: Configurare la sicurezza per l'accesso e la gestione dell'anteprima di Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come configurare la sicurezza e le autorizzazioni come criteri di accesso per la gestione e di accesso ai dati per proteggere Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.custom: seodec18
ms.openlocfilehash: dd4c5e1652eb4dbff66591aa4bbe74e51be3e6c0
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759555"
---
# <a name="grant-data-access-to-an-environment"></a>Concedere l'accesso ai dati in un ambiente

Questo articolo illustra i due tipi di criteri di accesso all'anteprima di Azure Time Series Insights.

## <a name="sign-in-to-tsi"></a>Accedere a Time Series Insights

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Individuare l'ambiente Time Series Insights. Immettere `Time Series` nella casella **Cerca**. Selezionare **ambiente Time Series** nei risultati della ricerca.
1. Selezionare l'ambiente Time Series Insights nell'elenco.

## <a name="grant-data-access"></a>Concedere l'accesso ai dati

Per concedere l'accesso ai dati per un'entità utente, eseguire queste operazioni.

1. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Aggiungi**.

    ![Accesso ai dati - uno][1]

1. Scegliere **Seleziona utente**. Cercare il nome utente o l'indirizzo di posta elettronica per individuare l'utente che si vuole aggiungere. Fare clic su **Seleziona** per confermare la selezione.

    ![Accesso ai dati - due][2]

1. Scegliere **Seleziona ruolo**. Scegliere il ruolo di accesso appropriato per l'utente:

    * Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente.

    * In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali non condivise nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

    ![Accesso ai dati - tre][3]

1. Selezionare **OK** nella pagina **Selezionare il ruolo utente**.

    ![Accesso ai dati - quattro][4]

1. Verificare che nella pagina **Criteri di accesso ai dati** siano elencati gli utenti e i ruoli per ogni utente.

    ![Accesso ai dati - cinque][5]

## <a name="provide-guest-access-from-another-aad-tenant"></a>Fornire l'accesso guest da un altro tenant AAD

`Guest` non è un ruolo di gestione, ma un termine usato per un account che viene invitato da un tenant a un altro. Dopo che l'account guest viene invitato nella directory del tenant, può disporre dello stesso controllo di accesso applicato alla directory come qualsiasi altro account. È possibile concedere l'accesso per la gestione a un ambiente Time Series Insights tramite il pannello di gestione delle identità e degli accessi. In alternativa, è possibile concedere l'accesso ai dati nell'ambiente tramite il pannello dei criteri di accesso ai dati. Per altre informazioni sull'accesso guest al tenant di Azure Active Directory (Azure AD), leggere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Per concedere l'accesso guest in un ambiente Time Series Insights a un utente AAD di un altro tenant, eseguire queste operazioni.

1. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Invita**.

    ![Accesso ai dati - sei][6]

1. Immettere l'indirizzo di posta elettronica per l'utente da invitare. Tale indirizzo deve essere associato ad Azure AD. Aggiungere un messaggio personale all'invito (facoltativo).

    ![Accesso ai dati - sette][7]

1. Cercare il messaggio di conferma visualizzato sullo schermo.

    ![Accesso ai dati - otto][8]

1. Scegliere **Seleziona utente**. Cercare l'indirizzo di posta elettronica dell'utente guest invitato per individuare l'utente da aggiungere. Fare clic su **Seleziona** per confermare la selezione.

    ![Accesso ai dati - nove][9]

1. Scegliere **Seleziona ruolo**. Scegliere il ruolo di accesso appropriato per l'utente guest:

    * Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente.

    * In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali non condivise nell'ambiente.

   Selezionare **OK** per confermare la scelta del ruolo.

    ![Accesso ai dati - dieci][10]

1. Selezionare **OK** nella pagina **Selezionare il ruolo utente**.

1. Verificare che nella pagina **Criteri di accesso ai dati** vengano elencati l'utente guest e i ruoli per ogni utente guest.

    ![Accesso ai dati - undici][11]

1. A questo punto l'utente guest deve seguire questa procedura per accedere all'ambiente presente nel tenant di Azure in cui è stato invitato. In primo luogo l'utente accetta l'invito che ha ricevuto. Tale invito viene inviato tramite posta elettronica all'indirizzo usato nel passaggio 5. L'utente seleziona **Get Started** (Inizia) per accettare.

    ![Accesso ai dati - dodici][12]

1. Successivamente, l'utente guest accetta le autorizzazioni associate all'organizzazione dell'amministratore.

    ![Accesso ai dati - tredici][13]

1. Dopo che l'utente guest ha eseguito l'accesso all'indirizzo di posta elettronica usato per invitarlo e dopo che ha accettato l'invito, accede al sito insights.azure.com. A questo punto seleziona l'avatar accanto all'indirizzo di posta elettronica nell'angolo superiore destro dello schermo.

    ![Accesso ai dati - quattordici][14]

1. Successivamente, l'utente guest seleziona il tenant di Azure nel menu a discesa della directory. Questo tenant è quello a cui l'utente è stato invitato.

    ![Accesso ai dati - quindici][15]

Dopo che l'utente guest ha selezionato il tenant, viene visualizzato l'ambiente Time Series Insights per cui è stato concesso l'accesso. Ora dispongono di tutte le funzionalità associate al ruolo che ha è fornito con nella **passaggio 5**.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [come aggiungere un'origine evento di Hub eventi di Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) all'ambiente Time Series Insights.

* Inviare [eventi all'origine evento](./time-series-insights-send-events.md).

* Visualizzare [l'ambiente nell'anteprima di Time Series Insights Explorer](./time-series-insights-update-explorer.md).

<!-- Images -->
[1]: media/data-access/data-access-one.png
[2]: media/data-access/data-access-two.png
[3]: media/data-access/data-access-three.png
[4]: media/data-access/data-access-four.png
[5]: media/data-access/data-access-five.png
[6]: media/data-access/data-access-six.png
[7]: media/data-access/data-access-seven.png
[8]: media/data-access/data-access-eight.png
[9]: media/data-access/data-access-nine.png
[10]: media/data-access/data-access-ten.png
[11]: media/data-access/data-access-eleven.png
[12]: media/data-access/data-access-twelve.png
[13]: media/data-access/data-access-thirteen.png
[14]: media/data-access/data-access-fourteen.png
[15]: media/data-access/data-access-fifteen.png
