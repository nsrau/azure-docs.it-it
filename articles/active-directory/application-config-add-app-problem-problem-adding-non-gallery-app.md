---
title: Errore durante l&quot;aggiunta di un&quot;applicazione non inclusa nella raccolta | Microsoft Docs
description: Comprendere i problemi frequenti che si riscontrano durante l&quot;aggiunta di applicazioni personalizzate non incluse nella raccolta
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
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 2dd8ed2cc0cd9575dcc56f489afa19a41cc9c10a
ms.lasthandoff: 04/11/2017


---

# <a name="problem-adding-a-non-gallery-application"></a>Errore durante l'aggiunta di un'applicazione non inclusa nella raccolta

Questo articolo consente di comprendere i problemi frequenti che si riscontrano durante l'aggiunta di **applicazioni personalizzate non incluse nella raccolta** e presenta le azioni da intraprendere per risolverli. 

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Dopo avere fatto clic sul pulsante "Aggiungi", l'applicazione ha impiegato molto tempo per essere visualizzata

In alcuni casi, dopo essere stata aggiunta alla directory, un'applicazione può impiegare 1-2 minuti per essere visualizzata. Talvolta, possono essere richiesti tempi ancora più lunghi. Sebbene non si tratti delle normali prestazioni previste, è possibile vedere che è in corso l'aggiunta dell'applicazione facendo clic sull'icona **Notifiche** (la campanella) nella parte superiore destra del [Portale di Azure](https://portal.azure.com/) e cercando una notifica che indica **In corso** o **Completata** con l'etichetta **Crea applicazione**.

Se l'applicazione non viene mai aggiunta o si verifica un errore quando si fa clic sul pulsante **Aggiungi**, verrà visualizzata una **Notifica** in uno stato di **Errore**. Se si desidera visualizzare altri dettagli sull'errore per saperne di più o per effettuarne la condivisione con un tecnico del supporto, è possibile visualizzare altre informazioni sull'errore seguendo i passaggi illustrati nella sezione [Come visualizzare i dettagli di una notifica del portale](#how-to-see-the-details-of-a-portal-notification).

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Dopo avere fatto clic sul pulsante "Aggiungi", l'applicazione non è stata visualizzata

In alcuni casi, a causa di problemi temporanei, problemi di rete o un bug, l'aggiunta di un'applicazione ha esito negativo. È possibile stabilire che si è verificata tale situazione quando si fa clic sull'icona **Notifiche** (la campanella) nella parte superiore destra del portale di Azure e viene visualizzata un'icona di colore rosso (!) accanto alla notifica **Crea applicazione**. Ciò indica che si è verificato un errore durante la creazione dell'applicazione.

Se si verifica un errore quando si fa clic sul pulsante **Aggiungi**, verrà visualizzata una **Notifica** in uno stato di **Errore**. Se si desidera visualizzare altri dettagli sull'errore per saperne di più o per effettuarne la condivisione con un tecnico del supporto, è possibile visualizzare altre informazioni sull'errore seguendo i passaggi illustrati nella sezione [Come visualizzare i dettagli di una notifica del portale](#how-to-see-the-details-of-a-portal-notification).

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Come configurare l'applicazione dopo averla aggiunta

Se sono necessarie altre informazioni sulle applicazioni personalizzate, la [Raccolta documenti sulle applicazioni Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) consente di acquisire familiarità con Single sign-on con Azure AD e il relativo funzionamento.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Come visualizzare i dettagli di una notifica del portale

È possibile visualizzare i dettagli di qualsiasi notifica del portale seguendo questa procedura:

1.  Fare clic sull'icona **Notifiche** (la campanella) nella parte superiore destra del portale di Azure

2.  Selezionare qualsiasi notifica in uno stato di **Errore** (quelle con accanto un (!) di colore rosso).

   >[!NOTE]
   >Non è possibile fare clic sulle notifiche in uno stato di **Riuscita** o **In corso**.
   >
   >

3.  Questa operazione apre il pannello **Dettagli notifica**.

4.  Usare queste informazioni per comprendere altri dettagli sul problema.

5.  In caso sia necessaria ulteriore assistenza, è anche possibile condividere queste informazioni con un tecnico del supporto o il gruppo di prodotto per ottenere assistenza con il problema.

6.  Fare clic sull'**icona Copia** a destra della casella di testo **Copia errore** per copiare tutti i dettagli di notifica da condividere con un tecnico del supporto o del gruppo di prodotto.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Come ottenere assistenza inviando i dettagli di notifica a un tecnico del supporto

È molto importante condividere **tutti i dettagli riportati di seguito** con un tecnico del supporto per poter ricevere assistenza rapidamente. È possibile farlo semplicemente **acquisendo una schermata** o facendo clic sull'**icona Copia errore** che si trova a destra della casella di testo **Copia errore**.

## <a name="notification-details-explained"></a>Spiegazione dei dettagli relativi alla notifica

Quanto segue illustra in dettaglio il significato degli elementi della notifica e offre alcuni esempi per ognuno di essi.

### <a name="essential-notification-items"></a>Elementi essenziali della notifica

-   **Titolo**: titolo descrittivo della notifica
   *  Esempio: **Impostazioni del proxy dell'applicazione**

-   **Descrizione**: la descrizione di ciò che si è verificato a seguito dell'operazione

   *  Esempio: **L'URL interno immesso è già in uso da un'altra applicazione**

-   **ID notifica**: l'ID univoco della notifica

   *  Esempio: **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID richiesta client**: l'ID specifico della richiesta creato dal browser

   *  Esempio: **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Timestamp (UTC)**: il timestamp in cui si è verificata la notifica, in UTC

   *  Esempio: **2017-03-23T19:50:43.7583681Z**

-   **ID transazione interna**: l'ID interno che è possibile usare per cercare l'errore nei sistemi

   *  Esempio: **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN**: l'utente che ha eseguito l'operazione

   *  Esempio: **tperkins@f128.info**

-   **ID tenant**: l'ID univoco del tenant di cui era membro l'utente che ha eseguito l'operazione

   *  Esempio: **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **ID oggetto utente**: l'ID univoco dell'utente che ha eseguito l'operazione

 *  Esempio: **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Elementi della notifica in dettaglio

-   **Nome visualizzato**: **(può essere vuoto)** un nome visualizzato più dettagliato per l'errore

  *  Esempio: **Impostazioni del proxy dell'applicazione**

-   **Stato**: lo stato specifico della notifica

   *  Esempio: **Non riuscita**

-   **ID oggetto**: **(può essere vuoto)** l'ID dell'oggetto per cui è stata eseguita l'operazione

   *  Esempio: **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Dettagli**: la descrizione dettagliata di ciò che si è verificato a seguito dell'operazione

   *  Esempio: **L'URL interno 'http://bing.com/' non è valido perché è già in uso**

-   **Copia errore**: fare clic sull'**icona Copia** a destra della casella di testo **Copia errore** per copiare tutti i dettagli di notifica da condividere con un tecnico del supporto o del gruppo di prodotto

   *  Esempio```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md)




