---
title: 'Esercitazione: Integrazione di Azure Active Directory con Panopto | Documentazione Microsoft'
description: Informazioni su come usare Panopto con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6084b2761e33609310461d90868179770d7acc01


---
# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Esercitazione: Integrazione di Azure Active Directory con Panopto
In questa esercitazione viene illustrata l'integrazione di Azure e Panopto.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Panopto

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Panopto saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Panopto (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Panopto
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-panopto-tutorial/IC777665.png "Scenario")

## <a name="enabling-the-application-integration-for-panopto"></a>Abilitazione dell'integrazione dell'applicazione per Panopto
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Panopto.

### <a name="to-enable-the-application-integration-for-panopto-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Panopto, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-panopto-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-panopto-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-panopto-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Panopto**.
   
   ![Raccolta Appkication](./media/active-directory-saas-panopto-tutorial/IC777666.png "Appkication Gallery")
7. Nel riquadro dei risultati selezionare **Panopto**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Panopto](./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Panopto tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Panopto** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-panopto-tutorial/IC777667.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Panopto** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-panopto-tutorial/IC777668.png "Configure single sign-on")
3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL accesso Panopto** nel formato "*https://\<nome-tenant\>. Panopto.com*" e fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-panopto-tutorial/IC777528.png "Configure app URL")
4. Nella pagina **Configura accesso Single Sign-On in Panopto** fare clic su **Scarica certificato** per scaricare il certificato e salvare il file di certificato nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-panopto-tutorial/IC777669.png "Configure single sign-on")
5. In un'altra finestra del Web browser accedere al sito aziendale di Panopto come amministratore.
6. Nella barra degli strumenti a sinistra, fare clic su **System** (Sistema) quindi fare clic su **Identity Providers** (Provider di identità).
   
   ![System](./media/active-directory-saas-panopto-tutorial/IC777670.png "System")
7. Fare clic su **Aggiungi provider**.
   
   ![Provider di identità](./media/active-directory-saas-panopto-tutorial/IC777671.png "Identity Providers")
8. Nella sezione del provider SAML, eseguire la procedura seguente:
   
   ![Configurazione SaaS](./media/active-directory-saas-panopto-tutorial/IC777672.png "SaaS configuration")
   
   1. Dall'elenco **Provider Type** (Tipo di provider) selezionare **SAML20**
   2. Nella casella di testo **Nome istanza** , digitare un nome per l'istanza.
   3. Nella casella di testo **Descrizione** , digitare una descrizione.
   4. Nella finestra di dialogo **Configure single sign-on at Panopto** (Configura accesso Single Sign-On in Panopto) del portale di Azure classico copiare il valore di **URL autorità di certificazione** e quindi incollarlo nella casella di testo **Issuer** (Autorità di certificazione).
   5. Nella finestra di dialogo **Configura accesso Single Sign-On in Panopto** del portale di Azure classico copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **Bounce Page Url** (URL pagina non recapitata).
   6. Creare un file **con codifica Base 64** dal certificato scaricato.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   7. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Chiave pubblica**
   8. Fare clic su **Salva**.
      ![Salva](./media/active-directory-saas-panopto-tutorial/IC777673.png "Save")
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-panopto-tutorial/IC777674.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in Panopto.  
Quando un utente assegnato tenta di accedere a Panopto usando il pannello di accesso, Panopto controlla se l'utente esiste.  
Se l’account utente non è disponibile, viene creato automaticamente da Panopto.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Panopto per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-panopto-perform-the-following-steps"></a>Per assegnare gli utenti a Panopto, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Panopto** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-panopto-tutorial/IC777675.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-panopto-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


