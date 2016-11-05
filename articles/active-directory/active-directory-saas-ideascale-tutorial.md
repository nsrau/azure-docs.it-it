---
title: 'Esercitazione: Integrazione di Azure Active Directory con IdeaScale | Microsoft Docs'
description: Informazioni su come usare IdeaScale con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2016
ms.author: jeedes

---
# Esercitazione: Integrazione di Azure Active Directory con IdeaScale
Questa esercitazione descrive l'integrazione di Azure e IdeaScale. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di IdeaScale abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a IdeaScale potranno eseguire l'accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per IdeaScale
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scenario")

## Abilitazione dell'integrazione dell'applicazione per IdeaScale
Questa sezione descrive come abilitare l'integrazione dell'applicazione per IdeaScale.

### Per abilitare l'integrazione dell'applicazione per IdeaScale, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-ideascale-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiunta di un'applicazione](./media/active-directory-saas-ideascale-tutorial/IC749321.png "Aggiunta di un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-ideascale-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **IdeaScale**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-ideascale-tutorial/IC790841.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **IdeaScale** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
   
   ## Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a IdeaScale tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per IdeaScale richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

### Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **IdeaScale** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configura accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a IdeaScale** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configura accesso Single Sign-On")
3. Nella casella di testo **IdeaScale Sign On URL** (URL di accesso IdeaScale) della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione IdeaScale, ad esempio: "*https://company.IdeaScale.com*" e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configura URL app")
4. Nella pagina **Configura accesso Single Sign-On in IdeaScale**, per scaricare il file di metadati fare clic su **Download metadati** e quindi salvare il file di metadati in locale nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configura accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di IdeaScale come amministratore.
6. Passare a **Impostazioni Community**.
   
   ![Community Settings](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
7. Passare a **Security > Single Signon Settings**.
   
   ![Single Signon Settings](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Single Signon Settings")
8. In **Tipo di Single-Signon** selezionare **SAML 2.0**.
   
   ![Single Signon Type](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Single Signon Type")
9. Nella finestra di dialogo **Impostazioni di Single Sign-O** seguire questa procedura:
   
   ![Single Signon Settings](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Single Signon Settings")
   
   1. Nella finestra di dialogo **Configure single sign-on at IdeaScale** (Configura accesso Single Sign-On in IdeaScale) del portale di Azure classico copiare il valore di **ID entità** e incollarlo nella casella di testo **SAML IdP Entity ID** (ID entità IdP SAML).
   2. Copiare il contenuto del file dei metadati scaricato e incollarlo nella casella di testo **SAML IdP Metadata**.
   3. Nella finestra di dialogo **Configure single sign-on at IdeaScale** (Configura accesso Single Sign-On in IdeaScale) del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Logout Success URL** (URL disconnessione riuscita).
   4. Fare clic su **Salva modifiche**.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configura accesso Single Sign-On")
    
    ## Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a IdeaScale, è necessario eseguirne il provisioning in IdeaScale. Nel caso di IdeaScale, il provisioning è un'attività manuale.

### Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **IdeaScale** come amministratore.
2. Passare a **Impostazioni Community**.
   
   ![Community Settings](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
3. Scegliere **Impostazioni Basic > Gestione membro**.
4. Fare clic su **Aggiungi membro**.
   
   ![Member Management](./media/active-directory-saas-ideascale-tutorial/IC790852.png "Member Management")
5. Nella sezione Add New Member seguire questa procedura:
   
   ![Add New Member](./media/active-directory-saas-ideascale-tutorial/IC790853.png "Add New Member")
   
   1. Nella casella di testo **Indirizzi email** digitare l'indirizzo di posta elettronica di un account di AAD valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Salva modifiche**.
   
   > [!NOTE]
   > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
   > 
   > 

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da IdeaScale per eseguire il provisioning degli account utente di AAD.
> 
> 

## Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### Per assegnare gli utenti a IdeaScale, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **IdeaScale** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-ideascale-tutorial/IC790854.png "Assegna utenti")
3. Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-ideascale-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->