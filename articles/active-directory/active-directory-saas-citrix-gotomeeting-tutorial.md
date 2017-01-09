---
title: 'Esercitazione: Integrazione di Azure Active Directory con Citrix GoToMeeting | Microsoft Docs'
description: Informazioni su come usare Citrix GoToMeeting con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7d7897f6-b88e-4dd5-8f3a-e612337b1413
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 5b775b3cb53086daea6258cccc15cd77fd0e68d9
ms.openlocfilehash: c4f33f3f856c5017c5b156e2b27b3b82a275afa6


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>Esercitazione: Integrazione di Azure Active Directory con Citrix GoToMeeting

Questa esercitazione descrive l'integrazione di Azure e Citrix GoToMeeting. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Citrix GoToMeeting

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Citrix GoToMeeting
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Configurazione](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuration")

## <a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>Abilitazione dell'integrazione dell'applicazione per Citrix GoToMeeting
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Citrix GoToMeeting.

### <a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Citrix GoToMeeting, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applications")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungi applicazione](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Add application")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Add an application from gallery")

6. Nella **casella di ricerca** digitare **Citrix GoToMeeting**.
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7. Nel riquadro dei risultati selezionare **Citrix GoToMeeting** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Citrix GoToMeeting tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 nel tenant di Citrix GoToMeeting.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Citrix GoToMeeting** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **CONFIGURA ACCESSO SINGLE SIGN-ON**.
   
    ![Abilita Single Sign-On](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Enable single sign-on")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Citrix GoToMeeting** selezionare **Single Sign-On di Microsoft Azure AD**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configure single sign-on")

3. Nella pagina **Configurare le impostazioni dell'app** fare clic su **Avanti**. 
   
    ![Abilita Single Sign-On](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Enable single sign-on")

4. Nella pagina **Configura accesso Single Sign-On in Citrix GoToMeeting** fare clic su **Scarica certificato** e quindi salvare il file di certificato nel computer.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configure single sign-on")

5. In un'altra finestra del browser, accedere al [Citrix Organization Center (Centro di organizzazione di Citrix)](https://account.citrixonline.com/organization/administration/).

6. Fare clic sulla scheda **Identity Provider** e seguire questa procedura:  
   
    ![SAML setup](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "SAML setup")
   
    a. Selezionare **Manual**

    b. Nella finestra di dialogo **Configura accesso Single Sign-On in Citrix GoToMeeting** del Portale di Azure classico copiare il valore di **URL pagina di accesso** e quindi incollarlo nella casella di testo **Sign-in page URL**. 

    c. Nella finestra di dialogo **Configura accesso Single Sign-On in Citrix GoToMeeting** del Portale di Azure classico copiare il valore di **URL pagina di disconnessione** e quindi incollarlo nella casella di testo **Sign-out page URL**.

    d. Nella finestra di dialogo **Configura accesso Single Sign-On in Citrix GoToMeeting** del portale di Azure classico copiare il valore di **ID entità** e quindi incollarlo nella casella di testo **Identity provider Entity ID**.

    e. Per caricare il certificato scaricato, fare clic su **Upload Certificate**.

    f. Fare clic su **Save**.


1. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configure single sign-on")

2. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
   
    ![SAML setup](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "SAML setup")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Questa sezione descrive come abilitare il provisioning degli account utente di Azure Active Directory in Citrix GoToMeeting.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Nella pagina di integrazione dell'applicazione **Citrix GoToMeeting** del Portale di Azure classico fare clic su **Configura provisioning utenti** per aprire la finestra di dialogo **Configura provisioning utenti**.
   
    ![Configura provisioning utenti](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configure user provisioning")

2. Nella pagina **Impostazioni e credenziali amministratore** seguire questa procedura:
   
    ![Configura provisioning utenti](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configure user provisioning")
   
    a. Nella casella di testo **Nome utente amministratore Citrix GoToMeeting** digitare il nome utente di un amministratore.

    b. Nella casella di testo **Password amministratore Citrix GoToMeeting** digitare la password dell'amministratore.

    c. Fare clic su **Next**.

1. Nella pagina **Conferma** fare clic sul segno di spunta per salvare la configurazione.
2. Fare clic sul pulsante **Convalida** , per verificare la configurazione.

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>Per assegnare gli utenti a Citrix GoToMeeting, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Citrix GoToMeeting** fare clic su **Assegna utenti**.
   
    ![Assegna utenti](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Assign users")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Yes")

È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con Dropbox for Business.

Come primo passaggio di verifica, è possibile controllare lo stato del provisioning facendo clic su Dashboard nella pagina di integrazione dell'applicazione **Citrix GoToMeeting** del portale di Azure classico.

![Dashboard](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

Un ciclo di provisioning utenti completato correttamente è indicato da uno stato correlato:

![Integration status](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Integration status")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).




<!--HONumber=Nov16_HO3-->


