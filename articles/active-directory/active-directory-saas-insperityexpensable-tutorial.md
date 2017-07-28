---
title: 'Esercitazione: Integrazione di Azure Active Directory con Insperity ExpensAble | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Insperity ExpensAble.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c579c453-580e-417d-8a5e-9b6b352795c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: b50e10be54b1fc413be10bee5b58631790629335
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-insperity-expensable"></a>Esercitazione: Integrazione di Azure Active Directory con Insperity ExpensAble

Questa esercitazione descrive come integrare Insperity ExpensAble con Azure Active Directory (Azure AD).

L'integrazione di Insperity ExpensAble con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Insperity ExpensAble
- È possibile abilitare gli utenti per l'accesso automatico a Insperity ExpensAble (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Insperity ExpensAble, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Insperity ExpensAble abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Insperity ExpensAble dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-insperity-expensable-from-the-gallery"></a>Aggiunta di Insperity ExpensAble dalla raccolta
Per configurare l'integrazione di Insperity ExpensAble in Azure AD, è necessario aggiungere Insperity ExpensAble dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Insperity ExpensAble dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Insperity ExpensAble**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_search.png)

5. Nel pannello dei risultati selezionare **Insperity ExpensAble** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Insperity ExpensAble mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Insperity ExpensAble corrisponde a un determinato utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Insperity ExpensAble.

Per stabilire la relazione di collegamento, in Insperity ExpensAble assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Insperity ExpensAble, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Insperity ExpensAble](#creating-an-insperity-expensable-test-user)**: per avere una controparte di Britta Simon in Insperity ExpensAble collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Insperity ExpensAble.

**Per configurare Single Sign-On di Azure AD con Insperity ExpensAble, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Insperity ExpensAble** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_samlbase.png)

3. Nella sezione **URL e dominio Insperity ExpensAble** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://server.expensable.com/esapp/Authenticate?companyId=<company ID>`

    > [!NOTE] 
    > Poiché non è reale, Aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore contattare il [team di supporto clienti di Insperity ExpensAble ](http://expensable.com/support/support-overview). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Insperity ExpensAble** fare clic su **Configura Insperity ExpensAble** per aprire la finestra **Configura accesso**. Copiare i valori **SAML Entity ID (ID entità SAML) e SAML Single Sign-On Service URL (URL servizio Single Sign-On SAML)** dalla sezione **Quick Reference** (Riferimento rapido).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_configure.png) 

7. Per configurare l'accesso Single Sign-On sul lato **Insperity ExpensAble** è necessario inviare il file **XML metadati** scaricato e i valori **SAML Entity ID** (ID entità SAML) e **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) al [team di supporto Insperity ExpensAble](http://expensable.com/support/support-overview). L'applicazione viene configurata in modo che la connessione SAML SSO sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-insperity-expensable-test-user"></a>Creazione di un utente test di Insperity ExpensAble

Questa sezione descrive come creare un utente chiamato Britta Simon in Insperity ExpensAble. Collaborare con il [team di supporto di Insperity ExpensAble](http://expensable.com/support/support-overview) per aggiungere gli utenti nell'account Insperity ExpensAble.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Insperity ExpensAble.

![Assegna utente][200] 

**Per assegnare Britta Simon a Insperity ExpensAble, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell’elenco di applicazioni selezionare **Insperity ExpensAble**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Insperity ExpensAble nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Insperity ExpensAble.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_04.png
[100]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_100.png
[200]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_203.png


