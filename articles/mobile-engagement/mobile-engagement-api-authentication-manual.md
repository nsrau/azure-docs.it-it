---
title: 'Eseguire l''autenticazione con le API REST di Mobile Engagement: configurazione manuale'
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
ms.openlocfilehash: 0b4a999c6778040e71f862d3a010b6635e84b26e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis-manual-setup"></a>Eseguire l'autenticazione con le API REST di Mobile Engagement: configurazione manuale
Questa documentazione è un'appendice a [Eseguire l'autenticazione con le API REST di Mobile Engagement](mobile-engagement-api-authentication.md). Assicurarsi di leggere prima questo articolo per capire il contesto. Descrive inoltre un modo alternativo per eseguire la configurazione una tantum dell'autenticazione per le API REST di Mobile Engagement tramite il portale di Azure.

> [!NOTE]
> Le istruzioni seguenti si basano su [questa guida di Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md). Sono personalizzate per i requisiti di autenticazione per le API di Mobile Engagement. Farvi riferimento per comprendere bene la procedura seguente.

1. Accedere all'account di Azure tramite il [portale di Azure](https://portal.azure.com/).
2. Selezionare **Active Directory** dal riquadro di sinistra.

   ![Selezionare Active Directory][1]

3. Per visualizzare le applicazioni nella directory, selezionare **Registrazioni per l'app**.

   ![Visualizzare le applicazioni][3]

4. Selezionare **Registrazione nuova applicazione**.

   ![Aggiunta di un'applicazione][4]

5. Immettere il nome dell'applicazione. Lasciare il tipo di applicazione **App Web/API**, quindi selezionare **Avanti**. È possibile fornire un URL fittizio come **URL di accesso**. Questi valori non vengono usati per questo scenario e gli URL non vengono convalidati.

   Al termine, si avrà un'app Azure Active Directory (Azure AD) con il nome specificato. Verrà usato come **AD\_APP\_NAME**, quindi assicurarsi di prenderne nota.

   ![Nome app][8]

7. Selezionare il nome dell'app.

8. Trovare l'**ID applicazione** e prenderne nota. Si tratta dell'ID client che verrà usato come **CLIENT\_ID** per le chiamate API.

   ![Trovare l'ID applicazione][10]

9. Individuare la sezione **Chiavi** a destra.

   ![Sezione Chiavi][11]

10. Creare una nuova chiave e copiarla immediatamente. Non verrà visualizzata di nuovo.

    ![Riquadro Chiavi con dettagli sulle chiavi][12]

    > [!IMPORTANT]
    > Questa chiave scade alla fine della durata specificata. Ricordarsi di rinnovarla al momento opportuno, altrimenti l'autenticazione API non funzionerà più. Se si ritiene che sia stata compromessa, è possibile eliminare e ricreare la chiave.
    >
    
11. Selezionare il pulsante **Endpoint** nella parte superiore della pagina. Quindi, copiare l'**ENDPOINT DI TOKEN OAUTH 2.0**.

    ![Copiare l'endpoint][14]

16. L'endpoint è nel formato seguente, in cui il GUID nell'URL è il **TENANT_ID**: `https://login.microsoftonline.com/<GUID>/oauth2/token`

17. A questo punto si possono configurare le autorizzazioni dell'app. Per avviare il processo, accedere al [portale di Azure](https://portal.azure.com).

18. Selezionare **Gruppi di risorse** e individuare il gruppo di risorse **Mobile Engagement**.

    ![Trovare MobileEngagement][15]

19. Selezionare il gruppo di risorse **MobileEngagement** e quindi selezionare **Tutte le impostazioni**.

    ![Passare alle impostazioni per MobileEngagement][16]

20. Selezionare **Utenti** nella sezione **Impostazioni**. Quindi, per aggiungere un utente, selezionare **Aggiungi**.

    ![Aggiungere un utente][17]

21. Fare clic su **Selezionare un ruolo**.

    ![Selezionare un ruolo][18]

22. Selezionare **Proprietario**.

    ![Selezionare il ruolo Proprietario][19]

23. Cercare il nome dell'applicazione, **AD\_APP\_NAME**, nella casella di ricerca. Questo nome non è presente per impostazione predefinita. Dopo averlo trovato, selezionarlo. Quindi, fare clic su **Seleziona** nella parte inferiore della sezione.

    ![Selezionare il nome][20]

24. Nella sezione **Aggiungi accesso** viene visualizzato come **1 utente, 0 gruppi**. Selezionare **OK** per confermare la modifica.

    ![Conferma di aggiunta utente][21]

Ora la configurazione di Azure AD necessaria è terminata e tutto è pronto per le chiamate API.

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



