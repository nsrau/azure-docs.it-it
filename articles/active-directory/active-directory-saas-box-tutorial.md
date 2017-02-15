---
title: 'Esercitazione: Integrazione di Azure Active Directory con Box | Documentazione Microsoft'
description: Informazioni su come usare Box con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 5f3517f8-30f2-4be7-9e47-43d702701797
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c8254f41006c36509af3626cb14825602e6a4adb


---
# <a name="tutorial-azure-active-directory-integration-with-box"></a>Esercitazione: Integrazione di Azure Active Directory con Box
Questa esercitazione descrive l'integrazione di Azure e Box.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di test in Box

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Box potranno eseguire l'accesso Single Sign-On all'applicazione nel sito aziendale Box (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Box
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utenti e gruppi
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-box-tutorial/IC769537.png "Scenario")

## <a name="enabling-the-application-integration-for-box"></a>Abilitazione dell'integrazione dell'applicazione per Box
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Box.

### <a name="to-enable-the-application-integration-for-box-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Box, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-box-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-box-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-box-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Box**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-box-tutorial/IC701023.png "Application gallery")
7. Nel riquadro dei risultati selezionare **box** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Box tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di caricare metadati in Box.com.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Box** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-box-tutorial/IC769538.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Box** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-box-tutorial/IC769539.png "Configure single sign-on")
3. Nella casella di testo **URL tenant di Box** della pagina **Configura URL app** digitare l'URL del tenant Box (ad esempio: https://<mydomainname>.box.com) e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-box-tutorial/IC669826.png "Configure app URL")
4. Nella pagina **Configura accesso Single Sign-On in Box** fare clic su **Scarica metadati** per scaricare i metadati e quindi salvare il file di dati in locale nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-box-tutorial/IC669824.png "Configure single sign-on")
5. Inoltrare il file dei metadati al team di supporto di Box. La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto.
6. Selezionare la conferma della configurazione Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-box-tutorial/IC769540.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in Box.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Box** nel portale di Azure classico fare clic su **Configura provisioning utenti** per aprire la finestra di dialogo **Configura provisioning utenti**. 
   
    ![Abilita provisioning utenti automatico](./media/active-directory-saas-box-tutorial/IC769541.png "Enable automatic user provisioning")
2. Nella finestra di dialogo **Abilita provisioning utenti in Box** fare clic su **Abilita provisioning utenti**. 
   
    ![Abilita provisioning utenti automatico](./media/active-directory-saas-box-tutorial/IC769544.png "Enable automatic user provisioning")
3. Nella pagina **Log in to grant access to Box** (Accedere per concedere l'accesso a Box) specificare le credenziali richieste e fare clic su **Authorize** (Autorizza). 
   
    ![Abilita provisioning utenti automatico](./media/active-directory-saas-box-tutorial/IC769546.png "Enable automatic user provisioning")
4. Fare clic su **Grant access to Box** (Concedi l'accesso a Box) per autorizzare l'operazione e tornare al portale di Azure classico. 
   
    ![Abilita provisioning utenti automatico](./media/active-directory-saas-box-tutorial/IC769549.png "Enable automatic user provisioning")
5. Nella pagina **Opzioni di provisioning**, le caselle di controllo **Tipi di oggetto per il provisioning** consentono di scegliere se eseguire anche il provisioning degli oggetti gruppo in Box oltre a quello degli oggetti utente.  Per altre informazioni, vedere "Assegnazione di utenti e gruppi" di seguito.
6. Per completare la configurazione fare clic sul pulsante Completa. 
   
    ![Abilita provisioning utenti automatico](./media/active-directory-saas-box-tutorial/IC769551.png "Enable automatic user provisioning")

## <a name="assigning-a-test-user"></a>Assegnazione di un utente test
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-box-perform-the-following-steps"></a>Per assegnare gli utenti a Box, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Box ** fare clic su **Assegna utenti**. 
   
    ![Assegna utenti](./media/active-directory-saas-box-tutorial/IC769552.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione. 
   
   ![Sì](./media/active-directory-saas-box-tutorial/IC767830.png "Yes")

È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con Box.

Come primo passaggio di verifica, è possibile controllare lo stato del provisioning facendo clic su Dashboard nella pagina di integrazione dell'applicazione Box del portale di Azure classico.

![Dashboard](./media/active-directory-saas-box-tutorial/IC769553.png "Dashboard")

Un ciclo di provisioning utenti completato correttamente è indicato da uno stato correlato:

![Integration status](./media/active-directory-saas-box-tutorial/IC769555.png "Integration status")

Nel tenant Box, gli utenti sincronizzati sono visualizzati nella sezione **Managed Users** (Utenti gestiti) di **Admin Console** (Console di amministrazione).

![Integration status](./media/active-directory-saas-box-tutorial/IC769556.png "Integration status")

## <a name="assigning-users-and-groups"></a>Assegnazione di utenti e gruppi
La scheda **Box > Utenti e gruppi** nel portale di Azure classico consente di specificare gli utenti e i gruppi cui concedere l'accesso a Box. L'assegnazione di un utente o gruppo causa quanto segue:

* Azure AD consente all'utente, assegnato tramite assegnazione diretta o appartenenza a un gruppo, di eseguire l'autenticazione in Box. Se l'utente non è assegnato, Azure AD non consentirà all'utente di eseguire l'accesso a Box e restituirà un errore nella pagina di accesso di Azure AD.
* Un riquadro dell'app Box viene aggiunto alla [schermata di avvio delle applicazioni](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)dell'utente.
* Se il provisioning automatico è abilitato, gli utenti e/o i gruppi assegnati vengono aggiunti alla coda di provisioning per l'esecuzione automatica del provisioning.
  
  * Se è stata selezionata l'esecuzione del provisioning solo per gli oggetti utente, tutti gli utenti assegnati direttamente e tutti gli utenti che appartengono ai gruppi assegnati vengono aggiunti alla coda di provisioning. 
  * Se è stata selezionata l'esecuzione del provisioning per gli oggetti gruppo, viene eseguito il provisioning in Box di tutti gli oggetti gruppo assegnati e di tutti gli utenti che appartengono a tali gruppi. Le appartenenze utente e gruppo vengono mantenute dopo la scrittura in Box.

La scheda **Attributi > Single Sign-On** consente di configurare gli attributi utente o le attestazioni presentate a Box durante l'autenticazione SAML. La scheda **Attributi > Provisioning** permette di configurare il flusso degli attributi utente e gruppo da Azure AD a Box durante le operazioni di provisioning. Per altre informazioni, vedere le risorse riportate di seguito.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Personalizzazione delle attestazioni rilasciate nel token SAML](active-directory-saml-claims-customization.md)
* [Provisioning: Personalizzare i mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md)
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


