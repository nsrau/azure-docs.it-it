---
title: 'Esercitazione: Integrazione di Azure Active Directory con SmarterU | Documentazione Microsoft'
description: Informazioni su come usare SmarterU con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/08/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 451369e21e7471180b6cd8c77d62b157d0bcddff
ms.openlocfilehash: 058f84f57c18735d42b95a74aee74468ccef0f8d
ms.lasthandoff: 12/14/2016


---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Esercitazione: Integrazione di Azure Active Directory con SmarterU
Questa esercitazione descrive l'integrazione di Azure e SmarterU.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Un tenant SmarterU

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a SmarterU potranno accedere all'applicazione tramite il sito aziendale di SmarterU (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per SmarterU
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-smarteru-tutorial/IC777320.png "Scenario")

## <a name="enabling-the-application-integration-for-smarteru"></a>Abilitazione dell'integrazione dell'applicazione per SmarterU
Questa sezione descrive come abilitare l'integrazione dell'applicazione per SmarterU.

### <a name="to-enable-the-application-integration-for-smarteru-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per SmarterU, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-smarteru-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-smarteru-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-smarteru-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **SmarterU**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-smarteru-tutorial/IC777321.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **SmarterU**, e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![SmarterU](./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a SmarterU tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **SmarterU** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a SmarterU** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configurare l'accesso Single Sign-On")

3. Nella pagina **Configura accesso Single Sign-On in SmarterU** fare clic su **Scarica metadati** per scaricare i metadati e salvare il file di dati localmente come **c:\\SmarterUMetaData.cer**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configurare l'accesso Single Sign-On")

4. In un'altra finestra del browser Web accedere al sito aziendale di SmarterU come amministratore.

5. Nella barra degli strumenti in alto fare clic su **Impostazioni account**.
   
    ![Impostazioni account](./media/active-directory-saas-smarteru-tutorial/IC777326.png "Impostazioni account")

6. Nella pagina di configurazione dell’account, eseguire la procedura seguente:
   
    ![Autorizzazione esterna](./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorizzazione esterna")
 
    a. Selezionare **Attiva autorizzazione esterna**.
   
    b. Nella sezione **Controllo accesso master** selezionare la scheda **SmarterU**.
   
    c. Nella sezione **Accesso predefinito utente** selezionare la scheda **SmarterU**.
   
    d. Selezionare **Attiva Okta**.
   
    e. Copiare il contenuto del file dei metadati scaricato e incollarlo nella casella di testo **Metadati Okta** .

    f. Fare clic su **Save**.

7. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configurare l'accesso Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a SmarterU, è necessario eseguirne il provisioning in SmarterU.  
Nel caso di SmarterU, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al tenant **SmarterU** .

2. Andare a **Utenti**.

3. Nella sezione degli utenti, eseguire la procedura seguente:
   
    ![Nuovo utente](./media/active-directory-saas-smarteru-tutorial/IC777329.png "Nuovo utente")
   
    a. Fare clic su **+Utente**.
   
    b. Digitare i valori degli attributi correlati dell'account utente Azure AD nelle caselle di testo seguenti: **Posta elettronica principale**, **ID dipendente**, **Password**, **Verifica password**, **Nome**, **Cognome**.
   
    c. Fare clic su **Attivo**.
   
    d. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento di creazione di account utentedi SmarterU o API fornita da SmarterU per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-smarteru-perform-the-following-steps"></a>Per assegnare gli utenti a SmarterU, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **SmarterU** fare clic su **Assign users** (Assegna utenti).
   
    ![Assegnare utenti](./media/active-directory-saas-smarteru-tutorial/IC777330.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-smarteru-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


