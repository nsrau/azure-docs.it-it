---
title: Pubblicare app con il proxy di applicazione di Azure AD | Documentazione Microsoft
description: Pubblicare applicazioni locali nel cloud con il proxy dell&quot;applicazione di Azure AD nel portale di Azure.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1fcec43ad50b9c78443ada116b9ca444605c4730
ms.openlocfilehash: dc30e18b92e492137e016e7a7372bfdf3285e5fe

---


# <a name="publish-applications-using-azure-ad-application-proxy---public-preview"></a>Pubblicare applicazioni mediante il proxy dell'applicazione AD Azure: anteprima pubblica

> [!div class="op_single_selector"]
> * [Portale di Azure](application-proxy-publish-azure-portal.md)
> * [Portale di Azure classico](active-directory-application-proxy-publish.md)

Il proxy dell'applicazione di Azure Active Directory (AD) consente di supportare lavoratori remoti pubblicando applicazioni locali in modo che siano accessibili tramite Internet. Tramite il portale di Azure è possibile pubblicare le applicazioni in esecuzione nella rete locale e fornire un accesso remoto sicuro dall'esterno della rete.

Questo articolo illustra tutti i passaggi per pubblicare un'app locale con il proxy dell'applicazione. Al termine di questo articolo, sarà possibile configurare l'applicazione con accesso Single Sign-On, informazioni o requisiti di sicurezza personalizzati.

Se non si ha familiarità con le funzionalità offerte dal proxy dell'applicazione, per altre informazioni vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md).


## <a name="publish-an-on-premises-app-for-remote-access"></a>Pubblicare un'applicazione locale per l'accesso remoto


> [!TIP]
> Se è la prima volta che si usa il proxy dell'applicazione, scegliere un'applicazione già configurata per l'autenticazione basata su password. Il proxy dell'applicazione supporta altri tipi di autenticazione, ma le app basate su password sono le più facili da configurare ed eseguire con rapidità. 

1. Accedere come amministratore al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Aggiungi**.

  ![Aggiungere un'applicazione aziendale](./media/application-proxy-publish-azure-portal/add-app.png)

3. Nella pagina delle categorie, selezionare **Or add your own** (Oppure aggiungine una).  

  ![Aggiungere una propria applicazione](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Selezionare **Distribuzione di un'applicazione esistente** dal menu a discesa.
5. Specificare un nome per l'app, quindi selezionare **Aggiungi**. Viene visualizzata una finestra di caricamento e, una volta aggiunta l'app, viene visualizzato il pannello di avvio rapido.
6. Nel pannello di avvio rapido selezionare **Enable remote access for your on-premises application** (Consenti accesso remoto per l'applicazione locale).


  ![Consenti accesso remoto per l'applicazione locale](./media/application-proxy-publish-azure-portal/enable-remote-access.png)

7. Specificare le informazioni relative all'applicazione elencate di seguito.

   - **URL interno**: indirizzo usato dal connettore proxy di applicazione per accedere all'applicazione dall'interno della rete privata. È possibile indicare un percorso specifico nel server back-end per la pubblicazione, mentre il resto del server non è pubblicato. In questo modo, si possono pubblicare siti diversi nello stesso server come app differenti, assegnando a ognuno un nome e regole di accesso specifici.

     > [!TIP]
     > Se si pubblica un percorso, verificare che includa tutte le immagini, gli script e i fogli di stile necessari per l'applicazione. Se l'app si trova in https://yourapp/app e usa immagini che si trovano in https://yourapp/media, si dovrà pubblicare come percorso https://yourapp/.

   - **URL esterno**: l'indirizzo immesso dagli utenti per accedere all'app dall'esterno della rete.
   - **Metodo di autenticazione preliminare**: come il proxy dell'applicazione verifica gli utenti prima di concedere loro l'accesso all'applicazione. 

     - Azure Active Directory: il proxy di applicazione reindirizza gli utenti in modo che eseguano l'accesso con Azure AD, che ne autentica le autorizzazioni per la directory e l'applicazione. È consigliabile mantenere questa opzione come impostazione predefinita.
     - Passthrough: gli utenti non devono eseguire l'autenticazione ad Azure Active Directory per accedere all'applicazione. È comunque possibile configurare i requisiti di autenticazione sul back-end.
   - **Tradurre URL nelle intestazioni?**: scegliere se tradurre gli URL nelle intestazioni o mantenere gli originali. 
   - **Gruppo di connettori**: i connettori elaborano l'accesso remoto all'applicazione, mentre i gruppi di connettori aiutano a organizzare connettori e app in base all'area geografica, alla rete o allo scopo. Se non si dispone ancora di gruppi di connettori, l'app viene assegnata al gruppo **Predefinito** e verrà visualizzato un messaggio di avviso che chiede di [creare un gruppo di connettori](active-directory-application-proxy-connectors-azure-portal.md).

   ![Configurare l'applicazione](./media/application-proxy-publish-azure-portal/configure-app.png)

8. Selezionare **Salva**.

   ![Salvare le impostazioni del proxy dell'applicazione](./media/application-proxy-publish-azure-portal/save-app-proxy.png)

## <a name="add-a-test-user"></a>Aggiungere un utente di test 

Per verificare l'avvenuta pubblicazione dell'app, aggiungere un account utente a cui è possibile accedere. 

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



<!--HONumber=Jan17_HO2-->


