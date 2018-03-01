---
title: Gestione dell'accesso Single Sign-On per le app aziendali in Azure Active Directory | Microsoft Docs
description: Gestire le impostazioni Single Sign-On per le app aziendali all'interno dell'organizzazione da una raccolta di applicazioni di Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: bcc954d3-ddbe-4ec2-96cc-3df996cbc899
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: curtand
ms.reviewer: asmalser
ms.openlocfilehash: dd251ca4696101911293caa59c54c3354a1627a5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Gestione dell'accesso Single Sign-On per le app aziendali

Questo articolo descrive come usare il [portale di Azure](https://portal.azure.com) per gestire impostazioni del Single Sign-On per le applicazioni aziendali. Le app aziendali sono app distribuite e usate all'interno dell'organizzazione. Questo articolo si applica in particolare alle app aggiunte dalla [raccolta di applicazioni di Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>Individuazione delle app nel portale
Tutte le app aziendali impostate per il Single Sign-On possono essere visualizzate e gestite nel portale di Azure. Le applicazioni sono disponibili nella sezione **Tutti i servizi** &gt; **Applicazioni aziendali** del portale. 

![Pannello Applicazioni aziendali][1]

Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le app che sono state configurate. Se si seleziona un'app, vengono visualizzate le risorse disponibili per l'app, in cui è possibile aprire i report relativi all'app e gestire varie impostazioni.

Per gestire le impostazioni dell'accesso Single Sign-On, selezionare **Single Sign-On**.

![Pannello Risorsa applicazione][2]

## <a name="single-sign-on-modes"></a>Modalità dell'accesso Single Sign-On
L'area **Single Sign-On** inizia con un menu **Modalità** che consente di configurare la modalità Single Sign-On. Le opzioni disponibili includono:

* **Accesso basato su SAML**: questa opzione è disponibile se l'applicazione supporta l'accesso Single Sign-On completamente federato con Azure Active Directory mediante i protocolli SAML 2.0, OpenID Connect o WS-Federation.
* **Accesso basato su password**: questa opzione è disponibile se Azure AD supporta la compilazione di moduli con password per questa applicazione.
* **Accesso collegato**: precedentemente nota come "Accesso Single Sign-On esistente", questa opzione consente agli amministratori di inserire un collegamento all'applicazione nel riquadro di accesso di Azure AD degli utenti o nella schermata di avvio delle applicazioni di Office 365.

Per altre informazioni su queste modalità, vedere [Come funziona Single Sign-On con Azure Active Directory (PHP)?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>Accesso basato su SAML
L'opzione **Accesso basato su SAML** è suddivisa in quattro sezioni:

### <a name="domains-and-urls"></a>Domains and URLs (Domini e URL)
In questa sezione vengono aggiunti alla directory di Azure AD tutti i dettagli sul dominio e sugli URL dell'applicazione. Tutti gli input necessari per il funzionamento dell'accesso Single Sign-On nell'app vengono visualizzati direttamente sullo schermo, mentre tutti gli input facoltativi possono essere visualizzati selezionando la casella di controllo **Mostra impostazioni URL avanzate**. L'elenco completo di input supportati include gli elementi seguenti:

* **URL di accesso** da cui l'utente accede all'applicazione. Se l'applicazione è configurata per eseguire l'accesso Single Sign-On avviato dal provider di servizi, quando un utente apre questo URL, il provider di servizi esegue il reindirizzamento ad Azure AD per l'autenticazione e l'accesso dell'utente. 
  * Se questo campo è popolato, Azure AD usa questo URL per avviare l'applicazione da Office 365 e il pannello di accesso di AD Azure.
  * Se questo campo è omesso, Azure AD esegue l'accesso Single Sign-On avviato dal provider di identità quando l'app viene avviata da Office 365, dal pannello di accesso di Azure AD o dall'URL di accesso Single Sign-On di Azure AD.
* **Identificatore**: questo URI deve identificare in modo univoco l'applicazione per cui viene configurato l'accesso Single Sign-On. Si tratta del valore che Azure AD invia all'applicazione come parametro Audience del token SAML e l'applicazione deve convalidarlo. Questo valore viene inoltre visualizzato come ID entità in tutti i metadati SAML forniti dall'applicazione.
* **URL di risposta**: è l'indirizzo a cui l'applicazione prevede di ricevere il token SAML. Viene anche definito URL del servizio consumer di asserzione (ACS). Dopo averli immessi, fare clic su Avanti per passare alla schermata successiva. Questa schermata fornisce informazioni su ciò che è necessario configurare sul lato applicazione in modo che questa accetti un token SAML da Azure AD.
* **Relay State** (Stato di inoltro): lo stato di inoltro è un parametro facoltativo che può indicare all'applicazione dove reindirizzare l'utente dopo il completamento dell'autenticazione. Sebbene questo valore sia, in genere, un URL valido nell'applicazione, alcune applicazioni usano questo campo in modo diverso. Per informazioni dettagliate, vedere la documentazione sull'accesso Single Sign-On dell'app. La possibilità di configurare lo stato di inoltro è una nuova funzionalità esclusiva del nuovo portale di Azure.

### <a name="user-attributes"></a>Attributi utente
In questa sezione gli amministratori possono visualizzare e modificare gli attributi inviati nel token SAML che Azure AD rilascia all'applicazione a ogni accesso dell'utente.

L'unico attributo modificabile supportato è l'attributo **Identificatore utente**. Il valore di questo attributo corrisponde al campo in Azure AD che identifica in modo univoco ogni utente nell'applicazione. Se, ad esempio, l'app è stata distribuita usando "Indirizzo di posta elettronica" come nome utente e identificatore univoco, il valore sarà impostato sul campo "user.mail" in Azure AD.

### <a name="saml-signing-certificate"></a>Certificato di firma SAML
Questa sezione mostra i dettagli del certificato usato da Azure AD per firmare i token SAML rilasciati all'applicazione a ogni autenticazione dell'utente. Consente il controllo delle proprietà del certificato corrente, inclusa la data di scadenza.

### <a name="application-configuration"></a>Configurazione dell'applicazione
La sezione finale fornisce la documentazione e/o i controlli necessari per configurare l'applicazione stessa per l'uso di Azure Active Directory come provider di identità.

Il menu a comparsa **Configura applicazione** fornisce nuove istruzioni concise e incorporate per la configurazione dell'applicazione. Anche questa è una funzionalità esclusiva del nuovo portale di Azure.

> [!NOTE]
> Per un esempio completo di documentazione incorporata, vedere l'applicazione Salesforce.com. La documentazione per app aggiuntive viene aggiunta continuamente.
> 
> 

![Documenti incorporati][3]

## <a name="password-based-sign-on"></a>Accesso basato su password
Se supportato dall'applicazione, selezionando la modalità di accesso Single Sign-On basato su password e quindi **Salva** sarà possibile eseguire la configurazione immediata dell'app per l'accesso Single Sign-On basato su password. Per altre informazioni sulla distribuzione dell'accesso Single Sign-On basato su password, vedere [Come funziona Single Sign-On con Azure Active Directory (PHP)?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Accesso basato su password][4]

## <a name="linked-sign-on"></a>Linked sign-on (Accesso collegato)
Se supportato dall'applicazione, la selezione della modalità di accesso Single Sign-On collegato consente di immettere l'URL a cui si vuole che il riquadro di accesso di Azure AD oppure Office 365 eseguano il reindirizzamento quando gli utenti selezionano l'app. Per altre informazioni sull'accesso Single Sign-On collegato, noto in precedenza come "Accesso Single Sign-On esistente", vedere [Come funziona Single Sign-On con Azure Active Directory (PHP)?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Linked sign-on (Accesso collegato)][5]

##<a name="feedback"></a>Commenti e suggerimenti

Speriamo che gli utenti usino il nuovo Azure AD e inviino commenti e suggerimenti. È possibile inviare commenti e idee per apportare miglioramenti nella sezione **Portale di amministrazione** del [forum dei commenti](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Microsoft accoglie i suggerimenti degli utenti per definire nuove funzionalità ogni giorno.

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
