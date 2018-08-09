---
title: 'Esercitazione: Integrazione di Azure Active Directory con myPolicies | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e myPolicies.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bf79e858-1dfb-4ab3-a6df-74b2d5a878d2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: jeedes
ms.openlocfilehash: f4c34d224c65a6e339f12def01079a87247d2d60
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429115"
---
# <a name="tutorial-azure-active-directory-integration-with-mypolicies"></a>Esercitazione: integrazione di Azure Active Directory con myPolicies

Questa esercitazione descrive come integrare myPolicies con Azure Active Directory (Azure AD).

L'integrazione di myPolicies con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a myPolicies
- È possibile abilitare gli utenti per l'accesso automatico a myPolicies (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con myPolicies, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di myPolicies abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di myPolicies dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-mypolicies-from-the-gallery"></a>Aggiunta di myPolicies dalla raccolta
Per configurare l'integrazione di myPolicies in Azure AD, è necessario aggiungere myPolicies dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere myPolicies dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **myPolicies**.

    ![Creazione di un utente test di Azure AD](./media/mypolicies-tutorial/tutorial_mypolicies_search.png)

1. Nel pannello dei risultati selezionare **myPolicies** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/mypolicies-tutorial/tutorial_mypolicies_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con myPolicies usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di myPolicies che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in myPolicies.

Per stabilire la relazione di collegamento, in myPolicies assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con myPolicies, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente di test di myPolicies](#creating-a-mypolicies-test-user)**: per avere una controparte di Britta Simon in myPolicies collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione myPolicies.

**Per configurare Single Sign-On di Azure AD con myPolicies, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **myPolicies** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/mypolicies-tutorial/tutorial_mypolicies_samlbase.png)

1. Nella sezione **URL e dominio myPolicies** seguire questa procedura:

    ![Configure Single Sign-On](./media/mypolicies-tutorial/tutorial_mypolicies_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<tenantname>.mypolicies.com/`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<tenantname>.mypolicies.com/users/auth/saml/callback`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di myPolicies](mailto:support@mypolicies.com).

1. L'applicazione myPolicies prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione. 

    ![Configure Single Sign-On](./media/mypolicies-tutorial/tutorial_mypolicies_attribute.png)

1. Fare clic sulla casella di controllo **Visualizza e modifica tutti gli altri attributi utente** nella sezione **Attributi utente** per espandere gli attributi. Seguire questa procedura per ogni attributo visualizzato:

    | Nome attributo | Valore attributo |
    | ------------------- | ---------- |
    | givenname | user.givenname |
    | surname | user.surname |
    | emailaddress | user.mail |
    | name | user.userprincipalname |
    
    a. Fare clic sull'attributo per aprire la finestra di dialogo **Modifica attributo**.
    
    ![Configure Single Sign-On](./media/mypolicies-tutorial/tutorial_attribute_05.png)
    
    b. Eliminare il valore dell'URL dallo **spazio dei nomi**.
    
    c. Fare clic su **OK** per salvare l'impostazione.
    
1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/mypolicies-tutorial/tutorial_mypolicies_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/mypolicies-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di myPolicies** fare clic su **Configura myPolicies** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configure Single Sign-On](./media/mypolicies-tutorial/tutorial_mypolicies_configure.png) 

1. Per configurare l'accesso Single Sign-On sul lato **myPolicies**, è necessario inviare il **certificato (Base64)** scaricato e l'**URL del servizio Single Sign-On SAML** al [team di supporto di myPolicies](mailto:support@mypolicies.com). 

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/mypolicies-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/mypolicies-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/mypolicies-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/mypolicies-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-mypolicies-test-user"></a>Creazione di un utente di test di myPolicies

In questa sezione viene creato un utente di nome Britta Simon in myPolicies. Collaborare con il [team di supporto di myPolicies](mailto:support@mypolicies.com) per aggiungere gli utenti alla piattaforma myPolicies. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a myPolicies.

![Assegna utente][200] 

**Per assegnare Britta Simon a myPolicies, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **myPolicies**.

    ![Configure Single Sign-On](./media/mypolicies-tutorial/tutorial_mypolicies_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro myPolicies nel pannello di accesso, si accederà automaticamente all'applicazione myPolicies.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mypolicies-tutorial/tutorial_general_01.png
[2]: ./media/mypolicies-tutorial/tutorial_general_02.png
[3]: ./media/mypolicies-tutorial/tutorial_general_03.png
[4]: ./media/mypolicies-tutorial/tutorial_general_04.png

[100]: ./media/mypolicies-tutorial/tutorial_general_100.png

[200]: ./media/mypolicies-tutorial/tutorial_general_200.png
[201]: ./media/mypolicies-tutorial/tutorial_general_201.png
[202]: ./media/mypolicies-tutorial/tutorial_general_202.png
[203]: ./media/mypolicies-tutorial/tutorial_general_203.png

