---
title: 'Esercitazione: Integrazione di Azure Active Directory con Hightail | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Hightail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: jeedes
ms.openlocfilehash: 59342aa95e50b29e58035892967be6d0407aae91
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812965"
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Esercitazione: Integrazione di Azure Active Directory con Hightail

Questa esercitazione descrive come integrare Hightail con Azure Active Directory (Azure AD).

L'integrazione di Hightail con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Hightail
- È possibile abilitare gli utenti per l'accesso automatico a Hightail (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Hightail, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Hightail abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Hightail dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-hightail-from-the-gallery"></a>Aggiunta di Hightail dalla raccolta
Per configurare l'integrazione di Hightail in Azure AD, è necessario aggiungere Hightail dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Hightail dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca, digitare **Hightail**.

    ![Creazione di un utente test di Azure AD](./media/hightail-tutorial/tutorial_hightail_search.png)

1. Nel pannello dei risultati selezionare **Hightail** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Hightail mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Hightail corrisponde a un determinato utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Hightail.

Per stabilire la relazione di collegamento, in Hightail assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Hightail, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di Hightail](#creating-a-hightail-test-user)**: per avere una controparte di Britta Simon in Hightail collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Hightail.

**Per configurare l'accesso Single Sign-On di Azure AD con Hightail, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Hightail** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_samlbase.png)

1. Nella sezione **URL e dominio Hightail** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_url.png)

    Nella casella di testo **URL di risposta** digitare un URL come indicato di seguito: `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE]
    > Il valore di URL di risposta non è reale. È necessario aggiornarlo con l'URL di risposta reale, descritto più avanti nell'esercitazione.

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_url1.png)

    Nella casella di testo **URL di accesso** digitare un URL come indicato di seguito: `https://www.hightail.com/loginSSO`

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_certificate.png) 

1. L'applicazione Hightail si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda **Attribute** (Attributo) dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione. 

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_attribute.png) 

1. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |    
    | UserIdentity | user.mail |
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_officespace_04.png)

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_officespace_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.

    e. Fare clic su **OK**.

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Hightail** fare clic su **Configura Hightail** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_configure.png)

    >[!NOTE]
    >Prima di configurare l'accesso Single Sign-On sull'app Hightail inserire il dominio di posta elettronica nell'elenco dei domini consentiti con il team Hightail, in modo che tutti gli utenti del dominio possano beneficiare della funzionalità Single Sign-On.

1. In un'altra finestra del browser, aprire il portale di amministrazione **Hightail**.

1. Fare clic sull'**icona Utente** nell'angolo superiore destro della pagina. 

    ![Configure Single Sign-On](./media/hightail-tutorial/configure1.png)

1. Fare clic sulla scheda **View Admin Console** (Visualizza console di amministrazione).

    ![Configure Single Sign-On](./media/hightail-tutorial/configure2.png)

1. Nel menu nella parte superiore, fare clic sulla scheda **SAML** ed la procedura seguente:

    ![Configure Single Sign-On](./media/hightail-tutorial/configure3.png)

    a. Nella casella di testo **Login URL** (URL di accesso) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    b. Aprire nel Blocco note il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **SAML Certificate** (Certificato SAML).

    c. Fare clic su **COPIA** per copiare l'URL consumer di SAML dell'istanza in uso e incollarlo nella casella di testo **Reply URL** (URL di risposta) della sezione **URL e dominio Hightail** del portale di Azure.

    d. Fare clic su **Salvare le configurazioni**.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/hightail-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/hightail-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/hightail-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/hightail-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-hightail-test-user"></a>Creazione di un utente test di Hightail

Questa sezione descrive come creare un utente chiamato Britta Simon in Hightail. 

Non è necessario alcun intervento dell'utente in questa sezione. Hightail supporta il provisioning utente just-in-time basato su attestazioni personalizzate. Se sono state configurate le attestazioni personalizzate come illustrato nella sezione **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** in alto, viene automaticamente creato un utente nell'applicazione se ancora non esiste. 

>[!NOTE]
>Per creare un utente manualmente è necessario contattare il [team di supporto di Hightail](mailto:support@hightail.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Hightail.

![Assegna utente][200] 

**Per assegnare Britta Simon a Hightail, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni, selezionare **Hightail**.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Hightail nel pannello di accesso, viene effettuato automaticamente l'accesso all'applicazione Hightail.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hightail-tutorial/tutorial_general_01.png
[2]: ./media/hightail-tutorial/tutorial_general_02.png
[3]: ./media/hightail-tutorial/tutorial_general_03.png
[4]: ./media/hightail-tutorial/tutorial_general_04.png

[100]: ./media/hightail-tutorial/tutorial_general_100.png

[200]: ./media/hightail-tutorial/tutorial_general_200.png
[201]: ./media/hightail-tutorial/tutorial_general_201.png
[202]: ./media/hightail-tutorial/tutorial_general_202.png
[203]: ./media/hightail-tutorial/tutorial_general_203.png

