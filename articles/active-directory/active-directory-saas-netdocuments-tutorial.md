---
title: 'Esercitazione: Integrazione di Azure Active Directory con NetDocuments | Documentazione Microsoft'
description: Informazioni su come utilizzare NetDocuments con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bd503bb141b5686f149c5fb46ba069db070d5fae
ms.openlocfilehash: 99dbb3deefb066b619b839aa709fe5e9b804eb27


---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Esercitazione: Integrazione di Azure Active Directory con NetDocuments
In questa esercitazione viene illustrata l'integrazione di Azure e NetDocuments. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant NetDocuments

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a NetDocuments saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di NetDocuments (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per NetDocuments
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Scenario")

## <a name="enabling-the-application-integration-for-netdocuments"></a>Abilitazione dell'integrazione dell'applicazione per NetDocuments
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per NetDocuments.

**Per abilitare l'integrazione dell'applicazione per NetDocuments, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **NetDocuments**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **NetDocuments**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![NetDocuments](./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a NetDocuments tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  

La configurazione dell'accesso Single Sign-On per NetDocuments richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere [Procedura: recuperare l'identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **NetDocuments** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a NetDocuments** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** seguire questa procedura:
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configurare l'URL dell'app")
   
   1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione NetDocuments, ad esempio "*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*".
   2. Nella casella di testo **URL di risposta NetDocuments** digitare lo stesso valore immesso nella casella di testo **URL di accesso**.  
      
      > [!NOTE]
      > È possibile trovare il valore corretto alla fine della finestra di dialogo **Identità federativa** (vedere la schermata per il passaggio 9).
      > 
      
   3. Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in NetDocuments** fare clic su **Scarica certificato** per scaricare il certificato e salvare il file del certificato localmente nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di NetDocuments come amministratore.
6. Passare alla pagina **Admin**.
7. Fare clic su **Aggiungi e rimuovi utenti e gruppi**.
   
   ![Repository](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repository")
8. Fare clic su **Configura opzioni di autenticazione avanzata**.
   
   ![Configurare opzioni di autenticazione avanzata](./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Configurare opzioni di autenticazione avanzata")
9. Nella finestra di dialogo della **identità federata** eseguire la procedura seguente:
   
   ![Identità federata](./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Identità federata")
   
   1. Come **Federated identity server type** (Tipo di server identità federata) selezionare **Active Directory Federation Services**.
   2. Per caricare il file di metadati scaricato fare clic su **Scegli file**.
   3. Fare clic su **OK**.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configurare l'accesso Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a NetDocuments, è necessario eseguirne il provisioning in NetDocuments. Nel caso di NetDocuments, il provisioning è un’attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di **NetDocuments** come amministratore.
2. Nel menu in alto fare clic su **Admin**.
   
   ![Amministratore](./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Amministratore")
3. Fare clic su **Aggiungi e rimuovi utenti e gruppi**.
   
   ![Repository](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repository")
4. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica di un account valido di Azure Active Directory di cui eseguire il provisioning, quindi fare clic su **Add User** (Aggiungi utente).
   
   ![Indirizzo di posta elettronica](./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Indirizzo di posta elettronica")
   
   > [!NOTE]
   > Il titolare dell'account di Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
   > 
   > 

> [!NOTE]
> È possibile utilizzare qualsiasi altro strumento di creazione di account utente di NetDocuments o le API fornite da NetDocuments per eseguire il provisioning degli account utente di AAD.
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a NetDocuments, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **NetDocuments ** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Feb17_HO1-->


