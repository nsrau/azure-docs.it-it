---
title: 'Esercitazione: Integrazione di Azure Active Directory con Achieve3000 | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Achieve3000.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 83a83d07-ff9c-46c4-b5ba-25fe2b2cd003
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: jeedes
ms.openlocfilehash: 03a6eb581dae88e9789b73c04d5424c0ed14cf62
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-achieve3000"></a>Esercitazione: Integrazione di Azure Active Directory con Achieve3000

Questa esercitazione descrive come integrare Achieve3000 con Azure Active Directory (Azure AD).

L'integrazione di Achieve3000 con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Achieve3000.
- È possibile abilitare gli utenti per l'accesso automatico ad Achieve3000 (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Achieve3000, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Achieve3000 abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Achieve3000 dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-achieve3000-from-the-gallery"></a>Aggiunta di Achieve3000 dalla raccolta
Per configurare l'integrazione di Achieve3000 in Azure AD, è necessario aggiungere Achieve3000 dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Achieve3000 dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Achieve3000**, selezionare **Achieve3000** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Achieve3000 nell'elenco dei risultati](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Achieve3000 usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Achieve3000 che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Achieve3000.

Per stabilire la relazione di collegamento, in Achieve3000 assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Achieve3000, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Achieve3000](#create-an-achieve3000-test-user)**: per avere una controparte di Britta Simon in Achieve3000 collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Achieve3000.

**Per configurare l'accesso Single Sign-On di Azure AD con Achieve3000, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Achieve3000** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_samlbase.png)

3. Nella sezione **URL e dominio Achieve3000** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Achieve3000](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_url.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il criterio seguente: `https://saml.achieve3000.com/district/<District Identifier>`

    b. Nella casella di testo **Identificatore** digitare il valore: `achieve3000-saml`

    > [!NOTE] 
    > Poiché il valore dell'URL di accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Achieve3000](https://www.achieve3000.com/contact-us/). 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_certificate.png) 

5. Nell'attestazione dell'identificatore del nome l'applicazione Achieve3000 richiede un valore **studentID** univoco. Il cliente può eseguire il mapping del valore corretto per l'attestazione dell'identificatore del nome. In questo caso è stato eseguito il mapping di **user.mail** a scopo dimostrativo. Tuttavia, in base all'identificatore univoco è necessario eseguire il mapping del valore corretto.   

    ![Configurare l'attributo Single Sign-On](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |    
    | studentID               | user.mail |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configurare l'aggiunta del Single Sign-On](./media/active-directory-saas-achieve3000-tutorial/tutorial_officespace_04.png)

    ![Configurare l'aggiunta attributo Single Sign-On](./media/active-directory-saas-achieve3000-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-achieve3000-tutorial/tutorial_general_400.png)
    
8. Per configurare l'accesso Single Sign-On sul lato **Achieve3000**, è necessario inviare il file **XML dei metadati** scaricato al [team di supporto di Achieve3000](https://www.achieve3000.com/contact-us/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-achieve3000-test-user"></a>Creare un utente test di Achieve3000

In questa sezione viene creato un utente di nome Britta Simon in Achieve3000. Collaborare con il [team di supporto di Achieve3000](https://www.achieve3000.com/contact-us/) per aggiungere gli utenti alla piattaforma Achieve3000. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Achieve3000.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad Achieve3000, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Achieve3000**.

    ![Collegamento di Achieve3000 nell'elenco delle applicazioni](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Achieve3000 nel pannello di accesso, si accederà automaticamente all'applicazione Achieve3000.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_203.png

