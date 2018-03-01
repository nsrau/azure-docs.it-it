---
title: 'Esercitazione: Integrazione di Azure Active Directory con SciQuest Spend Director | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SciQuest Spend Director.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: be9b17f31bedca1ae5704b484760c3ad24fbb14d
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Esercitazione: Integrazione di Azure Active Directory con SciQuest Spend Director

Questa esercitazione descrive come integrare SciQuest Spend Director con Azure Active Directory (Azure AD).

L'integrazione di SciQuest Spend Director con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SciQuest Spend Director.
- È possibile abilitare gli utenti per l'accesso automatico a SciQuest Spend Director (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con SciQuest Spend Director, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di SciQuest Spend Director abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di SciQuest Spend Director dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Aggiunta di SciQuest Spend Director dalla raccolta
Per configurare l'integrazione di SciQuest Spend Director in Azure AD, è necessario aggiungere SciQuest Spend Director dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SciQuest Spend Director dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **SciQuest Spend Director**, selezionare **SciQuest Spend Director** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SciQuest Spend Director nell'elenco dei risultati](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SciQuest Spend Director usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di SciQuest Spend Director corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SciQuest Spend Director.

Per stabilire la relazione di collegamento, in SciQuest Spend Director assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con SciQuest Spend Director, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di SciQuest Spend Director](#create-a-sciquest-spend-director-test-user)**: per avere una controparte di Britta Simon in SciQuest Spend Director collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SciQuest Spend Director.

**Per configurare Single Sign-On di Azure AD con SciQuest Spend Director, eseguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **SciQuest Spend Director** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_samlbase.png)

3. Nella sezione **URL e dominio SciQuest Spend Director** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SciQuest Spend Director](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.sciquest.com/apps/Router/SAMLAuth/<instancename>`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<companyname>.sciquest.com`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<companyname>.sciquest.com/apps/Router/ExternalAuth/Login/<instancename>`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SciQuest Spend Director](https://www.jaggaer.com/contact-us/). 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_400.png)

6. Per configurare l'accesso Single Sign-On sul lato **SciQuest Spend Director**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di SciQuest Spend Director](https://www.jaggaer.com/contact-us/).

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-sciquest-spend-director-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-sciquest-spend-director-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-sciquest-spend-director-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-sciquest-spend-director-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-sciquest-spend-director-test-user"></a>Creare un utente di test di SciQuest Spend Director

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in SciQuest Spend Director.

È necessario contattare il [team del supporto tecnico di SciQuest Spend Director](https://www.jaggaer.com/contact-us/) e fornire i dettagli relativi all'account di test perché possa essere creato.

In alternativa, è anche possibile sfruttare il provisioning JIT, una funzionalità Single Sign-On supportata da SciQuest Spend Director.  
Se il provisioning JIT è abilitato, gli utenti vengono creati automaticamente da SciQuest Spend Director durante un tentativo di accesso Single Sign-ON, se non esistono già. Questa funzionalità elimina la necessità di creare manualmente le controparti per l'accesso Single Sign-On.

Per abilitare il provisioning JIT, è necessario contattare il [team del supporto tecnico di SciQuest Spend Director](https://www.jaggaer.com/contact-us/).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SciQuest Spend Director.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SciQuest Spend Director, eseguire la procedura seguente:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SciQuest Spend Director**.

    ![Collegamento di SciQuest Spend Director nell'elenco delle applicazioni](./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SciQuest Spend Director nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SciQuest Spend Director.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_203.png

