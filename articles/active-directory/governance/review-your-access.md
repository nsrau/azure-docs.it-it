---
title: Controllare l'accesso ai gruppi & app nelle verifiche di accesso-Azure AD
description: Informazioni su come verificare il proprio accesso a gruppi o applicazioni in Azure Active Directory verifiche di accesso.
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
ms.openlocfilehash: 0b52830e7cddb01c129df2344e26ac1684a88872
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89292725"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Verificare l'accesso per se stessi a gruppi o applicazioni in Azure AD verifiche di accesso

Azure Active Directory (Azure AD) semplifica il modo in cui le aziende gestiscono l'accesso a gruppi o applicazioni in Azure AD e altri Microsoft Online Services con una funzionalità denominata verifiche di accesso Azure AD.

Questo articolo descrive come verificare il proprio accesso a un gruppo o a un'applicazione.

## <a name="review-your-access-using-my-apps"></a>Verifica l'accesso usando le mie app

Il primo passaggio per eseguire una verifica di accesso consiste nell'individuare e aprire la verifica di accesso.

>[!IMPORTANT]
> Potrebbero verificarsi ritardi nella ricezione della posta elettronica e in alcuni casi potrebbero essere necessarie fino a 24 ore. Aggiungere azure-noreply@microsoft.com all'elenco dei destinatari sicuri per assicurarsi di ricevere tutti i messaggi di posta elettronica.

1. Cercare un messaggio di posta elettronica da Microsoft che richiede di verificare l'accesso. Di seguito è riportato un esempio di messaggio di posta elettronica per esaminare l'accesso a un gruppo.

    ![Messaggio di posta elettronica di esempio da Microsoft per verificare l'accesso a un gruppo](./media/review-your-access/access-review-email.png)

1. Fare clic sul collegamento **Verifica accesso** per aprire la verifica di accesso.

Se non si dispone del messaggio di posta elettronica, è possibile trovare le verifiche di accesso in sospeso seguendo questa procedura.

1. Accedere al portale app personali all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portale app personali che elenca le app a cui si hanno le autorizzazioni](./media/review-your-access/myapps-access-panel.png)

1. Fare clic sul simbolo utente nell'angolo superiore destro della pagina, che indica il proprio nome e l'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare l'organizzazione che ha richiesto una verifica di accesso.

1. Sul lato destro della pagina fare clic sul riquadro verifiche di **accesso** per visualizzare un elenco delle verifiche di accesso in sospeso.

    Se il riquadro non è visibile, non esistono verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

    ![Elenco delle verifiche di accesso in sospeso per le app e i gruppi](./media/review-your-access/access-reviews-list.png)

1. Fare clic sul collegamento **Begin Review** per la verifica di accesso che si desidera eseguire.

### <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

Dopo aver aperto la verifica di accesso, è possibile visualizzare l'accesso.

