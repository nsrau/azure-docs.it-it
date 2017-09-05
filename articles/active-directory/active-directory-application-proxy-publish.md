---
title: Pubblicare app con il proxy di applicazione di Azure AD | Documentazione Microsoft
description: Pubblicare applicazioni locali nel cloud con il proxy di applicazione di Azure AD nel portale classico.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/14/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 96490c0d060fe5486a7235a5aa76380c8d9b5d4f
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---

# <a name="publish-applications-using-azure-ad-application-proxy"></a>Pubblicare applicazioni mediante il proxy di applicazione AD Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](application-proxy-publish-azure-portal.md)
> * [Portale di Azure classico](active-directory-application-proxy-publish.md)

Il proxy di applicazione di Azure AD consente di supportare lavoratori remoti pubblicando applicazioni locali in modo che siano accessibili tramite Internet. A questo punto dovrebbe essere già stato [abilitato il proxy di applicazione nel portale di Azure classico](active-directory-application-proxy-enable.md). Questo articolo illustra i passaggi per pubblicare le applicazioni in esecuzione nella rete locale e fornire un accesso remoto sicuro dall'esterno della rete. Al termine di questo articolo, sarà possibile configurare l'applicazione con informazioni o requisiti di sicurezza personalizzati.

> [!NOTE]
> Il proxy dell’applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md). Se si vuole usare il proxy dell'applicazione, è possibile [pubblicare applicazioni nel portale di Azure](application-proxy-publish-azure-portal.md).

## <a name="publish-an-app-using-the-wizard"></a>Pubblicare un'applicazione utilizzando la procedura guidata
1. Accedere come amministratore al [portale di Azure classico](https://manage.windowsazure.com/).
2. Passare ad Active Directory e selezionare la directory in cui è stato abilitato il proxy di applicazione.
   
    ![Active Directory - Icona](./media/active-directory-application-proxy-publish/ad_icon.png)
3. Fare clic sulla scheda **Applicazioni** e quindi sul pulsante **Aggiungi** nella parte inferiore della schermata.
   
    ![Aggiungi applicazione](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)
4. Selezionare **Pubblica un'applicazione che sarà accessibile dall'esterno della rete**.
   
    ![Pubblica un'applicazione che sarà accessibile dall'esterno della rete](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)
5. Specificare le informazioni relative all'applicazione elencate di seguito.
   
   * **Nome**: nome descrittivo dell'applicazione. Deve essere univoco all'interno della directory.
   * **URL interno**: indirizzo usato dal connettore proxy di applicazione per accedere all'applicazione dall'interno della rete privata. È possibile indicare un percorso specifico nel server back-end per la pubblicazione, mentre il resto del server non è pubblicato. In questo modo, si possono pubblicare siti diversi nello stesso server assegnando a ognuno un nome e regole di accesso specifici.
     
     > [!TIP]
     > Se si pubblica un percorso, verificare che includa tutte le immagini, gli script e i fogli di stile necessari per l'applicazione. Se l'app si trova in https://yourapp/app e usa immagini che si trovano in https://yourapp/media, si dovrà pubblicare come percorso https://yourapp/.
     > 
     > 
   * **Metodo di autenticazione preliminare**: come il proxy di applicazione verifica gli utenti prima di concedere loro l'accesso all'applicazione. Scegliere una delle opzioni nel menu a discesa.
     
     * Azure Active Directory: il proxy di applicazione reindirizza gli utenti in modo che eseguano l'accesso con Azure AD, che ne autentica le autorizzazioni per la directory e l'applicazione.
     * Passthrough: gli utenti non devono eseguire l'autenticazione per accedere all'applicazione.
     
     ![Proprietà dell'applicazione](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  
6. Per completare la procedura guidata, fare clic sul segno di spunta nella parte inferiore della schermata. L'applicazione ora è definita in Azure AD.

## <a name="assign-users-and-groups-to-the-application"></a>Assegnazione di utenti e gruppi all'applicazione
Per l'accesso degli utenti all'applicazione pubblicata, è necessario eseguirne l'assegnazione singolarmente o in gruppi (ricordando di assegnare l'accesso anche a se stessi). Ogni utente assegnato deve avere una licenza Azure Basic o superiore. È possibile assegnare le licenze singolarmente o a gruppi. Per altre informazioni, vedere [Assegnazione di utenti a un'applicazione](active-directory-applications-guiding-developers-assigning-users.md). 

Per le app che richiedono l'autenticazione preliminare, con l'assegnazione di un utente viene concessa l'autorizzazione per usare l'applicazione. Per le app che non richiedono l'autenticazione preliminare, l'assegnazione di un utente consente all'utente di accedere all'applicazione tramite il pannello di accesso.

1. Al termine della procedura guidata Aggiungi app, verrà visualizzata la pagina Avvio rapido dell'applicazione. Per gestire gli utenti che avranno accesso all'app, selezionare **Utenti e gruppi**.
   
    ![Assegnazione degli utenti nella pagina Avvio rapido per il proxy di applicazione - Screenshot](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)
2. Cercare gruppi specifici nella propria directory oppure visualizzare tutti gli utenti. Fare clic sul segno di spunta per visualizzare i risultati della ricerca.
   
      ![Ricerca di gruppi o utenti - Screenshot](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)
3. Selezionare ogni utente o gruppo che si desidera assegnare a questa app e fare clic su **Assegna**. Viene chiesto di confermare l'azione.

> [!NOTE]
> Per le app con autenticazione integrata di Windows, è possibile assegnare solo utenti e gruppi sincronizzati da Active Directory locale. Non è possibile assegnare utenti che accedono con account Microsoft e guest per le app pubblicate con il proxy di applicazione di Azure Active Directory. Verificare che gli utenti accedano con credenziali che fanno parte dello stesso dominio dell'app che viene pubblicata.
> 
> 

## <a name="test-your-published-application"></a>Testare l'applicazione pubblicata
Dopo che è stata pubblicata, è possibile testare l'applicazione passando all'URL pubblicato. Verificare che sia possibile accedervi, che il rendering venga eseguito correttamente e che tutto funzioni come previsto. In caso di problemi o se viene visualizzato un messaggio di errore, vedere la [guida alla risoluzione dei problemi](active-directory-application-proxy-troubleshoot.md).

## <a name="configure-your-application"></a>Configurare l'applicazione
Nella pagina Configura è possibile modificare le app pubblicate o configurare opzioni avanzate. In questa pagina si può personalizzare l'app modificando il nome o caricando un logo. È anche possibile gestire regole di accesso come il metodo di autenticazione preliminare o l'autenticazione a più fattori.

![Configurazione avanzata](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)

Dopo la pubblicazione di applicazioni mediante il proxy dell’applicazione di Azure Active Directory, queste vengono visualizzate nell'elenco delle applicazioni in Azure AD ed è possibile gestirle.

Se si disabilitano i servizi proxy dell'applicazione dopo la pubblicazione delle applicazioni, queste non sono più accessibili dall'esterno della rete privata. Gli utenti possono comunque accedere alle applicazioni in locale come di consueto.

Per visualizzare un'applicazione e verificare che sia accessibile, fare doppio clic sul nome dell'applicazione. Se il servizio proxy dell’applicazione è disabilitato e l'applicazione non è disponibile, viene visualizzato un messaggio di avviso nella parte superiore della schermata.

Per eliminare un'applicazione, selezionare un'applicazione nell'elenco e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi
* [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
* [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
* [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
* [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)

Per le notizie e gli aggiornamenti più recenti, vedere [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)


