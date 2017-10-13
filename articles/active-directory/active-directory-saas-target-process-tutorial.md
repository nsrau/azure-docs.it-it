---
title: 'Esercitazione: Integrazione di Azure Active Directory con TargetProcess | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: d15931a5d430252bbd9ae342e1f8fde1a539355b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Esercitazione: Integrazione di Azure Active Directory con TargetProcess

Questa esercitazione descrive come integrare TargetProcess con Azure Active Directory (Azure AD).

L'integrazione di TargetProcess con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a TargetProcess
- È possibile abilitare gli utenti per l'accesso automatico a TargetProcess (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TargetProcess, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di TargetProcess abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiungere TargetProcess dalla raccolta
2. Configurare e testare l'accesso Single Sign-On di Azure AD

## <a name="add-targetprocess-from-the-gallery"></a>Aggiungere TargetProcess dalla raccolta
Per configurare l'integrazione di TargetProcess in Azure AD, è necessario aggiungere TargetProcess dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere TargetProcess dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **TargetProcess**, selezionare **TargetProcess** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Aggiungere TargetProcess dalla raccolta](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TargetProcess Online usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di TargetProcess che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TargetProcess.

Per stabilire la relazione di collegamento, in TargetProcess assegnare il valore di **nome utente** in Azure AD come valore di **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con TargetProcess, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test per TargetProcess](#create-a-targetprocess-test-user)**: per avere una controparte di Britta Simon in TargetProcess collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione TargetProcess.

**Per configurare l'accesso Single Sign-On di Azure AD con TargetProcess, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **TargetProcess** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Accesso basato su SAML](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_samlbase.png)

3. Nella sezione **URL e dominio TargetProcess** seguire questa procedura:

    ![Sezione URL e dominio TargetProcess](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.tpondemand.com/`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.tpondemand.com/`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto client di TargetProcess](mailto:support@targetprocess.com). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Sezione Certificato di firma SAML](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva](./media/active-directory-saas-target-process-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di TargetProcess** fare clic su **Configura TargetProcess** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Sezione di configurazione di TargetProcess](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_configure.png) 

7. Accedere all'applicazione TargetProcess come amministratore.

8. Nel menu in alto fare clic su **Impostazione**.
   
    ![Configurazione](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_05.png)

9. Fare clic su **Impostazioni**.
   
    ![Impostazioni](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_06.png) 

10. Fare clic su **Single Sign-On**.
   
    ![fare clic su Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_07.png) 

11. Nella finestra di dialogo delle impostazioni Single Sign-On seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_08.png)
    
    a. Fare clic su **Abilita Single Sign-on**.
    
    b. Nella casella di testo **Sign on URL** (URL di accesso) incollare il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On) copiato dal portale di Azure.

    c. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificato** .
    
    d. Fare clic su **Abilita Provisioning JIT**.

    e. Fare clic su **Salva**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-target-process-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Per visualizzare l'elenco degli utenti](./media/active-directory-saas-target-process-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Pulsante Aggiungi](./media/active-directory-saas-target-process-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Sezione Utente](./media/active-directory-saas-target-process-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-targetprocess-test-user"></a>Creare un utente test per TargetProcess

Questa sezione descrive come creare un utente chiamato Britta Simon in TargetProcess.

TargetProcess supporta il provisioning JIT (just-in-time), È già stato abilitato in [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on).

Non è necessario alcun intervento dell'utente in questa sezione.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a TargetProcess.

![Assegna utente][200] 

**Per assegnare Britta Simon a TargetProcess, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Selezionare **TargetProcess**dall'elenco di applicazioni.

    ![TargetProcess nell'elenco di app](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro TargetProcess nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione TargetProcess. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_203.png