1. Controllare l'accesso e decidere se è ancora necessario l'accesso.

    Se la richiesta prevede di verificare l'accesso per altri utenti, la pagina sarà diversa. Per altre informazioni, vedere [verificare l'accesso a gruppi o applicazioni](perform-access-review.md).

    ![Aprire la verifica di accesso chiedendo se è ancora necessario l'accesso a un gruppo](./media/review-your-access/perform-access-review.png)

1. Fare clic su **Sì** per Mantenete l'accesso oppure su **No** per rimuovere l'accesso.

1. Se si fa clic su **Sì**, potrebbe essere necessario specificare una giustificazione nella casella **motivo** .

    ![Verifica di accesso completato che chiede se è ancora necessario l'accesso a un gruppo](./media/review-your-access/perform-access-review-submit.png)

1. Fare clic su **Submit** (Invia).

    La selezione viene inviata e si torna al portale app personali.

    Se si vuole modificare la risposta, riaprire la pagina delle verifiche di accesso e aggiornare la risposta. È possibile modificare la risposta in qualsiasi momento fino al termine della verifica di accesso.

    > [!NOTE]
    > Se è stato indicato che non è più necessario l'accesso, l'utente non viene rimosso immediatamente. L'utente viene rimosso al termine della verifica o quando un amministratore interrompe la revisione.

## <a name="review-your-own-access-using-my-access-new"></a>Verifica il tuo accesso con accesso personale (nuovo)

È possibile provare la nuova esperienza con l'interfaccia utente aggiornata in My Access in due modi diversi:

### <a name="my-apps-portal"></a>Portale app personali

1. Accedere al portale app personali all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portale app personali che elenca le app a cui si hanno le autorizzazioni](./media/review-your-access/myapps-access-panel.png)

2. Fare clic sul riquadro verifiche di **accesso** per visualizzare un elenco di verifiche di accesso in sospeso.

    > [!NOTE]
    > Se il riquadro verifiche di **accesso** non è visibile, non sono disponibili verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

3. Fare clic su **prova!** nel banner nella parte superiore della pagina per passare alla nuova esperienza di accesso.

    ![Elenco delle verifiche di accesso in sospeso per le app e i gruppi con il banner nuova esperienza disponibile visualizzato durante l'anteprima](./media/review-your-access/banner-your-access.png)

4. Continuare nella sezione **eseguire la verifica di accesso**

### <a name="email"></a>E-mail

>[!IMPORTANT]
> Potrebbero verificarsi ritardi nella ricezione della posta elettronica e in alcuni casi potrebbero essere necessarie fino a 24 ore. Aggiungere azure-noreply@microsoft.com all'elenco dei destinatari sicuri per assicurarsi di ricevere tutti i messaggi di posta elettronica.

1. Cercare un messaggio di posta elettronica da Microsoft in cui viene chiesto di verificare l'accesso. È possibile visualizzare un esempio di messaggio di posta elettronica di seguito:

 ![Messaggio di posta elettronica di esempio da Microsoft per verificare l'accesso a un gruppo](./media/review-your-access/access-review-email-preview.png)

2. Fare clic sul collegamento **Verifica accesso** per aprire la verifica di accesso.

3. Continuare nella sezione **eseguire la verifica di accesso**

>[!NOTE]
>Se facendo clic su Start Review si passa a **app personali** , seguire i passaggi elencati nella sezione precedente intitolata **My Apps Portal**.

### <a name="directly-at-my-access"></a>Direttamente all'accesso

È anche possibile visualizzare le verifiche di accesso in sospeso usando il browser per aprire l'accesso.

1. Accedere a My Access at https://myaccess.microsoft.com/

2. Selezionare verifiche di **accesso** dal menu sulla barra laterale sinistra per visualizzare un elenco delle verifiche di accesso in sospeso assegnate all'utente.

   ![verifiche di accesso nel menu](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

1. In gruppi e app è possibile vedere:
    
    - **Nome** Nome della verifica di accesso.
    - **Data di scadenza** Data di scadenza per la revisione. Dopo questa data, gli utenti potrebbero essere rimossi dal gruppo o dall'app da rivedere.
    - **Risorsa** di Nome della risorsa in fase di revisione.
    - **Stato di avanzamento** Il numero di utenti controllati per il numero totale di utenti che fanno parte di questa verifica di accesso.
    
2. Per iniziare, fare clic sul nome di una verifica di accesso.

   ![Elenco delle verifiche di accesso in sospeso per le app e i gruppi](./media/review-your-access/access-reviews-list-preview.png)

3. Controllare l'accesso e decidere se è ancora necessario l'accesso.

    Se la richiesta prevede di verificare l'accesso per altri utenti, la pagina sarà diversa. Per altre informazioni, vedere [verificare l'accesso a gruppi o applicazioni](perform-access-review.md).

    ![Aprire la verifica di accesso chiedendo se è ancora necessario l'accesso a un gruppo](./media/review-your-access/review-access-preview.png)

1. Selezionare **Sì** per gestire l'accesso o selezionare **No** per rimuovere l'accesso.

1. Se si fa clic su **Sì**, potrebbe essere necessario specificare una giustificazione nella casella **motivo** .

    ![Verifica di accesso completato che chiede se è ancora necessario l'accesso a un gruppo](./media/review-your-access/review-access-yes-preview.png)

1. Fare clic su **Submit** (Invia).

    La selezione viene inviata e la pagina di accesso è stata nuovamente visualizzata.

    Se si vuole modificare la risposta, riaprire la pagina delle verifiche di accesso e aggiornare la risposta. È possibile modificare la risposta in qualsiasi momento fino al termine della verifica di accesso.

    > [!NOTE]
    > Se è stato indicato che non è più necessario l'accesso, l'utente non viene rimosso immediatamente. L'utente viene rimosso al termine della verifica o quando un amministratore interrompe la revisione.

## <a name="next-steps"></a>Passaggi successivi

- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)
