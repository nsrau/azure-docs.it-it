---
title: Gestione dell&quot;accesso Single Sign-On per app aziendali nell&quot;anteprima di Azure Active Directory | Documentazione Microsoft
description: Informazioni su come gestire l&quot;accesso Single Sign-On per app aziendali usando Azure Active Directory
services: active-directory
documentationcenter: 
author: asmalser
manager: femila
editor: 
ms.assetid: bcc954d3-ddbe-4ec2-96cc-3df996cbc899
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 2f03079498568f52802b34ce57242a414e648fe3


---
# <a name="preview-managing-single-sign-on-for-enterprise-apps-in-the-new-azure-portal"></a>Anteprima: gestione dell'accesso Single Sign-On per app aziendali nel nuovo portale di Azure
> [!div class="op_single_selector"]
> * [Portale di Azure](active-directory-enterprise-apps-manage-sso.md)
> * [Portale di Azure classico](active-directory-sso-integrate-saas-apps.md)
> 
> 

Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com) per gestire le impostazioni dell'accesso Single Sign-On per le applicazioni, in particolare per le applicazioni aggiunte dalla [raccolta di applicazioni di Azure Active Directory (Azure AD)](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). L'esperienza di gestione di Azure AD per l'accesso Single Sign-On è attualmente disponibile in anteprima pubblica e questo articolo illustra le nuove funzionalità, oltre ad alcune limitazioni temporanee che saranno applicate solo nel periodo di anteprima. [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md)

## <a name="finding-your-apps-in-the-new-portal"></a>Individuazione delle app nel nuovo portale
A partire dal mese di settembre 2016, tutte le applicazioni configurate per l'accesso Single Sign-On in una directory da un amministratore di directory che usa la [raccolta di applicazioni di Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) nel [portale di Azure classico](https://manage.windowsazure.com) possono essere visualizzate e gestite nel portale di Azure.

Queste applicazioni sono disponibili nella sezione **Applicazioni aziendali** del portale di Azure. Un collegamento a questa sezione è disponibile nell'elenco **More Services** (Altri servizi) nel [portale](https://portal.azure.com). Le app aziendali sono app distribuite e usate da utenti all'interno dell'organizzazione.

![Pannello Applicazioni aziendali][1]

Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta. Se si seleziona un'app, viene caricato il pannello delle risorse per tale app, in cui è possibile visualizzare i report per l'app e gestire diverse impostazioni.

Per gestire le impostazioni dell'accesso Single Sign-On, selezionare **Single Sign-On**.

![Pannello Risorsa applicazione][2]

## <a name="single-sign-on-modes"></a>Modalità dell'accesso Single Sign-On
Il pannello **Single Sign-On** inizia con un menu **Modalità** che consente di configurare la modalità Single Sign-On. Le opzioni disponibili includono:

* **SAML-based sign on** (Accesso basato su SAML): questa opzione è disponibile se l'applicazione supporta l'accesso Single Sign-On completamente federato con Azure Active Directory mediante il protocollo SAML 2.0.
* **Password-based sign on** (Accesso basato su password): questa opzione è disponibile se Azure AD supporta la compilazione di moduli con password per questa applicazione.
* **Linked sign on** (Accesso collegato): nota in precedenza come "Accesso Single Sign-On esistente", questa opzione consente agli amministratori di inserire un collegamento all'applicazione nel riquadro di accesso di Azure AD degli utenti o nella schermata di avvio delle applicazioni di Office 365.

