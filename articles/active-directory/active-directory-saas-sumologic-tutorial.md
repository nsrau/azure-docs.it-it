---
title: 'Esercitazione: Integrazione di Azure Active Directory con SumoLogic| Documentazione Microsoft'
description: Informazioni su come usare SumoLogic con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 3fbf55ddc13e6489e81eb04c962e833600bfc846
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Esercitazione: Integrazione di Azure Active Directory con SumoLogic
In questa esercitazione viene descritta l'integrazione di Azure e SumoLogic.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant SumoLogic

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a SumoLogic potranno accedere all'applicazione tramite il sito aziendale di SumoLogic (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione SumoLogic
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-sumologic-tutorial/IC778549.png "Scenario")

## <a name="enable-the-application-integration-for-sumologic"></a>Abilitare l'integrazione dell'applicazione per SumoLogic
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per SumoLogic.

**Per abilitare l'integrazione dell'applicazione per SumoLogic, eseguire la procedura seguente:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-sumologic-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-sumologic-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-sumologic-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **sumologic**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-sumologic-tutorial/IC778550.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **SumoLogic**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![SumoLogic](./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a SumoLogic tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  

Come parte di questa procedura, verrà richiesto di caricare un file di certificato codificato in base 64 sul proprio tenant SumoLogic.  

Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SumoLogic** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a SumoLogic** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configurare l'accesso Single Sign-On")

3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL accesso SumoLogic** nel formato "*https://\<nome-tenant\>.SumoLogic.com*", quindi fare clic su **Avanti**.
   
    ![Configura URL app](./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configura URL app")

4. Nella pagina **Configura accesso Single Sign-On in SumoLogic** fare clic su **Scarica certificato** per scaricare il file del certificato, quindi salvarlo nel computer.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configurare l'accesso Single Sign-On")

5. In un'altra finestra del browser Web accedere al sito aziendale di SumoLogic come amministratore.

6. Passare a **Manage (Gestisci) \> Security (Sicurezza)**.
   
    ![Gestione](./media/active-directory-saas-sumologic-tutorial/IC778556.png "Gestione")

7. Fare clic su **SAML**.
   
    ![Impostazioni di sicurezza globale](./media/active-directory-saas-sumologic-tutorial/IC778557.png "Impostazioni di sicurezza globale")

8. Dall'elenco **Select a configuration or create a new one** (Selezionare o creare una configurazione) selezionare **Azure AD**, quindi fare clic su **Configure** (Configura).
   
    ![Configurare SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configurare SAML 2.0")

9. Nella finestra di dialogo **Configura SAML 2.0** , eseguire la procedura seguente:
   
    ![Configurare SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configurare SAML 2.0")   
  1. Nella casella di testo **Configuration Name** (Nome configurazione) digitare **Azure AD**. 
  2. Selezionare **Debug Mode**.
  3. Nella finestra di dialogo **Configura accesso Single Sign-On in SumoLogic** del portale di Azure classico copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Issuer** (Autorità di certificazione).
  4. Nella finestra di dialogo **Configura accesso Single Sign-On in SumoLogic** del portale di Azure classico copiare il valore di **URL richiesta di autenticazione** e incollarlo nella casella di testo **Authn Request URL** (URL richiesta di autenticazione).
  5. Creare un file **con codifica Base 64** dal certificato scaricato.  
      
     >[!TIP]
     >Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).
     >  

  6. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollare l’intero certificato nella casella di testo **Certificate X.509** .
  7. Per **Email Attribute** (Attributo e-mail), selezionare **Use SAML subject** (Usa oggetto SAML).  
  8. Selezionare **SP initiated Login Configuration**.
  9. Nella casella di testo **Login Path** (Percorso di accesso) digitare **Azure** e fare clic su **Salva**.

10. Nella finestra di dialogo **Configura accesso Single Sign-On in SumoLogic** del portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configurare l'accesso Single Sign-On")

## <a name="configure-user-provisioning"></a>Configura provisioning utenti
Per consentire agli utenti di Azure AD di accedere a SumoLogic, è necessario eseguirne il provisioning in SumoLogic.  

* Nel caso di SumoLogic, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **SumoLogic** .

2. Passare a **Manage (Gestisci) \> Users (Utenti)**.
   
    ![Utenti](./media/active-directory-saas-sumologic-tutorial/IC778561.png "Utenti")

3. Fare clic su **Aggiungi**.
   
    ![Utenti](./media/active-directory-saas-sumologic-tutorial/IC778562.png "Utenti")

4. Nella finestra di dialogo **New User** , eseguire la procedura seguente:
   
    ![Nuovo utente](./media/active-directory-saas-sumologic-tutorial/IC778563.png "Nuovo utente")  
  1. Digitare le informazioni correlate dell'account Azure AD di cui si vuole effettuare il provisioning nelle caselle di testo **First Name** (Nome), **Last Name** (Cognome) e **Email** (Posta elettronica).
  2. Selezionare un ruolo.
  3. Per **Status** (Stato) selezionare **Active** (Attivo).
  4. Fare clic su **Save**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento di creazione di account utente SumoLogic o qualsiasi API fornita da SumoLogic per eseguire il provisioning degli account utente di Azure AD. 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a SumoLogic, eseguire la procedura seguente:**

1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **SumoLogic** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-sumologic-tutorial/IC778564.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-sumologic-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


