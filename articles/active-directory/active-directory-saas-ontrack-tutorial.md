---
title: 'Esercitazione: Integrazione di Azure Active Directory con OnTrack | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OnTrack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d2cafba2-3b4a-4471-ba34-80f6a96ff2b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: e1262e276a87bc9a4b6d43de2bf6b3f7d8882213
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ontrack"></a>Esercitazione: Integrazione di Azure Active Directory con OnTrack

Questa esercitazione descrive come integrare OnTrack con Azure Active Directory (Azure AD).

L'integrazione di OnTrack con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a OnTrack.
- È possibile abilitare gli utenti per l'accesso automatico a OnTrack (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con OnTrack, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di OnTrack abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di OnTrack dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-ontrack-from-the-gallery"></a>Aggiunta di OnTrack dalla raccolta
Per configurare l'integrazione di OnTrack in Azure AD, è necessario aggiungere OnTrack dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere OnTrack dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **OnTrack**, selezionare **OnTrack** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![OnTrack nell'elenco risultati](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con OnTrack in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente di OnTrack che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OnTrack.

Per stabilire la relazione di collegamento, in OnTrack assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con OnTrack, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di OnTrack](#create-an-ontrack-test-user)**: per avere una controparte di Britta Simon in OnTrack collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On con OnTrack.

**Per configurare l'accesso Single Sign-On di Azure AD con OnTrack, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **OnTrack** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_samlbase.png)

3. Nella sezione **URL e dominio OnTrack** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di OnTrack](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_url.png)

    a. Nella casella di testo **Identificatore**
    
    Per l'ambiente di test, digitare l'URL: `https://staging.insigniagroup.com/sso`

    Per l'ambiente di produzione, digitare l'URL: `https://oeaccessories.com/sso`

    b. Nella casella di testo **URL di risposta**
    
    Per l'ambiente di test, digitare l'URL: `https://indie.staging.insigniagroup.com/sso/autonation.aspx`

    Per l'ambiente di produzione, digitare l'URL: `https://igaccessories.com/sso/autonation.aspx`

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_certificate.png)

5. L'applicazione OnTrack prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente ed eseguire i passaggi descritti di seguito:
    
    | Nome attributo | Valore attributo |
    | -------------- | ----------------|    
    | Ruolo utente      | "42F432" |
    | Codice Hyperion  | "12345" |

    > [!NOTE]
    > Viene eseguito il mapping degli attributi **Ruolo utente** e **Codice Hyperion** rispettivamente al ruolo utente Autonation e al codice del rivenditore. Si tratta di valori di esempio. Usare il codice corretto per l'integrazione da eseguire. Per assistenza relativa a tali valori, è possibile contattare il [supporto di Autonation](mailto:CustomerService@insigniagroup.com).
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute_04.png)   

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-ontrack-tutorial/tutorial_general_400.png)

8. Per configurare l'accesso Single Sign-On sul lato **OnTrack**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di OnTrack](mailto:CustomerService@insigniagroup.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-ontrack-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-ontrack-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-ontrack-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-ontrack-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-ontrack-test-user"></a>Creare un utente di test di OnTrack

In questa sezione viene creato un utente di nome Britta Simon in OnTrack. Collaborare con il [team di supporto di OnTrack](mailto:CustomerService@insigniagroup.com) per aggiungere gli utenti alla piattaforma OnTrack. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OnTrack.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a OnTrack, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **OnTrack**.

    ![Collegamento di OnTrack nell'elenco delle applicazioni](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di OnTrack nel riquadro di accesso, si dovrebbe accedere automaticamente all'applicazione OnTrack.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_203.png

