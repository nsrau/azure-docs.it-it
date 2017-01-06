---
title: 'Esercitazione: Integrazione di Azure Active Directory con 15Five | Microsoft Docs'
description: Informazioni su come usare 15Five con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2fb301c2-7d7a-4046-8ee1-7dc9e7684806
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: ce98338e6b21eb35a17f0183f409dd54d1123bb9


---
# <a name="tutorial-azure-active-directory-integration-with-15five"></a>Esercitazione: Integrazione di Azure Active Directory con 15Five
Questa esercitazione descrive l'integrazione di Azure e 15Five. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di 15Five abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a 15Five potranno accedere all'applicazione tramite il sito aziendale di 15Five (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per 15Five
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-15five-tutorial/IC784667.png "Scenario")

## <a name="enabling-the-application-integration-for-15five"></a>Abilitazione dell'integrazione dell'applicazione per 15Five
Questa sezione descrive come abilitare l'integrazione dell'applicazione per 15Five.

### <a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per 15Five, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-15five-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-15five-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-15five-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **15Five**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-15five-tutorial/IC784668.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **15Five** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a 15Five tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **15Five** del Portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-15five-tutorial/IC784670.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a 15Five** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-15five-tutorial/IC784671.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **URL accesso 15Five** della pagina **Configura URL app** digitare l'URL usando il modello "*https://company.15Five.com*" quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-15five-tutorial/IC784672.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in 15Five** fare clic su **Download metadata (Scarica metadati)** e quindi inoltrare il file di metadati al team di supporto di 15Five.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-15five-tutorial/IC784673.png "Configurare l'accesso Single Sign-On")
   
   > [!NOTE]
   > L'accesso Single Sign-On deve essere abilitato dal team di supporto di 15Five.
   > 
   > 
5. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-15five-tutorial/IC784674.png "Configurare l'accesso Single Sign-On")
   

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a 15Five, è necessario eseguirne il provisioning in 15Five.  
Nel caso di 15Five, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **15Five** come amministratore.
2. Passare a **Gestisci azienda**.
   
   ![Gestire una società](./media/active-directory-saas-15five-tutorial/IC784675.png "Gestire una società")
3. Passare a **Persone \> Aggiungi persone**.
   
   ![Persone](./media/active-directory-saas-15five-tutorial/IC784676.png "Persone")
4. Nella sezione Add New Person seguire questa procedura:
   
   ![Aggiungere una nuova persona](./media/active-directory-saas-15five-tutorial/IC784677.png "Aggiungere una nuova persona")
   
   1. Digitare il **Nome**, il **Cognome**, il **Titolo**, l'**Indirizzo email** di un account utente Azure Active Directory valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Done**.
   
   > [!NOTE]
   > Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
   > 
   > 



## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-15five-perform-the-following-steps"></a>Per assegnare gli utenti a 15Five, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **15Five** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-15five-tutorial/IC784678.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-15five-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->


