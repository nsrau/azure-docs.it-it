---
title: 'Esercitazione: Integrazione di Azure Active Directory con Benefitsolver | Microsoft Docs'
description: Informazioni su come usare Benefitsolver con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: cf4529b1-3fb6-4475-82b7-2ceedcb70b3c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eb7dce1c0664e34c4946bb08f5b412e1e01c715d


---
# <a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>Esercitazione: Integrazione di Azure Active Directory con Benefitsolver
Questa esercitazione descrive l'integrazione di Azure e Benefitsolver.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Benefitsolver abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Benefitsolver potranno eseguire l'accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Benefitsolver
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Scenario")

## <a name="enabling-the-application-integration-for-benefitsolver"></a>Abilitazione dell'integrazione dell'applicazione per Benefitsolver
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Benefitsolver.

### <a name="to-enable-the-application-integration-for-benefitsolver-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Benefitsolver, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Benefitsolver**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Benefitsolver** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Benefitssolver](./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Benefitsolver tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
L'applicazione Benefitsolver prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione di **Attributi token SAML**.  
La schermata seguente illustra un esempio relativo a questa operazione.

![Attributi](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributi")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Benefitsolver** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Benefitsolver** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
   ![Configurare le impostazioni dell'app](./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Configurare le impostazioni dell'app")
   
   1. Nella casella di testo **URL di accesso** digitare **http://azure.benefitsolver.com**.
   2. Nella casella di testo **URL di risposta** digitare **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**.  
   3. Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in Benefitsolver** fare clic su **Download metadati** per scaricare il file di metadati e quindi salvarlo nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configurare l'accesso Single Sign-On")
5. Inviare il file di metadati scaricato al team di supporto di Benefitsolver.
   
   > [!NOTE]
   > Il team di supporto di Benefitsolver si occuperà dell'effettiva configurazione dell'accesso Single Sign-On.
   > Una volta completata l'abilitazione dell'accesso Single Sign-On per la sottoscrizione, si riceverà una notifica.
   > 
   > 
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configurare l'accesso Single Sign-On")
7. Nel menu in alto fare clic su **Attributi** to open the **SAML Token Attributi** .
   
   ![Attributi](./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Attributi")
8. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:
   
   ![Attributi](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributi")
   
   | Nome attributo | Valore attributo |
   | --- | --- |
   | ClientID |È necessario ottenere questo valore dal team di supporto di Benefitsolver. |
   | ClientKey |È necessario ottenere questo valore dal team di supporto di Benefitsolver. |
   | LogoutURL |È necessario ottenere questo valore dal team di supporto di Benefitsolver. |
   | EmployeeID |È necessario ottenere questo valore dal team di supporto di Benefitsolver. |
   
   1. Per ogni riga di dati nella tabella precedente, fare clic su **aggiungi attributo utente**.
   2. Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
   3. Nella casella di testo **Valore attributo** , selezionare il valore dell'attributo indicato per la riga.
   4. Fare clic su **Completa**.
9. Fare clic su **Applica modifiche**.

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a Benefitsolver, è necessario eseguirne il provisioning in Benefitsolver.  
Nel caso di Benefitsolver, i dati dei dipendenti si trovano nell'applicazione popolata tramite un file di censimento del sistema HRIS, in genere durante la notte.  

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Benefitsolver per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-benefitsolver-perform-the-following-steps"></a>Per assegnare gli utenti a Benefitsolver, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Benefitsolver** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Jan17_HO1-->


