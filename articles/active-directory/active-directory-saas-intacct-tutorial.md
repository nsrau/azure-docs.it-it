---
title: 'Esercitazione: Integrazione di Azure Active Directory con Intacct | Microsoft Docs'
description: Informazioni su come usare Intacct con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
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
# Esercitazione: Integrazione di Azure Active Directory con Intacct
Questa esercitazione descrive l'integrazione di Azure e Intacct. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Intacct

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Intacct potranno accedere all'applicazione tramite il sito aziendale di Intacct (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Intacct
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-intacct-tutorial/IC790030.png "Scenario")

## Abilitazione dell'integrazione dell'applicazione per Intacct
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Intacct.

### Per abilitare l'integrazione dell'applicazione per Intacct, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-intacct-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiunta di un'applicazione](./media/active-directory-saas-intacct-tutorial/IC749321.png "Aggiunta di un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-intacct-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Intacct**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-intacct-tutorial/IC790031.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Intacct** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
   
   ## Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Intacct tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Intacct** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configura accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Intacct** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configura accesso Single Sign-On")
3. Nella casella di testo **Intacct Sign On URL** (URL di accesso Intacct) della pagina **Configura URL app** digitare l'URL usando il modello "*https://Intacct.com/company*" e fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configura URL app")
4. Nella pagina **Configura accesso Single Sign-On in Intacct** fare clic su **Download certificato** e quindi salvare il file di certificato nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configura accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Intacct come amministratore.
6. Fare clic sulla scheda **Azienda** e quindi su **Informazioni azienda**.
   
   ![Company](./media/active-directory-saas-intacct-tutorial/IC790037.png "Company")
7. Fare clic sulla scheda **Sicurezza** e quindi su **Modifica**.
   
   ![Sicurezza](./media/active-directory-saas-intacct-tutorial/IC790038.png "Sicurezza")
8. Nella sezione **Single sign on (SSO)** seguire questa procedura:
   
   ![Single Sign On](./media/active-directory-saas-intacct-tutorial/IC790039.png "Single Sign On")
   
   1. Selezionare **Abilita Single Sign-On**.
   2. In **Tipo di provider di identità** selezionare **SAML 2.0**.
   3. Nella finestra di dialogo **Configure single sign-on at Intacct** (Configura accesso Single Sign-On in Intacct) del portale di Azure classico copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Issuer URL** (URL autorità di certificazione).
   4. Nella pagina della finestra di dialogo **Configure single sign-on at Intacct** (Configura accesso Single Sign-On in Intacct) del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Login URL** (URL di accesso).
   5. Creare un file **con codifica Base 64** dal certificato scaricato.
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato**.
   7. Fare clic su **Save**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configura accesso Single Sign-On")
   
   ## Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Intacct, è necessario eseguirne il provisioning in Intacct. Nel caso di Intacct, il provisioning è un'attività manuale.

### Per eseguire il provisioning di un account utente, seguire questa procedura:
1. Accedere al tenant di **Intacct**.
2. Fare clic sulla scheda **Azienda** e quindi su **Utenti**.
   
   ![Utenti](./media/active-directory-saas-intacct-tutorial/IC790041.png "Utenti")
3. Fare clic sulla scheda **Aggiungi**.
   
   ![Aggiungi](./media/active-directory-saas-intacct-tutorial/IC790042.png "Aggiungi")
4. Nella sezione **Informazioni utente** seguire questa procedura:
   
   ![User Information](./media/active-directory-saas-intacct-tutorial/IC790043.png "User Information")
   
   1. Nelle caselle di testo **ID utente**, **Cognome**, **Nome**, **Indirizzo email**, **Titolo** e **Telefono** digitare l'ID utente, il cognome, il nome, l'indirizzo di posta elettronica, il titolo e il telefono di un account Azure AD di cui si vuole eseguire il provisioning.
   2. Selezionare i **privilegi di amministratore** di un account Azure AD di cui si vuole eseguire il provisioning.
   3. Fare clic su **Save**.
      
      > [!NOTE]
      > Il titolare dell'account AAD riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
      > 
      > 

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Intacct per eseguire il provisioning degli account utente di AAD.
> 
> 

## Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### Per assegnare gli utenti a Intacct seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Intacct** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-intacct-tutorial/IC790044.png "Assegna utenti")
3. Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-intacct-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->