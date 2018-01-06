---
title: Eseguire l'autenticazione con le API REST di Mobile Engagement - installazione manuale
description: Descrive come configurare manualmente l'autenticazione per le API REST di Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3b678acbae225c28223a2ee76e5be2462a529362
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Eseguire l'autenticazione con le API REST di Mobile Engagement - installazione manuale
Questa documentazione è una documentazione appendice a [eseguire l'autenticazione con le API REST di Engagement Mobile](mobile-engagement-api-authentication.md). Leggerlo prima per capire il contesto.
Descrive un modo alternativo per eseguire il programma di installazione singola per configurare l'autenticazione per le API REST di Engagement Mobile tramite il portale di Azure.

> [!NOTE]
> Le istruzioni seguenti si basano su questo [Guida di Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) e personalizzato per ciò che è richiesto per l'autenticazione per le API di Mobile Engagement. Consultare la guida per comprendere bene la seguente procedura.

1. Accedere all'account di Azure tramite il [portale di Azure](https://portal.azure.com/).
2. Selezionare **Active Directory** dal riquadro di sinistra.

     ![selezionare Active Directory][1]

3. Per visualizzare le applicazioni nella directory, fare clic su **registrazioni di App**.

     ![visualizzare le applicazioni][3]

4. Fare clic su **Registrazione nuova applicazione**.

     ![Aggiungi applicazione][4]

5. Nome dell'applicazione e lasciare il tipo di applicazione come **Web app/API** e fare clic sul pulsante Avanti. È possibile fornire qualsiasi URL fittizio per **URL accesso**: non vengono usati per questo scenario e gli URL se stessi non vengono convalidati.
6. Al termine, si dispone di un'app di Azure AD con il nome specificato. È il **AD\_APP\_nome**, prendere nota di esso.

     ![Nome dell'applicazione][8]

7. Fare clic sul nome dell'app.
8. Trovare **ID applicazione**, prendere nota di esso, è l'ID CLIENT che deve essere utilizzato come **CLIENT\_ID** per l'API chiamate.

     ![configurare l'applicazione][10]

9. Trovare il **chiavi** sezione a destra.

     ![configurare l'applicazione][11]

10. Creare una nuova chiave immediatamente copiarlo e salvarlo per usarlo. Non verrà mai visualizzato nuovamente.

     ![configurare l'applicazione][12]

    > [!IMPORTANT]
    > Questa chiave scade alla fine della durata specificata in modo da assicurarsi di rinnovare il certificato al momento opportuno in caso contrario l'autenticazione di API non funzionerà più. È possibile anche eliminare e ricreare la chiave se si ritiene che sia stata compromessa.
    >
    >
11. Fare clic su **endpoint** pulsante nella parte superiore della pagina e copia il **ENDPOINT TOKEN OAUTH 2.0**.

    ![][14]

16. Questo endpoint sarà nel formato seguente, dove il GUID nell'URL è il **TENANT_ID** quindi prendere nota di esso:`https://login.microsoftonline.com/<GUID>/oauth2/token`
17. A questo punto si potranno configurare le autorizzazioni dell'applicazione. A tal fine, aprire il [portale di Azure](https://portal.azure.com). 
18. Fare clic su **Gruppi di risorse** e individuare il gruppo di risorse **Mobile Engagement**.

    ![][15]

19. Fare clic su di **Mobile Engagement** risorse gruppo e passare al **impostazioni** sezione qui.

    ![][16]

20. Fare clic su **utenti** nelle impostazioni della sezione e quindi fare clic su **Aggiungi** per aggiungere un utente.

    ![][17]

21. Fare clic su **selezionare un ruolo**.

    ![][18]

22. Fare clic su **proprietario**.

    ![][19]

23. Cercare il nome dell'applicazione **AD\_APP\_NAME** nella casella di ricerca. Qui non verrà visualizzato per impostazione predefinita. Se si trova il, selezionarla e fare clic su **selezionare** nella parte inferiore della sezione.

    ![][20]

24. Nel **aggiungere accesso** sezione, verrà visualizzato come **1 utente, 0 gruppi**. Fare clic su **OK** in questa sezione per confermare la modifica.

    ![][21]

È stata completata la configurazione di Azure AD e sono impostati per chiamare le API.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



