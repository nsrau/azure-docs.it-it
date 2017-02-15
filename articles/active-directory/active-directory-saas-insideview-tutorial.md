---
title: 'Esercitazione: Integrazione di Azure Active Directory con InsideView | Documentazione Microsoft'
description: Informazioni su come usare InsideView con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0da7c8f20fb6bbe78294aab2fb69c24a2dddd877


---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Esercitazione: Integrazione di Azure Active Directory con InsideView
Questa esercitazione descrive l'integrazione di Azure e InsideView.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di InsideView

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a InsideView saranno in grado di eseguire l'accesso Single Sign-On all'applicazione tramite il sito aziendale di InsideView (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per InsideView
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-insideview-tutorial/IC794128.png "Scenario")

## <a name="enabling-the-application-integration-for-insideview"></a>Abilitazione dell'integrazione dell'applicazione per InsideView
Questa sezione descrive come abilitare l'integrazione dell'applicazione per InsideView.

### <a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per InsideView, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-insideview-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-insideview-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **InsideView**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-insideview-tutorial/IC794129.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **InsideView** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a InsideView con il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **InsideView** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configure Single SignOn")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a InsideView** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configure Single SignOn")
3. Nella casella di testo **URL di risposta InsideView** della pagina **Configure App URL** (Configura URL app) digitare l'URL SSO di InsideView, ad esempio `https://my.insideview.com/iv/<STS Name>/login.iv`, e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in InsideView** fare clic su **Scarica certificato** e quindi salvare il file del certificato nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configure Single SignOn")
5. In un'altra finestra del Web browser accedere al sito aziendale di InsideView come amministratore.
6. Nella barra degli strumenti in alto fare clic su **Admin** (Amministratore), **SingleSignOn Settings** (Impostazioni Single Sign-On) e quindi su **Add SAML** (Aggiungi SAML).
   
   ![SAML Single Sign On Settings](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign On Settings")
7. Nella sezione **Aggiungi un nuovo SAML** seguire questa procedura:
   
   ![Aggiungi un nuovo SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Add a New SAML")
   
   1. Nella casella di testo **Nome STS** digitare un nome per la configurazione.
   2. Nella finestra di dialogo **Configura accesso Single Sign-On in InsideView** del portale di Azure classico copiare il valore di **Endpoint avviato da provider di servizi** e incollarlo nella casella di testo **SamlP/WS-Fed Unsolicated EndPoint** (Endpoint non richiesto SamlP/WS-Fed).
   3. Creare un file **con codifica Base 64** dal certificato scaricato.
      
      > [!TIP]
      > Per informazioni dettagliate, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato STS** .
   5. Nella casella di testo **Crm User Id Mapping** (Mapping ID utente CRM) digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   6. Nella casella di testo **Crm Email Mapping** (Mapping indirizzo di posta elettronica CRM) digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   7. Nella casella di testo **Crm First Name Mapping** (Mapping nome CRM) digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
   8. Nella casella di testo **Crm lastName Mapping** (Mapping cognome CRM) digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
   9. Fare clic su **Save**.
8. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a InsideView, è necessario eseguirne il provisioning in InsideView.  
Nel caso di InsideView, il provisioning è un'attività manuale.

Per creare utenti o contatti in InsideView, contattare il responsabile della soddisfazione del cliente o inviare un messaggio di posta elettronica a **support@insideview.com**

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da InsideView per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Per assegnare gli utenti a InsideView, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **InsideView** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-insideview-tutorial/IC794138.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-insideview-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


