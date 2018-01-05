---
title: 'Esercitazione: Integrazione di Azure Active Directory con Halosys | Documentazione Microsoft'
description: Informazioni su come usare Halosys con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 42a0eb7c-5cb7-44a9-b00b-b0e7df4b63e8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2018
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 6e8167c1152fe80813d5c13706a72badce0a0ce9
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-halosys"></a>Esercitazione: Integrazione di Azure Active Directory con Halosys

Questa esercitazione descrive come integrare Halosys con Azure Active Directory (Azure AD).

L'integrazione di Halosys con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Halosys
- È possibile abilitare gli utenti per l'accesso automatico a Halosys (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Halosys, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Halosys abilitata per l'accesso Single Sign-On


> [!NOTE] 
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Halosys dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-halosys-from-the-gallery"></a>Aggiunta di Halosys dalla raccolta
Per configurare l'integrazione di Halosys in Azure AD, è necessario aggiungere Halosys dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Halosys dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

6. Nella casella di ricerca digitare **Halosys**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_01.png)
    
7. Nel riquadro dei risultati selezionare **Halosys** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_011.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Halosys usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente controparte di Halosys che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Halosys.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in Halosys.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Halosys, è necessario eseguire le operazioni fondamentali seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Halosys](#creating-a-halosys-test-user)**: per avere una controparte di Britta Simon in Halosys collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione, si abilita Azure AD single sign-on nel portale e configurare l'accesso single sign-on nell'applicazione Halosys.


**Per configurare Single Sign-On di Azure AD con Halosys, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SCC LifeCycle** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-scclifecycle-tutorial/tutorial_scclifecycle_samlbase.png)

3. Nel **Halosys dominio e gli URL** sezione, eseguire la procedura seguente:
    1. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<sub-domain>.hs.com/ic7/welcome/customer/PICTtest.aspx`

    2. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente:
    | |
    |--|--|
    | `https://bs1.hs.com/<entity>`|
    | `https://lifecycle.hs.com/<entity>`|
    
    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di SCC LifeCycle](mailto:lifecycle.support@scc.com). 
         
4. Nel **certificato di firma SAML** selezionare **Metadata XML** in **scaricare**e quindi salvare il file di metadati nel computer in uso.
   
5. Per ottenere l'accesso single sign-on configurato per l'applicazione, contattare il team di supporto Halosys e fornire loro le operazioni seguenti:

  * Scaricato **file di metadati**
  * **URL SAML SSO**
    

  >[!NOTE]
  >Accesso Single sign-on deve essere abilitato dal team di supporto Halosys.


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
In questa sezione creare un utente di test nel portale di Britta Simon chiamato.


![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.


### <a name="creating-a-halosys-test-user"></a>Creazione di un utente test per Halosys

In questa sezione viene creato un utente chiamato Britta Simon in Halosys. Collaborare con il team di supporto di Halosys per aggiungere gli utenti alla piattaforma Halosys.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Halosys.

![Assegna utente][200] 

**Per assegnare Britta Simon a Halosys, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Halosys**.

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic su **Add** . Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic su **selezionare** pulsante il **utenti e gruppi** finestra di dialogo.

7. Fare clic su **assegnare** pulsante il **Aggiungi** finestra di dialogo.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Halosys nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Halosys. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_205.png
 