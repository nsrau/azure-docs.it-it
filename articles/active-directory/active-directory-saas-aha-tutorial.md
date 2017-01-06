---
title: 'Esercitazione: Integrazione di Azure Active Directory con Aha! | Documentazione Microsoft'
description: Informazioni su come usare Aha! con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f95aecd18d5231413678669d01d16387737e9db2


---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>Esercitazione: Integrazione di Azure Active Directory con Aha!
Questa esercitazione descrive l'integrazione di Azure e Aha!  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione Aha! abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati ad Aha! potranno accedere all'applicazione tramite il sito aziendale di Aha! (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Aha!
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-aha-tutorial/IC798944.png "Scenario")

## <a name="enabling-the-application-integration-for-aha"></a>Abilitazione dell'integrazione dell'applicazione per Aha!
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Aha!.

### <a name="to-enable-the-application-integration-for-aha-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Aha!, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-aha-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-aha-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-aha-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Aha!**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-aha-tutorial/IC798945.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Aha!** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione ad Aha! tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Aha!** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On **.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-aha-tutorial/IC798946.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Aha!** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-aha-tutorial/IC798947.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Aha! nella casella di testo **URL di accesso a Aha! URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Aha!, ad esempio "*https://company.aha.io/session/new*", e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-aha-tutorial/IC798948.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Aha!** scaricare il file di metadati, fare clic su **Scarica metadati** e quindi salvare il file di metadati localmente nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-aha-tutorial/IC798949.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Aha! come amministratore.
6. Nel menu in alto fare clic su **Impostazioni**.
   
   ![Impostazioni](./media/active-directory-saas-aha-tutorial/IC798950.png "Impostazioni")
7. Fare clic su **Account**.
   
   ![Profilo](./media/active-directory-saas-aha-tutorial/IC798951.png "Profilo")
8. Fare clic su **Sicurezza e single sign-on**.
   
   ![Sicurezza e Single Sign-On](./media/active-directory-saas-aha-tutorial/IC798952.png "Sicurezza e Single Sign-On")
9. Nella sezione **Single Sign-On** in **Identity Provider** (Provider di identità) selezionare **SAML2.0**.
   
   ![Sicurezza e Single Sign-On](./media/active-directory-saas-aha-tutorial/IC798953.png "Sicurezza e Single Sign-On")
10. Nella pagina di configurazione **Single Sign-On** eseguire la procedura seguente:
    
    ![Single Sign-On](./media/active-directory-saas-aha-tutorial/IC798954.png "Single Sign-On")
    
    1. Nella casella di testo **Name** digitare un nome per la configurazione.
    2. Per **Configure using** (Configura mediante) selezionare **Metadata File** (File di metadati).
    3. Per caricare il file di metadati scaricato, fare clic su **Browse**.
    4. Fare clic su **Aggiorna**.
11. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-aha-tutorial/IC798955.png "Configurare l'accesso Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere ad Aha!, è necessario eseguirne il provisioning in Aha!.  
Nel caso di Aha!, il provisioning è un'attività automatica.  
Non è necessario eseguire alcuna operazione.

Se necessario, gli utenti vengono creati automaticamente durante il primo tentativo di accesso Single Sign-On.

> [!NOTE]
> È possibile usare qualsiasi altro strumento di creazione di account utente Aha! o qualsiasi altra API disponibile in Aha! per eseguire il provisioning degli account utente AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-aha-perform-the-following-steps"></a>Per assegnare gli utenti ad Aha!, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Aha! **fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-aha-tutorial/IC798956.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-aha-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->


