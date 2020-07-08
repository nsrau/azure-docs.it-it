---
title: Verificare l'accesso ai gruppi & le applicazioni nelle verifiche di accesso-Azure AD
description: Informazioni su come verificare l'accesso dei membri del gruppo o dell'accesso alle applicazioni in Azure Active Directory verifiche di accesso.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b672cca97e70c97a5dccb1ca54daccd6c171932
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85077977"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Esaminare l'accesso ai gruppi e alle applicazioni nelle verifiche di accesso Azure AD

Azure Active Directory (Azure AD) semplifica il modo in cui le aziende gestiscono l'accesso ai gruppi e alle applicazioni in Azure AD e in altri Microsoft Online Services con una funzionalità denominata verifiche di accesso Azure AD. Questo articolo descrive come un revisore designato esegue una verifica di accesso per i membri di un gruppo o gli utenti con accesso a un'applicazione. Per esaminare, accedere a un pacchetto leggere [verificare l'accesso di un pacchetto di accesso in Azure ad gestione dei diritti](entitlement-management-access-reviews-review-access.md)

## <a name="perform-access-review-using-my-apps"></a>Eseguire la verifica dell'accesso usando le mie app

È possibile avviare il processo di verifica dell'accesso dal messaggio di posta elettronica di notifica o passare direttamente al sito.

- **Posta elettronica**:

>[!IMPORTANT]
> Potrebbero verificarsi ritardi nella ricezione della posta elettronica e in alcuni casi potrebbero essere necessarie fino a 24 ore. Whitelist azure-noreply@microsoft.com per assicurarsi di ricevere tutti i messaggi di posta elettronica.

1. Cercare un messaggio di posta elettronica da Microsoft in cui viene chiesto di verificare l'accesso. Di seguito è riportato un esempio di messaggio di posta elettronica per esaminare l'accesso per un gruppo.

    ![Messaggio di posta elettronica di esempio da Microsoft per verificare l'accesso a un gruppo](./media/perform-access-review/access-review-email.png)

1. Fare clic sul collegamento **Avvia verifica** per aprire la verifica di accesso.

- **Se non si dispone del messaggio di posta elettronica**, è possibile trovare le verifiche di accesso in sospeso seguendo questa procedura.

    1. Accedere al portale app personali all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com) .

        ![Portale app personali che elenca le app a cui si hanno le autorizzazioni](./media/perform-access-review/myapps-access-panel.png)

    1. Nell'angolo in alto a destra della pagina fare clic sull'utente accanto al nome e all'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare l'organizzazione che ha richiesto una verifica di accesso.

    1. Fare clic sul riquadro verifiche di **accesso** per visualizzare un elenco di verifiche di accesso in sospeso.

        > [!NOTE]
        > Se il riquadro verifiche di **accesso** non è visibile, non sono disponibili verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

        ![Elenco delle verifiche di accesso in sospeso per le app e i gruppi](./media/perform-access-review/access-reviews-list.png)

    1. Fare clic sul collegamento **Begin Review** per la verifica di accesso che si desidera eseguire.

Dopo aver aperto la verifica di accesso, vengono visualizzati i nomi degli utenti che devono essere rivisti.

Se la richiesta prevede di verificare il proprio accesso, l'aspetto della pagina sarà diverso. Per altre informazioni, vedere [verificare l'accesso per se stessi a gruppi o applicazioni](review-your-access.md).

![Aprire la verifica di accesso elencando gli utenti da rivedere](./media/perform-access-review/perform-access-review.png)

È possibile approvare o negare l'accesso in due modi:

- È possibile approvare o negare l'accesso per uno o più utenti manualmente scegliendo l'azione appropriata per ogni richiesta dell'utente.
- È possibile accettare le raccomandazioni relative al sistema.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Approva o nega l'accesso per uno o più utenti

1. Esaminare l'elenco degli utenti e decidere se approvare o negare l'accesso continuo.

    - Per approvare o negare l'accesso per un singolo utente, fare clic sulla riga per aprire una finestra per specificare l'azione da eseguire. 
    - Per approvare o negare l'accesso per più utenti, aggiungere i segni di spunta accanto agli utenti e quindi fare clic sul pulsante **Verifica utente/i X** per aprire una finestra per specificare l'azione da eseguire.

