---
title: 'Esercitazione: Integrazione di Azure Active Directory con Sciforma | Microsoft Docs'
description: Informazioni su come usare Sciforma con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: abbfb5ac-7687-4153-b263-8090102dae37
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: 166524147c050512a84c1c0340de93960d8b7632


---
# <a name="tutorial-azure-ad-integration-with-sciforma"></a>Esercitazione: Integrazione di Azure Active Directory con Sciforma
In questa esercitazione viene illustrata l'integrazione di Azure e Sciforma.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Sciforma

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Sciforma saranno in grado di eseguire l'accesso Single Sign-On all'applicazione tramite il sito aziendale di Sciforma (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Sciforma
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-sciforma-tutorial/IC777369.png "Scenario")

## <a name="enabling-the-application-integration-for-sciforma"></a>Abilitazione dell'integrazione dell'applicazione per Sciforma
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Sciforma.

### <a name="to-enable-the-application-integration-for-sciforma-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Sciforma, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-sciforma-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-sciforma-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-sciforma-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-sciforma-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **Sciforma**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-sciforma-tutorial/IC777370.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **Sciforma**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Sciforma](./media/active-directory-saas-sciforma-tutorial/IC777371.png "Sciforma")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Sciforma tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Sciforma** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sciforma-tutorial/IC777372.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Sciforma** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sciforma-tutorial/IC777373.png "Configurare l'accesso Single Sign-On")

3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL di accesso Sciforma** usando il modello seguente "*https://\<tenant-name\>.Sciforma.com*" e fare clic su **Avanti**.
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-sciforma-tutorial/IC777374.png "Configurare l'URL dell'app")

4. Nella pagina **Configura accesso Single Sign-On in Sciforma** fare clic su **Scarica metadati** per scaricare i metadati e salvare il file di dati localmente come **c:\\SciformaMetaData.xml**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sciforma-tutorial/IC777375.png "Configurare l'accesso Single Sign-On")

5. Inoltrare il file dei metadati al team di supporto di Sciforma. La configurazione dell'accesso Single Sign-On viene eseguita dal team di supporto.

6. Selezionare la conferma della configurazione Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sciforma-tutorial/IC777376.png "Configurare l'accesso Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in Sciforma.  
Quando un utente assegnato tenta di accedere a Sciforma usando il pannello di accesso, Sciforma controlla se l'utente esiste.  
Se l'account utente non è disponibile, viene creato automaticamente da Sciforma.

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-sciforma-perform-the-following-steps"></a>Per assegnare gli utenti a Sciforma, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **Sciforma** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-sciforma-tutorial/IC777377.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-sciforma-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Jan17_HO1-->