Per altre informazioni su queste modalità, vedere [Come funziona Single Sign-On con Azure Active Directory (PHP)?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>SAML-based sign on
L'opzione **SAML-based sign on** (Accesso basato su SAML) consente di visualizzare un pannello suddiviso in quattro sezioni:

### <a name="domains-and-urls"></a>Domains and URLs (Domini e URL)
In questa sezione vengono aggiunti alla directory di Azure AD tutti i dettagli sul dominio e sugli URL dell'applicazione. Tutti gli input necessari per il funzionamento dell'accesso Single Sign-On nell'app vengono visualizzati direttamente sullo schermo, mentre tutti gli input facoltativi possono essere visualizzati selezionando la casella di controllo **Mostra impostazioni URL avanzate** . L'elenco completo di input supportati include gli elementi seguenti:

* **URL di accesso** da cui l'utente accede all'applicazione. Se l'applicazione è configurata per eseguire l'accesso Single Sign-On avviato dal provider di servizi, quando un utente passa a questo URL, il provider di servizi esegue il reindirizzamento necessario ad Azure AD per l'autenticazione e l'accesso dell'utente. Se questo campo è popolato, Azure AD userà questo URL per avviare l'applicazione da Office 365 e il pannello di accesso di AD Azure. Se questo campo è omesso, Azure AD esegue l'accesso Single Sing-On avviato dal provider di identità quando l'app viene avviata da Office 365, dal pannello di accesso di Azure AD o dall'URL di accesso Single Sign-On di Azure AD.
* **Identificatore** : questo URI deve identificare in modo univoco l'applicazione per cui viene configurato l'accesso Single Sign-On. Si tratta del valore che Azure AD invia all'applicazione come parametro Audience del token SAML e l'applicazione deve convalidarlo. Questo valore viene inoltre visualizzato come ID entità in tutti i metadati SAML forniti dall'applicazione.
* **URL di risposta** : è l'indirizzo a cui l'applicazione prevede di ricevere il token SAML. Viene anche definito URL del servizio consumer di asserzione (ACS). Dopo averli immessi, fare clic su Avanti per passare alla schermata successiva. Questa schermata fornisce informazioni su ciò che è necessario configurare sul lato applicazione in modo che questa accetti un token SAML da Azure AD.
* **Relay State** (Stato di inoltro): lo stato di inoltro è un parametro facoltativo che può indicare all'applicazione dove reindirizzare l'utente dopo il completamento dell'autenticazione. Questo valore è in genere un URL valido nell'applicazione, ma alcune applicazioni usano questo campo in modo diverso. Per informazioni dettagliate, vedere la documentazione sull'accesso Single Sign-On dell'app. La possibilità di configurare lo stato di inoltro è una nuova funzionalità esclusiva del nuovo portale di Azure.

### <a name="user-attributes"></a>Attributi utente
In questa sezione gli amministratori possono visualizzare e modificare gli attributi inviati nel token SAML che Azure AD rilascia all'applicazione a ogni accesso dell'utente.

Nella prima versione di anteprima l'unico attributo modificabile supportato è l'attributo **Identificatore utente** . Il valore di questo attributo corrisponde al campo in Azure AD che identifica in modo univoco ogni utente nell'applicazione. Se, ad esempio, l'app è stata distribuita usando "Indirizzo di posta elettronica" come nome utente e identificatore univoco, il valore sarà impostato sul campo "user.mail" in Azure AD.

La modifica di altri attributi sarà supportata in un'anteprima successiva.

### <a name="saml-signing-certificate"></a>Certificato di firma SAML
Questa sezione mostra i dettagli del certificato usato da Azure AD per firmare i token SAML rilasciati all'applicazione a ogni autenticazione dell'utente. Consente il controllo delle proprietà del certificato corrente, inclusa la data di scadenza.

La possibilità di eseguire il rollover del certificato e di gestire altre opzioni dei certificati sarà supportata in una versione successiva dell'anteprima. Si noti che è ancora possibile eseguire la gestione completa dei certificati nel [portale di Azure classico](active-directory-sso-certs.md).

### <a name="application-configuration"></a>Configurazione dell'applicazione
La sezione finale fornisce la documentazione e/o i controlli necessari per configurare l'applicazione stessa per l'uso di Azure Active Directory come provider di identità.

Il menu a comparsa **Configura applicazione** fornisce nuove istruzioni concise e incorporate per la configurazione dell'applicazione. Anche questa è una funzionalità esclusiva del nuovo portale di Azure.

> [!NOTE]
> Per un esempio completo di documentazione incorporata, vedere l'applicazione Salesforce.com. La documentazione per app aggiuntive viene aggiunta continuamente durante la fase di anteprima.
> 
> 

![Documenti incorporati][3]

## <a name="password-based-sign-on"></a>Password-based sign on
Se supportato dall'applicazione, selezionando la modalità di accesso Single Sign-On basato su password e quindi **Salva** sarà possibile eseguire la configurazione immediata dell'app per l'accesso Single Sign-On basato su password. Per altre informazioni sulla distribuzione dell'accesso Single Sign-On basato su password, vedere [Come funziona Single Sign-On con Azure Active Directory (PHP)?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Password-based sign on][4]

## <a name="linked-sign-on"></a>Linked sign on
Se supportato dall'applicazione, la selezione della modalità di accesso Single Sign-On collegato consente di immettere l'URL a cui si vuole che il riquadro di accesso di Azure AD oppure Office 365 eseguano il reindirizzamento quando gli utenti selezionano l'app. Per altre informazioni sull'accesso Single Sign-On collegato, noto in precedenza come "Accesso Single Sign-On esistente", vedere [Come funziona Single Sign-On con Azure Active Directory (PHP)?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Linked sign-on (Accesso collegato)][5]

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG



<!--HONumber=Feb17_HO3-->


