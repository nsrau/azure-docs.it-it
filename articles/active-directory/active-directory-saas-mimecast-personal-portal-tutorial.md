---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mimecast Personal Portal | Documentazione Microsoft'
description: Informazioni su come utilizzare Mimecast Personal Portal con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: eb0fef7143e85e1e9ab8b586e1f9944340c76bf3


---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Esercitazione: Integrazione di Azure Active Directory con Mimecast Personal Portal
In questa esercitazione viene illustrata l'integrazione di Azure e Mimecast Personal Portal.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Mimecast Personal Portal abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Mimecast Personal Portal saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Mimecast Personal Portal (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Mimecast Personal Portal
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "Scenario")

## <a name="enabling-the-application-integration-for-mimecast-personal-portal"></a>Abilitazione dell'integrazione dell'applicazione per Mimecast Personal Portal
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Mimecast Personal Portal.

### <a name="to-enable-the-application-integration-for-mimecast-personal-portal-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Mimecast Personal Portal, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Mimecast Personal Portal**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Mimecast Personal Portal**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Mimecast Personal Portal")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Mimecast Personal Portal tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Mimecast Personal Portal** nel portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Mimecast Personal Portal** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Configure Single Sign-On")
3. Nella pagina **Configura URL app**, nella casella di testo **URL di accesso a Mimecast Personal Portal**, digitare l'URL utilizzato dagli utenti per accedere all'applicazione Mimecast Personal Portal (ad esempio, "https://webmail-uk.mimecast.com" o "https://webmail-us.mimecast.com"), quindi fare clic su **Avanti**.
   
   > [!NOTE]
   > L’URL di accesso è specifico dell’area geografica.
   > 
   > 
   
   ![Configura URL app](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Mimecast Personal Portal**, per scaricare il certificato fare clic su **Download certificato**, quindi salvare il file di certificato localmente nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere a Mimecast Personal Portal come amministratore.
6. Passare a **Services \> Application**.
   
   ![Applicazioni](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "Applications")
7. Fare clic su **Authentication Profiles**.
   
   ![Authentication Profiles](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "Authentication Profiles")
8. Fare clic su **New Authentication Profile**.
   
   ![Nuovo profilo di autenticazione](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "New Authentication Profil")
9. Nella sezione **Authentication Profile** , eseguire la procedura seguente:
   
   ![Profilo di autenticazione](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "Authentication Profil")
   
   1. Nella casella di testo **Description** digitare un nome per la configurazione.
   2. Selezionare **Enforce SAML Authentication for Mimecast Personal Portal**.
   3. Come **Provider** selezionare **Azure Active Directory**.
   4. Nella pagina **Configura accesso Single Sign-On in Mimecast Personal Portal** del portale di Azure classico copiare il valore **URL autorità di certificazione** e incollarlo nella casella di testo **Issuer URL** (URL autorità di certificazione).
   5. Nella pagina **Configura accesso Single Sign-On in Mimecast Personal Portal** del portale di Azure classico copiare il valore **URL accesso remoto** e incollarlo nella casella di testo **Login URL** (URL di accesso).
   6. Nella pagina **Configura accesso Single Sign-On in Mimecast Personal Portal** del portale di Azure classico copiare il valore **URL accesso remoto** e incollarlo nella casella di testo **Logout URL** (URL di disconnessione).  
      
      > [!NOTE]
      > Il valore dell’URL di accesso e il valore dell'URL disconnessione per l’accesso Single Sign-On in Mimecast Personal Portal sono identici.
      > 
      > 
   7. Creare un file **con codifica Base 64** dal certificato scaricato.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).
      > 
      > 
   8. Aprire il certificato con codifica Base 64 nel Blocco note, rimuovere la prima riga ("*--*") e l'ultima riga ("*--*"), copiare il contenuto rimanente negli Appunti e incollarlo nella casella di testo **Identity Provider Certificate (Metadata)** (Certificato del provider di identità (Metadati)).
   9. Selezionare **Allow Single Sign On**.
   10. Fare clic su **Salva**.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Mimecast Personal Portal, è necessario eseguirne il provisioning in Mimecast Personal Portal.  
Nel caso di Mimecast Personal Portal, il provisioning è un’attività manuale.

Per poter creare gli utenti è necessario registrare un dominio.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere a **Mimecast Personal Portal** come amministratore.
2. Accedere a **Directories \> Internal**.
   
   ![Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "Directories")
3. Fare clic su **Register New Domain**.
   
   ![Register New Domain](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "Register New Domain")
4. Dopo aver creato il nuovo dominio, fare clic su **New Address**.
   
   ![New Address](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "New Address")
5. Nella finestra di dialogo del nuovo indirizzo, seguire questa procedura:
   
   ![Salva](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "Save")
   
   1. Nelle caselle di testo corrispondenti digitare gli attributi **indirizzo di posta elettronica**, **nome globale**, **password** e **conferma password** di un account ADD valido di cui si intende eseguire il provisioning.
   2. Fare clic su **Salva**.

> [!NOTE]
> È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Mimecast Personal Portal o le API fornite da Mimecast Personal Portal per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-mimecast-personal-portal-perform-the-following-steps"></a>Per assegnare gli utenti a Mimecast Personal Portal, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Mimecast Personal Portal** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


