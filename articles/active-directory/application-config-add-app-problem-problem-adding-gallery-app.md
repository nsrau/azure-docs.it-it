---
title: Errore durante l'aggiunta di un'applicazione della raccolta di Azure AD| Microsoft Docs
description: Informazioni sui problemi frequenti che si riscontrano quando si aggiungono applicazioni della raccolta di Azure AD e sulle azioni da eseguire per risolverli
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: b429f5fc1be24e63b0e9e065f879f0b0e389403c
ms.contentlocale: it-it
ms.lasthandoff: 04/11/2017

---

# <a name="problem-adding-an-azure-ad-gallery-application"></a>Errore durante l'aggiunta di un'applicazione della raccolta di Azure AD

Questo articolo illustra i problemi frequenti che si riscontrano durante l'aggiunta di applicazioni della raccolta di Azure AD e le operazioni da eseguire per risolverli.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Ho fatto clic sul pulsante "Aggiungi" ed è trascorso molto tempo prima che l'applicazione venisse visualizzata

In alcuni casi, potrebbero trascorrere anche due minuti (e talvolta di più) prima che l'applicazione venga visualizzata dopo averla aggiunta alla directory. Sebbene non si tratti delle normali prestazioni previste, è possibile visualizzare l'aggiunta dell'applicazione in corso facendo clic sull'icona **Notifiche** (la campanella) nella parte superiore destra del [Portale di Azure](https://portal.azure.com/) e cercando una notifica **In corso** o **Completata** con l'etichetta **Crea applicazione**.

Se l'applicazione non è aggiunta o si verifica un errore quando si fa clic sul pulsante **Aggiungi**, verrà visualizzata una **Notifica** in uno stato di **Errore**. Per ottenere più dettagli sull'errore e condividere i dettagli con un tecnico di supporto, seguire i passaggi nella sezione [Come visualizzare i dettagli di una notifica del portale](#how-to-see-the-details-of-a-portal-notification).

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Ho fatto clic sul pulsante "Aggiungi" e l'applicazione non è stata visualizzata

In alcuni casi, l'aggiunta di un applicazione ha esito negativo a causa di problemi temporanei, di problemi di rete o di un bug. È possibile verificare il problema facendo clic sull'icona **Notifiche** (la campanella) nella parte superiore destra del Portale di Azure. Viene visualizzata un'icona di colore rosso (!) accanto alla notifica **Crea applicazione**. Questa icona indica che si è verificato un errore durante la creazione dell'applicazione.

Se si verifica un errore quando si fa clic sul pulsante **Aggiungi**, verrà visualizzata una **Notifica** in uno stato di **Errore**. Per ottenere più dettagli sull'errore e condividere i dettagli con un tecnico di supporto, seguire i passaggi nella sezione [Come visualizzare i dettagli di una notifica del portale](#how-to-see-the-details-of-a-portal-notification).

 ## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Non so come configurare l'applicazione dopo averla aggiunta

Per informazioni su come configurare le applicazioni, vedere l'articolo [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

In aggiunta, l'articolo [Azure AD Applications Document Library](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) (Raccolta di documenti sulle applicazioni di Azure AD) include informazioni sull'accesso Single Sign-On e ne illustra il funzionamento.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Come visualizzare i dettagli di una notifica del portale

È possibile visualizzare i dettagli di qualsiasi notifica del portale seguendo la procedura seguente:

1.  Fare clic sull'icona **Notifiche** (la campanella) nella parte superiore destra del Portale di Azure

2.  Selezionare una notifica in uno stato di **Errore** (quelle con accanto un (!) di colore rosso).

    >[!NOTE]
    >Non è possibile fare clic sulle notifiche in uno stato di **Operazione completata** o **In corso**.
    >
    >

3.  Viene aperto il pannello **Dettagli notifica**.

4.  Usare queste informazioni per ottenere più dettagli sul problema.

5.  In caso sia ancora necessaria assistenza sul problema, è possibile condividere queste informazioni con un tecnico di supporto o con il gruppo del prodotto.

6.  Fare clic sull'**icona** **Copia** a destra della casella di testo **Copia errore** per copiare tutti i dettagli della notifica da condividere con un tecnico del supporto o con il gruppo del prodotto.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Come ottenere assistenza inviando i dettagli della notifica a un tecnico di supporto

È molto importante condividere **tutti i dettagli elencati di seguito** con un tecnico del supporto per poter ricevere assistenza immediata. A tale scopo, è possibile **effettuare una schermata** o fare clic sull'**icona Copia errore** che si trova a destra della casella di testo **Copia errore**.

## <a name="notification-details-explained"></a>Spiegazione dei dettagli della notifica

La sezione seguente illustra in dettaglio il significato degli elementi della notifica e offre esempi per ognuno di essi.

### <a name="essential-notification-items"></a>Elementi essenziali della notifica

-   **Titolo**: titolo descrittivo della notifica

  * Esempio: **impostazioni del proxy di applicazione**

-   **Descrizione**: la descrizione di ciò che si è verificato a seguito dell'operazione

    -   Esempio: **l'URL interno specificato è già usato da un'altra applicazione**

-   **ID notifica**: l'ID univoco della notifica

    -   Esempio: **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID richiesta client**: l'ID specifico della richiesta creato dal browser

    -   Esempio: **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Timestamp (UTC)**: il timestamp in cui si è verificata la notifica, basato sul sistema UTC

    -   Esempio: **2017-03-23T19:50:43.7583681Z**

-   **ID transazione interna**: l'ID interno che è possibile usare per cercare l'errore nei sistemi

    -   Esempio: **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** : l'utente che ha eseguito l'operazione

    -   Esempio: **tperkins@f128.info**

-   **ID tenant**: l'ID univoco del tenant di cui è membro l'utente che ha eseguito l'operazione

    -   Esempio: **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **ID oggetto utente**: l'ID univoco dell'utente che ha eseguito l'operazione

    -   Esempio: **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Elementi della notifica dettagliati

-   **Nome visualizzato**: **(può essere vuoto)** un nome visualizzato più dettagliato per l'errore

    -   Esempio: **impostazioni del proxy di applicazione**

-   **Stato**: lo stato specifico della notifica

    -   Esempio: **Operazione non riuscita**

-   **ID oggetto**: **(può essere vuoto)** l'ID dell'oggetto su cui è stata eseguita l'operazione

    -   Esempio: **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Dettagli**: la descrizione dettagliata di ciò che si è verificato come conseguenza dell'operazione

    -   Esempio: **L'URL interno "http://bing.com/" non è valido perché è già in uso**

-   **Copia errore**: fare clic sull'**icona Copia** a destra della casella di testo **Copia errore** per copiare tutti i dettagli di notifica da condividere con un tecnico di supporto o con il gruppo del prodotto

    -   Esempio```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md)

