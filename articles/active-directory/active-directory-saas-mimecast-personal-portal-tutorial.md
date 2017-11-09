---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mimecast Personal Portal | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: bf46da35a55608d7e4656c9dd3ad9d5f2253e225
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Esercitazione: Integrazione di Azure Active Directory con Mimecast Personal Portal

Questa esercitazione descrive come integrare Mimecast Personal Portal con Azure Active Directory (Azure AD).

L'integrazione di Mimecast Personal Portal con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Mimecast Personal Portal
- È possibile abilitare gli utenti per l'accesso automatico a Mimecast Personal Portal (Single Sign-On) con gli account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Mimecast Personal Portal, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Mimecast Personal Portal abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Mimecast Personal Portal dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Aggiunta di Mimecast Personal Portal dalla raccolta
Per configurare l'integrazione di Mimecast Personal Portal in Azure AD, è necessario aggiungere Mimecast Personal Portal dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Mimecast Personal Portal dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Mimecast Personal Portal**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_search.png)

5. Nel pannello dei risultati selezionare **Mimecast Personal Portal** e quindi fare clic su **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Mimecast Personal Portal con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di Mimecast Personal Portal corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Mimecast Personal Portal.

Per stabilire la relazione di collegamento, in Mimecast Personal Portal assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Mimecast Personal Portal, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Mimecast Personal Portal](#creating-a-mimecast-personal-portal-test-user)** : per avere una controparte di Britta Simon in Mimecast Personal Portal collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Mimecast Personal Portal.

**Per configurare l'accesso Single Sign-On di Azure AD con Mimecast Personal Portal, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Mimecast Personal Portal** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. Nella sezione **URL e dominio Mimecast Personal Portal** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il criterio seguente: 
    | |     
    | ----------------------------------------|
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|
    | |
   
    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente:

    | |     
    | --- |
    | `https://webmail-us.mimecast.com/sso/<companyname>`|
    | `https://webmail-uk.mimecast.com/sso/<companyname>`|    
    | `https://webmail-za.mimecast.com/sso/<companyname>`|
    | `https://webmail.mimecast-offshore.com/sso/<companyname>`|
    ||                                                 
    
    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Mimecast Personal Portal](https://www.mimecast.com/customer-success/technical-support/). 
 


4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Mimecast Personal Portal** fare clic su **Configura Mimecast Personal Portal** per visualizzare la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. In un'altra finestra del Web browser accedere a Mimecast Personal Portal come amministratore.

8. Passare a **Services (Servizi) \> Applications (Applicazioni)**.
   
    ![Applicazioni](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794998.png "Applicazioni")

9. Fare clic su **Authentication Profiles**.
   
    ![Profili di autenticazione](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794999.png "Profili di autenticazione")

10. Fare clic su **New Authentication Profile**.
   
    ![New Authentication Profile](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795000.png "New Authentication Profile")

11. Nella sezione **Authentication Profile** , eseguire la procedura seguente:
   
    ![Profilo di autenticazione](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795001.png "Profilo di autenticazione")
   
    a. Nella casella di testo **Description** digitare un nome per la configurazione.
   
    b. Selezionare **Enforce SAML Authentication for Mimecast Personal Portal**.
   
    c. Come **Provider** selezionare **Azure Active Directory**.
   
    d. Nella casella di testo **Issuer URL** (URL autorità emittente) incollare il valore dell'**ID entità SAML** copiato dal portale di Azure.
   
    e. Nella casella di testo **URL di accesso** incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.
   
    f. Nella casella di testo **Logout URL** (URL di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    g. Aprire nel Blocco note il certificato con codifica **Base 64** scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Identity Provider Certificate (Metadata)** (Certificato provider di identità (metadati)).

    h. Selezionare **Allow Single Sign On**.
   
    i. Fare clic su **Salva**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-mimecast-personal-portal-test-user"></a>Creazione di un utente test di Mimecast Personal Portal

Per consentire agli utenti di Azure AD di accedere a Mimecast Personal Portal, è necessario eseguirne il provisioning in Mimecast Personal Portal. Nel caso di Mimecast Personal Portal, il provisioning è un’attività manuale.

Per poter creare gli utenti è necessario registrare un dominio.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere a **Mimecast Personal Portal** come amministratore.

2. Accedere a **Directories \> Internal**.
   
    ![Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795003.png "Directory")

3. Fare clic su **Register New Domain**.
   
    ![Registra nuovo dominio](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795004.png "Registra nuovo dominio")

4. Dopo aver creato il nuovo dominio, fare clic su **New Address**.
   
    ![Nuovo indirizzo](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795005.png "Nuovo indirizzo")

5. Nella finestra di dialogo del nuovo indirizzo eseguire i passaggi seguenti per un account Azure AD valido di cui si vuole eseguire il provisioning:
   
    ![Salvare](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795006.png "Salvare")
   
    a. Nella casella di testo **Indirizzo di posta elettronica** digitare l'**indirizzo di posta elettronica** dell'utente **BrittaSimon@contoso.com**.
    
    b. Nella casella di testo **Global Name** (Nome globale) digitare il **nome utente** **BrittaSimon**.

    c. Nelle caselle di testo **Password** e **Conferma password** digitare la **password** dell'utente.
   
    b. Fare clic su **Salva**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerta da Mimecast Personal Portal per eseguire il provisioning degli account utente di Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Mimecast Personal Portal.

![Assegna utente][200] 

**Per assegnare Britta Simon a Mimecast Personal Portal, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Mimecast Personal Portal**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Mimecast Personal Portal nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Mimecast Personal Portal. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_203.png

