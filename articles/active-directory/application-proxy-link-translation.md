---
title: Convertire collegamenti e URL - Proxy di app di Azure AD| Microsoft Docs
description: Tratta i fondamenti dei connettori del proxy applicazione di Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: d716476deb81f6627cf03401f78ab399ae940e68
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017

---

# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Reindirizzare i collegamenti hardcoded per le app pubblicate con il proxy di app di Azure AD

Il proxy di applicazione di Azure AD rende disponibili le app locali per gli utenti remoti o che usano i propri dispositivi. Alcune applicazioni, tuttavia, sono state sviluppate tenendo conto di destinatari locali, e questo in genere significa che collegamenti locali sono incorporati nel codice HTML e quindi non funzionano correttamente quando l'app viene usata in remoto. Questo è più comune quando molte applicazioni locali puntano l'una all'altra e gli utenti si aspettano di usare i collegamenti per spostarsi fra le app anziché digitare l'URL esterno per ogni app.

Per risolvere questo problema è stata sviluppata la funzionalità di conversione dei collegamenti del proxy di applicazione. Quando si hanno applicazioni che puntano direttamente a endpoint o porte interne, è possibile mappare questi URL interni agli URL del proxy di applicazione esterno pubblicato. Abilitare la conversione dei collegamenti in modo che il proxy di applicazione cerchi nell'HTML, nel CSS e nei tag JavaScript i collegamenti interni pubblicati, quindi li converta in modo che gli utenti abbiano un'esperienza senza interruzioni.

>[!NOTE]
>La funzionalità di conversione dei collegamenti è destinata ai tenant che, per qualsiasi motivo, non possono usare i domini personalizzati per avere gli stessi URL interni ed esterni per le app. Prima di abilitare questa funzionalità, verificare se i [domini personalizzati nel proxy di applicazione di Azure AD](active-directory-application-proxy-custom-domains.md) possono fare al caso.

## <a name="how-link-translation-works"></a>Come funziona la conversione dei collegamenti

Dopo l'autenticazione, quando il server proxy passa i dati dell'applicazione all'utente, il proxy di applicazione esegue l'analisi dell'applicazione alla ricerca dei collegamenti hardcoded e li sostituisce con i rispettivi URL esterni pubblicati.

### <a name="which-links-are-affected"></a>Quali collegamenti sono interessati?

La funzionalità di conversione dei collegamenti cerca solo i collegamenti che si trovano nel tag di codice nel corpo di un'app. Il proxy di applicazione ha una funzione separata per la conversione dei cookie o URL in intestazioni. 

Esistono due tipi comuni di collegamenti interni nelle applicazioni locali:

- I **collegamenti interni relativi** che puntano a una risorsa condivisa in una struttura di file locale come `/claims/claims.html`. Questi collegamenti sono attivati automaticamente nelle app che vengono pubblicate tramite il proxy di applicazione e continueranno a funzionare con o senza la conversione dei collegamenti. 
- I **collegamenti interni hardcoded** ad altre applicazioni locali come `http://expenses` o i file pubblicati come `http://expenses/logo.jpg`. La funzionalità di conversione dei collegamenti opera sui collegamenti interni hardcoded e li converte, in modo che puntino agli URL esterni che gli utenti remoti devono usare.

### <a name="how-do-apps-link-to-each-other"></a>In che modo le app si collegano tra loro?

La conversione dei collegamenti è abilitata per ogni applicazione, perciò si ha il controllo dell'esperienza utente a livello di app. Attivare la conversione dei collegamenti per un'app quando si desidera che siano convertiti i collegamenti *da* tale app e non i collegamenti *a* tale app. 

Ad esempio, si supponga di avere tre applicazioni pubblicate mediante il proxy di applicazione e che tutte siano collegate tra loro: Benefits, Expenses e Travel. C'è una quarta app, Feedback, che non viene pubblicata tramite il proxy di applicazione.

Quando si abilita la conversione dei collegamenti per l'app Benefits, i collegamenti a Expenses e Travel sono reindirizzati agli URL esterni per tali app, ma il collegamento a Feedback non viene reindirizzato perché non esiste alcun URL esterno. I collegamenti da Expenses e Travel a Benefits non funzionano perché la conversione dei collegamenti non è stata abilitata per queste due app.

![Collegamenti da Benefits ad altre app quando è abilitata la conversione dei collegamenti](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Quali collegamenti non vengono convertiti?

Per migliorare prestazioni e sicurezza, alcuni collegamenti non vengono convertiti:

- Collegamenti non interni ai tag di codice. 
- Collegamenti interni aperti da altri programmi. I collegamenti inviati tramite posta elettronica o messaggistica istantanea o inclusi in altri documenti non vengono convertiti. Gli utenti devono sapere di passare all'URL esterno.

Se è necessario supportare uno di questi due scenari, è possibile usare gli stessi URL interni ed esterni, il che elimina la necessità della conversione dei collegamenti.  

## <a name="enable-link-translation"></a>Abilitare la conversione dei collegamenti

Per iniziare con la conversione dei collegamenti, è sufficiente fare clic su un pulsante:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni** > selezionare l'app che si desidera gestire > **Proxy di applicazione**.
3. Impostare l'opzione di **conversione URL nel corpo dell'applicazione** su **Sì**.

   ![Selezionare Sì per convertire gli URL nel corpo dell'applicazione](./media/application-proxy-link-translation/select_yes.png).
4. Selezionare **Salva** per applicare le modifiche.

Ora, quando gli utenti accedono a questa applicazione, il proxy esegue automaticamente un'analisi per individuare gli URL interni che sono stati pubblicati tramite il proxy di applicazione nel tenant.

## <a name="send-feedback"></a>Invia commenti

Abbiamo bisogno dell'intervento dell'utente per rendere questa funzionalità operante per tutte le app. Cerchiamo oltre 30 tag in HTML e CSS e stiamo prendendo in considerazione quali casi JavaScript supportare. Se si hanno esempi di collegamenti generati che non vengono convertiti, inviare un frammento di codice all'area [commenti sul proxy di applicazione](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Passaggi successivi
- [Usare i domini personalizzati con il proxy di applicazione Azure AD per avere gli stessi URL interni ed esterni](active-directory-application-proxy-custom-domains.md)

