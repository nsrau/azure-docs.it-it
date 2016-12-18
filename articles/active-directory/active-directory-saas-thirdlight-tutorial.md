---
title: 'Esercitazione: Integrazione di Azure Active Directory con Thirdlight | Documentazione Microsoft'
description: Informazioni su come usare Thirdlight con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c7b3a782b41daab459a374e5356babced7260af1


---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Esercitazione: Integrazione di Azure Active Directory con Thirdlight
In questa esercitazione viene descritta l'integrazione di Azure e Thirdlight.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Thirdlight abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Thirdlight saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Thirdlight (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Thirdlight
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Scenario")

## <a name="enabling-the-application-integration-for-thirdlight"></a>Abilitazione dell'integrazione dell'applicazione per Thirdlight
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Thirdlight.

### <a name="to-enable-the-application-integration-for-thirdlight-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Thirdlight, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Thirdlight**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Thirdlight**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Thirdlight](./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Thirdlight tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per Thirdlight richiede di recuperare un valore di identificazione personale da un certificato.  
Se non si ha familiarità con questa procedura, vedere [Procedura: recuperare l'identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Thirdlight** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano alla pagina Thirdlight** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configure Single Sign-On")
3. Nella casella di testo **URL di accesso Thirdlight** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Thirdlight, ad esempio "*http://azuresso2.thirdlight.com/*", e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Thirdlight** fare clic su **Scarica metadati** per scaricare i metadati, quindi salvare il file di metadati localmente sul computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Thirdlight come amministratore.
6. Passare a **Configuration (Configurazione) \> System Administration (Amministrazione sistema)** e quindi fare clic su **SAML2**.
   
   ![System Administration](./media/active-directory-saas-thirdlight-tutorial/IC805843.png "System Administration")
7. Nella sezione di configurazione di SAML2, eseguire la procedura seguente:
   
   ![Single Sign-On SAML](./media/active-directory-saas-thirdlight-tutorial/IC805844.png "SAML Single Sign-On")
   
   1. Selezionare **Attiva Single Sign-On SAML2**.
   2. In **Source for IdP Metadata** (Origine metadati IdP) selezionare **Load IdP Metadata from XML** (Carica metadati IdP da XML).
   3. Aprire il file dei metadati scaricato, copiare il contenuto e incollarlo nella casella di testo **XML metadati IDP** .
   4. Fare clic su **Salva impostazioni SAML2**.
8. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a Thirdlight, è necessario eseguirne il provisioning in Thirdlight.  
Nel caso di Thirdlight, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, seguire questa procedura:
1. Accedere al sito aziendale di **Thirdlight** come amministratore.
2. Andare alla scheda **Utenti** .
3. Selezionare **Utenti e gruppi**.
4. Fare clic sul pulsante **Aggiungi nuovo utente** .
5. Immettere **nome utente, nome o descrizione, posta elettronica, scegliere un set predefinito o un gruppo di nuovi membri** di un account valido di AAD di cui si desidera eseguire il provisioning.
6. Fare clic su **Crea**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Thirdlight per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-thirdlight-perform-the-following-steps"></a>Per assegnare gli utenti a Thirdlight, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Thirdlight ** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