1. Fare clic su **approva** o **Nega**. 

    ![Finestra azione che include le opzioni approva, nega e non so](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > Se non si è certi, è possibile fare clic su **non so**. e l'utente è tenuto a conservarne l'accesso e la scelta viene registrata nei log di controllo.

1. L'amministratore della verifica di accesso può richiedere di fornire un motivo nella casella **motivo** per la decisione. Anche quando non è necessario un motivo. È comunque possibile specificare un motivo per la decisione e le informazioni incluse saranno disponibili per gli altri revisori.

1. Dopo aver specificato l'azione da eseguire, fare clic su **Salva**.

    >[!NOTE]
    > È possibile modificare la risposta in qualsiasi momento prima della fine della verifica di accesso. Se si desidera modificare la risposta, selezionare la riga e aggiornare la risposta. Ad esempio, è possibile approvare un utente negato in precedenza o negare un utente precedentemente approvato.

    >[!IMPORTANT]
    > - Se a un utente viene negato l'accesso, questi non vengono rimossi immediatamente. Vengono rimossi al termine del periodo di revisione o quando un amministratore interrompe la verifica se è abilitata l' [opzione auto Apply](complete-access-review.md#apply-the-changes) .
    > - Se sono presenti più revisori, viene registrata l'ultima risposta inviata. Si consideri un esempio in cui un amministratore designa due revisori: Alice e Bob. Alice apre prima di tutto la verifica di accesso e approva la richiesta di accesso di un utente. Prima della fine del periodo di revisione, Bob apre la verifica di accesso e nega l'accesso alla stessa richiesta precedentemente approvata da Alice. L'ultima decisione che nega l'accesso è la risposta che viene registrata.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Approva o nega l'accesso in base alle raccomandazioni

Per semplificare e velocizzare le verifiche di accesso, sono disponibili anche consigli che è possibile accettare con un solo clic. Le indicazioni vengono generate in base all'attività di accesso dell'utente.

1. Nella barra blu nella parte inferiore della pagina fare clic su **accetta raccomandazioni**.

    ![Aprire l'elenco di controllo di accesso che mostra il pulsante accetta raccomandazioni](./media/perform-access-review/accept-recommendations.png)

    Viene visualizzato un riepilogo delle azioni consigliate.

    ![Finestra che visualizza un riepilogo delle azioni consigliate](./media/perform-access-review/accept-recommendations-summary.png)

1. Fare clic su **OK** per accettare le raccomandazioni.

## <a name="perform-access-review-using-my-access-new"></a>Eseguire la verifica dell'accesso tramite My Access (nuovo)

È possibile accedere alla nuova esperienza di revisore con l'interfaccia utente aggiornata in My Access in due modi diversi:

### <a name="my-apps-portal"></a>Portale app personali

1. Accedere alle app personali all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portale app personali che elenca le app a cui si hanno le autorizzazioni](./media/perform-access-review/myapps-access-panel.png)

2. Fare clic sul riquadro verifiche di **accesso** per visualizzare un elenco di verifiche di accesso in sospeso.

    > [!NOTE]
    > Se il riquadro verifiche di **accesso** non è visibile, non sono disponibili verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

![Elenco delle verifiche di accesso in sospeso per le app e i gruppi con il banner nuova esperienza disponibile visualizzato durante l'anteprima](./media/perform-access-review/banner.png)

3. Fare clic su **prova!** nel banner nella parte superiore della pagina. Verrà visualizzata la nuova esperienza di accesso.
  
### <a name="email"></a>Posta elettronica

  >[!IMPORTANT]
> Potrebbero verificarsi ritardi nella ricezione della posta elettronica e in alcuni casi potrebbero essere necessarie fino a 24 ore. Whitelist azure-noreply@microsoft.com per assicurarsi di ricevere tutti i messaggi di posta elettronica.

   1. Cercare un messaggio di posta elettronica da Microsoft in cui viene chiesto di verificare l'accesso. È possibile visualizzare un esempio di messaggio di posta elettronica di seguito:

   ![Messaggio di posta elettronica di esempio da Microsoft per verificare l'accesso a un gruppo](./media/perform-access-review/access-review-email-preview.png)

   2. Fare clic sul collegamento **Avvia verifica** per aprire la verifica di accesso.

>[!NOTE]
>Se facendo clic su Start Review si passa a **app personali** , seguire i passaggi elencati nella sezione precedente intitolata **My Apps Portal**.

### <a name="navigate-to-my-access-directly"></a>Passare direttamente a accesso personale

È anche possibile visualizzare le verifiche di accesso in sospeso usando il browser per aprire l'accesso.

1. Accedere a My Access athttps://myaccess.microsoft.com/

2. Selezionare verifiche di **accesso** dal menu sulla barra laterale sinistra per visualizzare un elenco delle verifiche di accesso in sospeso assegnate all'utente.

   ![verifiche di accesso nel menu](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Approva o nega l'accesso per uno o più utenti

Dopo aver aperto l'accesso in gruppi e app, è possibile vedere:

- **Nome** Nome della verifica di accesso.
- **Data di scadenza** Data di scadenza per la revisione. Dopo questa data, gli utenti potrebbero essere rimossi dal gruppo o dall'app da rivedere.
- **Risorsa** di Nome della risorsa in fase di revisione.
- **Stato di avanzamento** Il numero di utenti controllati per il numero totale di utenti che fanno parte di questa verifica di accesso.

Per iniziare, fare clic sul nome di una verifica di accesso.

![Elenco delle verifiche di accesso in sospeso per le app e i gruppi](./media/perform-access-review/access-reviews-list-preview.png)

Una volta aperta, verrà visualizzato l'elenco degli utenti nell'ambito della verifica di accesso. Se la richiesta prevede di verificare il proprio accesso, l'aspetto della pagina sarà diverso. Per altre informazioni, vedere [verificare l'accesso per se stessi a gruppi o applicazioni](review-your-access.md).

È possibile approvare o negare l'accesso in due modi:

- È possibile approvare o negare manualmente l'accesso per uno o più utenti.
- È possibile accettare le raccomandazioni relative al sistema.

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Approva o nega manualmente l'accesso per uno o più utenti

1. Esaminare l'elenco degli utenti e decidere se approvare o negare l'accesso continuo.
2. Selezionare uno o più utenti facendo clic sul cerchio accanto ai rispettivi nomi.
3. Selezionare **approva** o **Nega** sulla barra sopra.
    - Se non si è certi, è possibile fare clic su **non so**. L'utente ottiene l'accesso e la scelta viene registrata nei log di controllo. È importante tenere presente che tutte le informazioni fornite saranno disponibili per gli altri revisori. Possono leggere i commenti e tenerli in considerazione quando esaminano la richiesta.

    ![Aprire la verifica di accesso con l'elenco degli utenti che necessitano di Revisione](./media/perform-access-review/user-list-preview.png)

4. L'amministratore della verifica di accesso può richiedere di fornire un motivo nella casella **motivo** per la decisione. Anche quando non è necessario un motivo. È comunque possibile specificare un motivo per la decisione e le informazioni che si includono saranno disponibili per gli altri responsabili approvazione per la revisione.

5. Fare clic su **Submit** (Invia).
    - È possibile modificare la risposta in qualsiasi momento fino al termine della verifica di accesso. Se si desidera modificare la risposta, selezionare la riga e aggiornare la risposta. Ad esempio, è possibile approvare un utente negato in precedenza o negare un utente precedentemente approvato.

 >[!IMPORTANT]
 > - Se a un utente viene negato l'accesso, questi non vengono rimossi immediatamente. Vengono rimossi al termine del periodo di revisione o quando un amministratore interrompe la revisione. 
 > - Se sono presenti più revisori, viene registrata l'ultima risposta inviata. Si consideri un esempio in cui un amministratore designa due revisori: Alice e Bob. Alice apre prima di tutto la verifica di accesso e approva la richiesta di accesso di un utente. Prima della fine del periodo di revisione, Bob apre la verifica di accesso e nega l'accesso alla stessa richiesta precedentemente approvata da Alice. L'ultima decisione che nega l'accesso è la risposta che viene registrata.

#### <a name="approve-or-deny-access-based-on-recommendations"></a>Approva o nega l'accesso in base alle raccomandazioni

Per semplificare e velocizzare le verifiche di accesso, sono disponibili anche consigli che è possibile accettare con un solo clic. Le indicazioni vengono generate in base all'attività di accesso dell'utente.

1. Selezionare uno o più utenti e quindi fare clic su **accetta raccomandazioni**.

    ![Aprire l'elenco di controllo di accesso che mostra il pulsante accetta raccomandazioni](./media/perform-access-review/accept-recommendations-preview.png)

1. Fare clic su **Submit (Invia** ) per accettare le raccomandazioni.

Per accettare raccomandazioni per tutti gli utenti, assicurarsi che non sia selezionata e fare clic sul pulsante **accetta raccomandazioni** nella barra superiore.

>[!NOTE]
>Quando si accettano raccomandazioni, le decisioni precedenti non verranno modificate.

## <a name="next-steps"></a>Passaggi successivi

- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)

