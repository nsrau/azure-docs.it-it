---
title: 'Esercitazione: Integrazione di Azure Active Directory con TINFOIL SECURITY | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 614e4de3335574f4b56c7d641af4fcfafdb17d12
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Esercitazione: Integrazione di Azure Active Directory con TINFOIL SECURITY

Questa esercitazione descrive come integrare TINFOIL SECURITY con Azure Active Directory (Azure AD).

L'integrazione di TINFOIL SECURITY con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a TINFOIL SECURITY
- È possibile abilitare gli utenti per l'accesso automatico a TINFOIL SECURITY (Single Sign-On) con gli account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TINFOIL SECURITY, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di TINFOIL SECURITY abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiungere TINFOIL SECURITY dalla raccolta
2. Configurare e testare l'accesso Single Sign-On di Azure AD

## <a name="add-tinfoil-security-from-the-gallery"></a>Aggiungere TINFOIL SECURITY dalla raccolta
Per configurare l'integrazione di TINFOIL SECURITY in Azure AD, è necessario aggiungere TINFOIL SECURITY dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere TINFOIL SECURITY dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **TINFOIL SECURITY**, selezionare **TINFOIL SECURITY** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![TINFOIL SECURITY dalla raccolta](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TINFOIL SECURITY con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di TINFOIL SECURITY corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TINFOIL SECURITY.

Per stabilire la relazione di collegamento, in TINFOIL SECURITY assegnare il valore di **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con TINFOIL SECURITY, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di TINFOIL SECURITY](#create-a-tinfoil-security-test-user)**: per avere una controparte di Britta Simon in TINFOIL SECURITY collegata alla relativa rappresentazione in Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione TINFOIL SECURITY.

**Per configurare l'accesso Single Sign-On di Azure AD con TINFOIL SECURITY, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **TINFOIL SECURITY** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Accesso basato su SAML](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

3. Nella sezione **URL e dominio TINFOIL SECURITY** l'utente non deve eseguire alcuna operazione poiché l'app è già integrata in Azure.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


4. Nella sezione **Certificato di firma SAML** copiare il valore **IDENTIFICAZIONE PERSONALE**.

    ![Sezione Certificato di firma SAML](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

5. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:
    
    ![Attributi](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "Attributi")
    
    | Nome attributo    |   Valore attributo |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
    a. Fare clic su **Aggiungi attributo utente**.
    
    ![ADD Attribute](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "Attributes")
    
    ![ADD Attribute](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "Attributes")
    
    b. Nella casella di testo **Nome attributo**, digitare **accountid**.
    
    c. Nella casella di testo **Valore attributo** incollare il valore di ID account ottenuto più avanti nell'esercitazione.
    
    d. Fare clic su **OK**.    

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_400.png)

7. Nella sezione **Configurazione di TINFOIL SECURITY** fare clic su **Configura TINFOIL SECURITY** per visualizzare la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configurazione di TINFOIL SECURITY](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

8. In un'altra finestra del Web browser accedere al sito aziendale TINFOIL SECURITY come amministratore.

9. Nel barra degli strumenti in alto fare clic su **Account**.
   
    ![Dashboard](./media/active-directory-saas-tinfoil-security-tutorial/ic798971.png "Dashboard")

10. Fare clic su **Security**.
   
    ![Sicurezza](./media/active-directory-saas-tinfoil-security-tutorial/ic798972.png "Sicurezza")

11. Nella pagina di configurazione **Single Sign-On** eseguire la procedura seguente:
   
    ![Single Sign-On](./media/active-directory-saas-tinfoil-security-tutorial/ic798973.png "Single Sign-On")
   
    a. Selezionare **Enable SAML**.
   
    b. Fare clic su **Configurazione manuale**.
   
    c. Nella casella di testo **SAML Post URL** (URL post SAML) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure
   
    d. Nella casella di testo **SAML Certificate Fingerprint** (Impronta digitale certificato SAML) incollare il valore di **Identificazione personale** copiato dalla sezione **Certificato di firma SAML**.
  
    e. Copiare il valore **Your Account ID** (ID account) e incollarlo nella casella di testo **Valore attributo** nella sezione **Aggiungi attributo** nel portale di Azure.
   
    f. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Utenti e gruppi -> Tutti gli utenti ](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Utente](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Creare un utente test di TINFOIL SECURITY

Per consentire agli utenti di Azure AD di accedere a TINFOIL SECURITY, è necessario eseguirne il provisioning in TINFOIL SECURITY. In TINFOIL SECURITY il provisioning è un'attività manuale.

**Per eseguire il provisioning di un utente, seguire questa procedura:**

1. Se l'utente fa parte di un account aziendale, è necessario [contattare il team di supporto TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) per creare l'account utente.

2. Se l'utente è un normale utente SaaS TINFOIL SECURITY, può aggiungere un collaboratore a uno qualsiasi dei siti dell'utente. Viene attivato un processo per l'invio all'indirizzo di posta elettronica specificato di un invito a creare un nuovo account utente di TINFOIL SECURITY.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente offerta da TINFOIL SECURITY per eseguire il provisioning degli account utente di Azure AD.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a TINFOIL SECURITY.

![Assegna utente][200] 

**Per assegnare Britta Simon a TINFOIL SECURITY, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **TINFOIL SECURITY**.

    ![selezionare TINFOIL SECURITY](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro TINFOIL SECURITY nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione TINFOIL SECURITY. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_203.png

