---
title: Esercitazione Integrazione di Azure Active Directory con Litmos | Documentazione Microsoft
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Litmos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 2447cc5dbb01aa718e4418386499c6e50e9f5184
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Esercitazione Integrazione di Azure Active Directory con Litmos

Questa esercitazione descrive come integrare Litmos con Azure Active Directory (Azure AD).

L'integrazione di Litmos con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Litmos.
- È possibile abilitare gli utenti per l'accesso automatico a Litmos (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Litmos, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Litmos abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Litmos dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-litmos-from-the-gallery"></a>Aggiunta di Litmos dalla raccolta
Per configurare l'integrazione di Litmos in Azure AD, è necessario aggiungere Litmos dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Litmos dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Litmos**, selezionare **Litmos** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Litmos nell'elenco dei risultati](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Litmos usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di Litmos che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Litmos.

Per stabilire la relazione di collegamento, in Litmos assegnare il valore di **nome utente** di Azure AD come valore di **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Litmos, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Litmos](#create-a-litmos-test-user)**: per avere una controparte di Britta Simon in Litmos collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Litmos.

**Per configurare l'accesso Single Sign-On di Azure AD con Litmos, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Litmos** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_samlbase.png)

3. Nella sezione **URL e dominio Litmos** seguire questa procedura:

    ![Informazioni sul Single Sign-On di URL e dominio di Litmos](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<companyname>.litmos.com/account/Login`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'effettivo URL di risposta e identificatore, descritti più avanti nell'esercitazione, o contattare [il team di supporto di Litmos](https://www.litmos.com/contact-us/) per ottenere questi valori.

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Il collegamento di download del certificato](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_certificate.png)

5. La procedura di configurazione prevede la personalizzazione degli **Attributi token SAML** per l'applicazione Litmos.

    ![Sezione relativa all'attributo](./media/active-directory-saas-litmos-tutorial/tutorial_attribute.png)
           
    | Nome attributo   | Valore attributo |   
    | ---------------  | ----------------|
    | FirstName |user.givenname |
    | LastName  |user.surname |
    | Email |user.mail |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Aggiungi attributo](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_04.png)

    ![Finestra di dialogo Aggiungi attributo](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.     

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-litmos-tutorial/tutorial_general_400.png)

7. In un'altra finestra del browser Web accedere al sito aziendale di Litmos come amministratore.

8. Nella barra di spostamento sul lato sinistro fare clic su **Accounts**(Account).
   
    ![Sezione relativa agli account sul lato dell'app][22] 

9. Fare clic sulla scheda **Integrations** (Integrazioni).
   
    ![Scheda Integrazione][23] 

10. Nella scheda **Integrations** (Integrazioni) scorrere verso il basso fino a **3rd Party Integrations** (Integrazioni di terze parti) e quindi fare clic sulla scheda **SAML 2.0** (SAML 2.0).
   
    ![Sezione SAML 2.0][24] 

11. Copiare il valore indicato per l'**endpoint SAML per Litmos** e incollarlo nella casella di testo **URL di risposta** della sezione **URL e dominio Litmos** nel portale di Azure. 
   
    ![Endpoint SAML][26] 

12. Nell'applicazione **Litmos** seguire questa procedura:
    
     ![Applicazione Litmos][25] 
     
     a. Fare clic su **Enable SAML**.
    
     b. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **SAML X.509 Certificate** .
     
     c. Fare clic su **Salva modifiche**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-litmos-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-a-litmos-test-user"></a>Creare un utente di test di Litmos

Questa sezione descrive come creare un utente chiamato Britta Simon in Litmos.  
L'applicazione Litmos supporta il provisioning JIT (just-in-time). Questo significa che, se necessario, un account utente viene creato automaticamente durante il tentativo di accedere all'applicazione tramite il pannello di accesso.

**Per creare un utente denominato Britta Simon in Litmos, seguire questa procedura:**

1. In un'altra finestra del browser Web accedere al sito aziendale di Litmos come amministratore.

2. Nella barra di spostamento sul lato sinistro fare clic su **Accounts**(Account).
   
    ![Sezione relativa agli account sul lato dell'app][22] 

3. Fare clic sulla scheda **Integrations** (Integrazioni).
   
    ![Scheda Integrazioni][23] 

4. Nella scheda **Integrations** (Integrazioni) scorrere verso il basso fino a **3rd Party Integrations** (Integrazioni di terze parti) e quindi fare clic sulla scheda **SAML 2.0** (SAML 2.0).
   
    ![SAML 2.0][24] 
    
5. Selezionare **Autogenerate Users**(Genera utenti in automatico)
   
    ![Autogenerate Users (Genera utenti in automatico)][27] 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Litmos.

![Assegnare il ruolo dell'utente][200] 

**Per assegnare Britta Simon a Litmos, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Litmos**.

    ![Il collegamento Litmos nell'elenco Applicazioni](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro Litmos nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Litmos. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png

