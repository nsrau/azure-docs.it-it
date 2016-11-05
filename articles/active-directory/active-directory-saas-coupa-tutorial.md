---
title: 'Esercitazione: Integrazione di Azure Active Directory con Coupa | Microsoft Docs'
description: Informazioni su come usare Coupa con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2016
ms.author: jeedes

---
# Esercitazione: Integrazione di Azure Active Directory con Coupa
Questa esercitazione descrive l'integrazione di Azure e Coupa. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione Coupa abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Coupa potranno eseguire l'accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Coupa
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-coupa-tutorial/IC791897.png "Scenario")

## Abilitazione dell'integrazione dell'applicazione per Coupa
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Coupa.

### Per abilitare l'integrazione dell'applicazione per Coupa, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-coupa-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiunta di un'applicazione](./media/active-directory-saas-coupa-tutorial/IC749321.png "Aggiunta di un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-coupa-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Coupa**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-coupa-tutorial/IC791898.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Coupa** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
   
   ## Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione ad Coupa tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per Coupa richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

### Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Accedere al sito aziendale di Coupa come amministratore.
2. Passare a **Configurazione > Controlli di sicurezza**.
   
   ![Security Controls](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")
3. Per scaricare il file dei metadati Coupa nel computer fare clic su **Scarica e importa i metadati SP**.
   
   ![Coupa SP metadata](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP metadata")
4. In un'altra finestra del browser accedere al portale di Azure classico.
5. Nella pagina di integrazione dell'applicazione **Coupa** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-coupa-tutorial/IC791902.png "Configura accesso Single Sign-On")
6. Nella pagina **Stabilire come si desidera che gli utenti accedano a Coupa** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-coupa-tutorial/IC791903.png "Configura accesso Single Sign-On")
7. Nella pagina **Configura URL app** seguire questa procedura:
   
   ![Configura URL app](./media/active-directory-saas-coupa-tutorial/IC791904.png "Configura URL app")
   
   1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Coupa (ad esempio: "*http://company.Coupa.com*”).
   2. Aprire il file dei metadati Coupa scaricato e quindi copiare il valore di **Indice/URL AssertionConsumerService**.
   3. Nella casella di testo **URL di risposta Coupa** incollare il valore di **Indice/URL AssertionConsumerService**.
   4. Fare clic su **Next**.
8. Nella pagina **Configura accesso Single Sign-On in Coupa** per scaricare il file dei metadati, fare clic su **Download metadati** e quindi salvarlo in locale nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-coupa-tutorial/IC791905.png "Configura accesso Single Sign-On")
9. Nel sito della società Coupa passare a **Configurazione > Controllo di sicurezza**.
   
   ![Security Controls](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")
10. Nella sezione **Accesso mediante le credenziali di Coupa** seguire questa procedura:
    
    ![Log in using Coupa credentials](./media/active-directory-saas-coupa-tutorial/IC791906.png "Log in using Coupa credentials")
    
    1. Selezionare **Accedere mediante SAML**.
    2. Fare clic su **Sfoglia** per caricare il file di metadati di Azure Active Directory scaricato.
    3. Fare clic su **Save**.
11. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-coupa-tutorial/IC791907.png "Configura accesso Single Sign-On")
    
    ## Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Coupa, è necessario eseguirne il provisioning in Coupa. Nel caso di Coupa, il provisioning è un'attività manuale.

### Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **Coupa** come amministratore.
2. Nel menu in alto fare clic su **Configura** e quindi fare clic su **Utenti**.
   
   ![Utenti](./media/active-directory-saas-coupa-tutorial/IC791908.png "Utenti")
3. Fare clic su **Crea**.
   
   ![Creare gli utenti](./media/active-directory-saas-coupa-tutorial/IC791909.png "Creare gli utenti")
4. Nella sezione **Crea utente** seguire questa procedura:
   
   ![User Details](./media/active-directory-saas-coupa-tutorial/IC791910.png "User Details")
   
   1. Nelle caselle di testo **Accesso**, **Nome**, **Cognome**, **Single Sign-On ID**, **Email** immettere i dati di accesso, il nome, il cognome, l'ID di Single Sign-On e l'indirizzo di posta elettronica di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Crea**.
   
   > [!NOTE]
   > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
   > 
   > 

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Coupa per eseguire il provisioning degli account utente di AAD.
> 
> 

## Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### Per assegnare gli utenti a Coupa, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Coupa** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-coupa-tutorial/IC791911.png "Assegna utenti")
3. Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-coupa-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->