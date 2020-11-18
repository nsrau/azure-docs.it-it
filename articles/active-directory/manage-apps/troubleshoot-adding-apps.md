---
title: Risolvere i problemi comuni relativi all'aggiunta o alla rimozione di un'applicazione Azure Active Directory
description: Risolvere i problemi comuni riscontrati durante l'aggiunta o la rimozione di un'app Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/11/2018
ms.author: kenwith
ms.openlocfilehash: 714cbb06506cc3e0dffe74007712d9cbad072284
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655069"
---
# <a name="troubleshoot-common-problem-adding-or-removing-an-application-to-azure-active-directory"></a>Risolvere i problemi comuni relativi all'aggiunta o alla rimozione di un'applicazione Azure Active Directory
Questo articolo consente di comprendere i problemi comuni che si verificano quando si aggiunge o rimuove un'app per Azure Active Directory.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Quando si fa clic sul pulsante "Aggiungi" trascorre molto tempo prima che l'applicazione venga visualizzata
In alcuni casi, potrebbero trascorrere uno o due minuti e a volte di più prima che l'applicazione venga visualizzata dopo essere stata aggiunta alla directory. Sebbene non si tratti delle normali prestazioni previste, è possibile visualizzare l'aggiunta dell'applicazione in corso facendo clic sull'icona **Notifiche** (la campanella) nella parte superiore destra del [portale di Azure](https://portal.azure.com/) e cercando una notifica con stato **In corso** o **Completato** con l'etichetta **Aggiunta dell'applicazione**.

Se l'applicazione non viene mai aggiunta o si verifica un errore quando si fa clic sul pulsante **Aggiungi**, viene visualizzata una **Notifica** con stato **Errore**. Se si desidera visualizzare altri dettagli sull'errore per saperne di più o per effettuarne la condivisione con un tecnico del supporto, è possibile visualizzare altre informazioni sull'errore seguendo i passaggi illustrati nella sezione [Come visualizzare i dettagli di una notifica del portale](#how-to-see-the-details-of-a-portal-notification).

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Quando si fa clic sul pulsante "Aggiungi" l'applicazione non viene visualizzata
In alcuni casi, a causa di problemi temporanei, problemi di rete o un bug, l'aggiunta di un'applicazione ha esito negativo. È possibile verificare il problema facendo clic sull'icona **Notifiche** (la campanella) in alto a destra nel portale di Azure. Accanto alla notifica **Aggiunta dell'applicazione** viene visualizzato un punto esclamativo (!) rosso. Questa icona indica che si è verificato un errore durante la creazione dell'applicazione.

Se si verifica un errore quando si fa clic sul pulsante **Aggiungi**, viene visualizzata una **Notifica** con stato **Errore**. Se si desidera visualizzare altri dettagli sull'errore per saperne di più o per effettuarne la condivisione con un tecnico del supporto, è possibile visualizzare altre informazioni sull'errore seguendo i passaggi illustrati nella sezione [Come visualizzare i dettagli di una notifica del portale](#how-to-see-the-details-of-a-portal-notification).

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Non si sa come configurare l'applicazione dopo averla aggiunta
Per assistenza sull'apprendimento delle applicazioni, l' [elenco delle esercitazioni su come integrare le app Saas con Azure Active Directory](../saas-apps/tutorial-list.md) articolo è un punto di partenza valido.

Vedere anche la [raccolta di documenti sulle applicazioni di Azure AD](./what-is-application-management.md) che include informazioni sull'accesso Single Sign-On con Azure AD e ne illustra il funzionamento.

## <a name="i-want-to-delete-an-application-but-the-delete-button-is-disabled"></a>Si vuole eliminare un'applicazione, ma il pulsante Elimina è disabilitato

Il pulsante Elimina sarà disabilitato negli scenari seguenti:

- Per le applicazioni in applicazioni aziendali, se non si ha uno dei ruoli seguenti: amministratore globale, amministratore dell'applicazione cloud, amministratore dell'applicazione o proprietario dell'entità servizio.

- Per l'applicazione Microsoft, non sarà possibile eliminarli dall'interfaccia utente indipendentemente dal ruolo.

- Per entità servizio che corrispondono a un'identità gestita. Le entità servizio delle identità gestite non possono essere eliminate nel pannello app aziendali. È necessario passare alla risorsa di Azure per gestirla. Altre informazioni sull' [identità gestita](../managed-identities-azure-resources/overview.md)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Come visualizzare i dettagli di una notifica del portale
È possibile visualizzare i dettagli di qualsiasi notifica del portale seguendo questa procedura:
1.  Selezionare l'icona **notifiche** (la Campanella) nella parte superiore destra della portale di Azure
2.  Selezionare una notifica con stato **Errore** contrassegnata con un punto esclamativo (!) rosso.
    >[!NOTE]
    >Non è possibile fare clic sulle notifiche con stato **Operazione completata** o **In corso**.
4.  Usare le informazioni in **Dettagli notifica** per ottenere maggiori dettagli sul problema.
5.  In caso sia ancora necessaria assistenza sul problema, è possibile condividere queste informazioni con un tecnico di supporto o con il gruppo del prodotto.
6.  Selezionare l' **icona copia** a destra della casella di testo **copia errore** per copiare tutti i dettagli della notifica da condividere con un tecnico del supporto o del gruppo di prodotti.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Come ottenere assistenza inviando i dettagli di notifica a un tecnico del supporto
È importante condividere **tutti i dettagli elencati di seguito** con un tecnico del supporto se è necessaria assistenza, in modo che possano aiutarti rapidamente. **Scattare uno screenshot** o selezionare l' **icona copia errore**, a destra della casella di testo **copia errore** .

## <a name="notification-details-explained"></a>Spiegazione dei dettagli della notifica
Vedere le descrizioni seguenti per altri dettagli sulle notifiche.

### <a name="essential-notification-items"></a>Elementi essenziali della notifica
- **Titolo**: il titolo descrittivo della notifica
  * Esempio: **impostazioni del proxy di applicazione**
- **Descrizione**: la descrizione di ciò che si è verificato a seguito dell'operazione
  -   Esempio: **L'URL interno immesso è già usato da un'altra applicazione**
- **ID notifica**: ID univoco della notifica
  -   Esempio: **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**
- **ID richiesta client**: ID specifico della richiesta creato dal browser
  -   Esempio: **302fd775-3329-4670-a9f3-bea37004f0bc**
- **Timestamp (UTC)**: il timestamp in cui si è verificata la notifica, basato sul sistema UTC
  -   Esempio: **2017-03-23T19:50:43.7583681Z**
- **ID transazione interna** : ID interno che è possibile usare per cercare l'errore nei sistemi
  -   Esempio: **71a2f329-ca29-402f-aa72-bc00a7aca603**
- **UPN**: l'utente che ha eseguito l'operazione
  -   Esempio: **tperkins \@ F128.info**
- **ID tenant**: ID univoco del tenant di cui è membro l'utente che ha eseguito l'operazione
  -   Esempio: **7918d4b5-0442-4a97-be2d-36f9f9962ece**
- **ID oggetto utente**: ID univoco dell'utente che ha eseguito l'operazione
  -   Esempio: **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Elementi della notifica dettagliati
-   **Nome visualizzato**: **(può essere vuoto)** un nome visualizzato più dettagliato per l'errore
    -   Esempio: **impostazioni del proxy di applicazione**
-   **Stato**: lo stato specifico della notifica
    -   Esempio: **non riuscito**
-   **ID oggetto**: **(può essere vuoto)** ID dell'oggetto su cui è stata eseguita l'operazione
    -   Esempio: **8e08161d-f2fd-40ad-a34a-a9632d6bb599**
-   **Dettagli**: la descrizione dettagliata di ciò che si è verificato come conseguenza dell'operazione
    -   Esempio: l' **URL interno non `https://bing.com/` è valido perché è già in uso**
-   **Copia errore** : selezionare l' **icona copia** a destra della casella di testo **copia errore** per copiare tutti i dettagli della notifica da condividere con un gruppo di supporto o di prodotto 
-   engineer
    -   Esempio```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```