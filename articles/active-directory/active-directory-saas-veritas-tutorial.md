---
title: 'Esercitazione: Integrazione di Azure Active Directory con Veritas Enterprise Vault.cloud SSO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Veritas Enterprise Vault.cloud SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 1f8c7fd97021f320a23bc78466a7b61f2d7e513e
ms.contentlocale: it-it
ms.lasthandoff: 07/27/2017

---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Esercitazione: Integrazione di Azure Active Directory con Veritas Enterprise Vault.cloud SSO

Questa esercitazione descrive come integrare Veritas Enterprise Vault.cloud SSO con Azure Active Directory (Azure AD).

L'integrazione di Veritas Enterprise Vault.cloud SSO con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Veritas Enterprise Vault.cloud SSO
- È possibile abilitare gli utenti per l'accesso automatico a Veritas Enterprise Vault.cloud SSO (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Veritas Enterprise Vault.cloud SSO, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Veritas Enterprise Vault.cloud abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Veritas Enterprise Vault.cloud SSO dalla galleria
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Aggiunta di Veritas Enterprise Vault.cloud SSO dalla galleria
Per configurare l'integrazione di Veritas Enterprise Vault.cloud SSO in Azure AD, è necessario aggiungere Veritas Enterprise Vault.cloud SSO dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Veritas Enterprise Vault.cloud SSO, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Veritas Enterprise Vault.cloud SSO**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_search.png)

5. Nel pannello dei risultati selezionare **Veritas Enterprise Vault.cloud SSO** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Veritas Enterprise Vault.cloud SSO usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Veritas Enterprise Vault.cloud SSO che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Veritas Enterprise Vault.cloud SSO.

Per stabilire la relazione di collegamento, in Veritas Enterprise Vault.cloud SSO assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Veritas Enterprise Vault.cloud SSO, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Veritas Enterprise Vault.cloud SSO](#creating-a-veritas-enterprise-vaultcloud-sso-test-user)**: per avere una controparte di Britta Simon in Veritas Enterprise Vault.cloud SSO collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Veritas Enterprise Vault.cloud SSO.

**Per configurare l'accesso Single Sign-On di Azure AD con Veritas Enterprise Vault.cloud SSO, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Veritas Enterprise Vault.cloud SSO** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_samlbase.png)

3. Nella sezione **URL e dominio Veritas Enterprise Vault.cloud SSO** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`
    
    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html). 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-veritas-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Veritas Enterprise Vault.cloud SSO** fare clic su **Configura Veritas Enterprise Vault.cloud SSO** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_configure.png) 

7. Per configurare l'accesso Single Sign-On sul lato **Veritas Enterprise Vault.cloud SSO**, è necessario inviare il **certificato (Base64)** e l'**URL del servizio Single Sign-On SAML** scaricati al [team di supporto di Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html).

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Creazione di un utente test di Veritas Enterprise Vault.cloud SSO

In questa sezione viene creato un utente di nome Britta Simon in Enterprise Vault.cloud SSO. Collaborare con il [team di supporto di Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) per aggiungere gli utenti nella piattaforma Veritas Enterprise Vault.cloud SSO. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Veritas Enterprise Vault.cloud SSO.

![Assegna utente][200] 

**Per assegnare Britta Simon a Veritas Enterprise Vault.cloud SSO, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Veritas Enterprise Vault.cloud SSO**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Veritas Enterprise Vault.cloud SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Veritas Enterprise Vault.cloud SSO.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_203.png


