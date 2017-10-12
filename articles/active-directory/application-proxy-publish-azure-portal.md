---
title: Pubblicare app con il proxy di applicazione di Azure AD | Documentazione Microsoft
description: Pubblicare applicazioni locali nel cloud con il proxy dell'applicazione di Azure AD nel portale di Azure.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: e00a939f2b20ab8e0a2ddf0ff91e59db440213ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Pubblicare applicazioni mediante il proxy di applicazione AD Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](application-proxy-publish-azure-portal.md)
> * [Portale di Azure classico](active-directory-application-proxy-publish.md)

Il proxy dell'applicazione di Azure Active Directory (AD) consente di supportare lavoratori remoti pubblicando applicazioni locali in modo che siano accessibili tramite Internet. Tramite il portale di Azure è possibile pubblicare queste applicazioni per fornire un accesso remoto sicuro dall'esterno della rete.

Questo articolo illustra tutti i passaggi per pubblicare un'app locale con il proxy dell'applicazione. Dopo aver completato questo articolo, gli utenti potranno accedere all'app in modalità remota. E sarà possibile configurare funzionalità extra per l'applicazione con accesso Single Sign-On, informazioni personalizzate e requisiti di sicurezza.

Se non si ha familiarità con le funzionalità offerte dal proxy dell'applicazione, per altre informazioni vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md).


## <a name="publish-an-on-premises-app-for-remote-access"></a>Pubblicare un'applicazione locale per l'accesso remoto

Seguire questa procedura per pubblicare le app con il proxy dell'applicazione. Se non è ancora stato scaricato e configurato un connettore per l'organizzazione, passare ad [Attività iniziali del proxy di applicazione e installazione del connettore](active-directory-application-proxy-enable.md) in primo luogo e quindi pubblicare l'app.

> [!TIP]
> Se è la prima volta che si prova il proxy dell'applicazione, scegliere un'applicazione già configurata per l'autenticazione basata su password. Il proxy dell'applicazione supporta altri tipi di autenticazione, ma le app basate su password sono le più facili da configurare ed eseguire con rapidità. 

1. Accedere come amministratore al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Nuova applicazione**.

  ![Aggiungere un'applicazione aziendale](./media/application-proxy-publish-azure-portal/add-app.png)

3. Selezionare **Tutte** quindi scegliere **Applicazione locale**.  

  ![Aggiungere una propria applicazione](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Specificare le informazioni relative all'applicazione elencate di seguito.

   - **Nome**: il nome dell'applicazione che verrà visualizzato nel pannello di accesso e nel portale di Azure. 

   - **URL interno**: URL usato per accedere all'applicazione dall'interno della rete privata. È possibile indicare un percorso specifico nel server back-end per la pubblicazione, mentre il resto del server non è pubblicato. In questo modo, si possono pubblicare siti diversi nello stesso server come app differenti, assegnando a ognuno un nome e regole di accesso specifici.

     > [!TIP]
     > Se si pubblica un percorso, verificare che includa tutte le immagini, gli script e i fogli di stile necessari per l'applicazione. Se l'app si trova in https://yourapp/app e usa immagini che si trovano in https://yourapp/media, si dovrà pubblicare come percorso https://yourapp/. Questo URL interno non deve necessariamente corrispondere alla pagina di destinazione visualizzata dagli utenti. Per altre informazioni, vedere [Impostare una home page personalizzata per le app pubblicate tramite il proxy applicazione di Azure AD](application-proxy-office365-app-launcher.md).

   - **URL esterno**: l'indirizzo immesso dagli utenti per accedere all'app dall'esterno della rete. Se non si desidera usare il dominio del Proxy di applicazione predefinito, trovare informazioni sui [domini personalizzati nel Proxy dell'applicazione di Azure AD](active-directory-application-proxy-custom-domains.md).
   - **Metodo di autenticazione preliminare**: come il proxy dell'applicazione verifica gli utenti prima di concedere loro l'accesso all'applicazione. 

     - Azure Active Directory: il proxy di applicazione reindirizza gli utenti in modo che eseguano l'accesso con Azure AD, che ne autentica le autorizzazioni per la directory e l'applicazione. È consigliabile lasciare questa opzione come impostazione predefinita, in modo da poter usufruire delle funzionalità di sicurezza di Azure AD come l'accesso condizionale e l'autenticazione a più fattori.
     - Passthrough: gli utenti non devono eseguire l'autenticazione ad Azure Active Directory per accedere all'applicazione. È comunque possibile configurare i requisiti di autenticazione sul back-end.
   - **Gruppo di connettori**: i connettori elaborano l'accesso remoto all'applicazione, mentre i gruppi di connettori aiutano a organizzare connettori e app in base all'area geografica, alla rete o allo scopo. Se ancora non si dispone di tutti i gruppi connettore creati, l'app viene assegnata a **Impostazione predefinita**.

   ![Configurare l'applicazione](./media/application-proxy-publish-azure-portal/configure-app.png)
5. Se necessario, configurare le impostazioni aggiuntive. Per la maggior parte delle applicazioni, è consigliabile mantenere queste impostazioni nei rispettivi stati predefiniti. 
   - **Timeout applicazione back-end**: impostare questo valore su **Lungo** solo se l'applicazione è lenta nell'autenticazione e nella connessione. 
   - **Convertire l'URL nelle intestazioni**: mantenere questo valore su **Sì**, a meno che l'applicazione non richieda l'intestazione host originale nella richiesta di autenticazione.
   - **Convertire gli URL nel corpo dell'applicazione**: mantenere questo valore su **No**, a meno che non si sia in possesso di collegamenti HTML hardcoded ad altre applicazioni locali e non si usino domini personalizzati. Per altre informazioni, vedere [Reindirizzare i collegamenti hardcoded per le app pubblicate con il proxy di app di Azure AD](application-proxy-link-translation.md).
   
   ![Configurare l'applicazione](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Selezionare **Aggiungi**.


## <a name="add-a-test-user"></a>Aggiungere un utente di test 

Per verificare che l'app sia stata pubblicata correttamente, aggiungere un account utente di prova. Verificare che questo account disponga già delle autorizzazioni per accedere all'app dall'interno della rete aziendale.

1. Nel pannello di avvio rapido selezionare **Assign a user for testing** (Assegna utente per il test).

  ![Assegna utente per il test](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Nel pannello Utenti e gruppi selezionare **Aggiungi**.

  ![Aggiungere un utente o gruppo](./media/application-proxy-publish-azure-portal/add-user.png)

3. Nel pannello Aggiungi assegnazione selezionare **Utenti e gruppi**, quindi scegliere l'account da aggiungere. 
4. Selezionare **Assegna**.

## <a name="test-your-published-app"></a>Testare l'app pubblicata

Nel browser passare all'URL esterno configurato durante la fase di pubblicazione. Dovrebbe essere visualizzata la schermata iniziale in cui accedere con l'account test configurato.

![Testare l'app pubblicata](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Passaggi successivi
- [Scaricare connettori](active-directory-application-proxy-enable.md) e [creare gruppi di connettori](active-directory-application-proxy-connectors-azure-portal.md) per pubblicare applicazioni in reti e posizioni separate.

- [Configurare l'accesso Single Sign-On](application-proxy-sso-azure-portal.md) per l'app appena pubblicata
