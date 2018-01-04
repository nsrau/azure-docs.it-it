---
title: 'Esercitazione: Integrazione di Azure Active Directory con Sprinklr | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 13eea1b7889c6382f1f4a6fb5c9bc02578c52711
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Esercitazione: Integrazione di Azure Active Directory con Sprinklr

Questa esercitazione descrive come integrare Sprinklr con Azure Active Directory (Azure AD).

L'integrazione di Sprinklr con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Sprinklr
- È possibile abilitare gli utenti per l'accesso automatico a Sprinklr (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Sprinklr, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Sprinklr abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Sprinklr dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sprinklr-from-the-gallery"></a>Aggiunta di Sprinklr dalla raccolta
Per configurare l'integrazione di Sprinklr in Azure AD, è necessario aggiungere Sprinklr dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Sprinklr dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Sprinklr**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_search.png)

5. Nel pannello dei risultati selezionare **Sprinklr** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Sprinklr usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Sprinklr corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Sprinklr.

Per stabilire la relazione di collegamento, in Sprinklr assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Sprinklr, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Sprinklr](#creating-a-sprinklr-test-user)**: per avere una controparte di Britta Simon in Sprinklr collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Sprinklr.

**Per configurare l'accesso Single Sign-On di Azure AD con Sprinklr, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Sprinklr** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

3. Nella sezione **URL e dominio Sprinklr** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.sprinklr.com`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.sprinklr.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Sprinklr](https://www.sprinklr.com/contact-us/). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Sprinklr** fare clic su **Configura Sprinklr** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

7. In un'altra finestra del Web browser accedere al sito aziendale di Sprinklr come amministratore.

8. Passare ad **Administration (Amministrazione) \> Settings (Impostazioni)**.
   
    ![Amministrazione](./media/active-directory-saas-sprinklr-tutorial/ic782907.png "Amministrazione")

9. Passare a **Manage Partner (Gestisci partner) \> Single Sign (Accesso singolo)** nel riquadro sinistro.
   
    ![Gestione partner](./media/active-directory-saas-sprinklr-tutorial/ic782908.png "Gestione partner")

10. Fare clic su **+ Add Single Sign Ons**.
   
    ![Accessi Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/ic782909.png "Accessi Single Sign-On")

11. Nella pagina **Single Sign On** eseguire la procedura seguente:
   
    ![Accessi Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/ic782910.png "Accessi Single Sign-On")

    a. Nella casella di testo **Name** (Nome) digitare un nome per la configurazione (ad esempio *WAADSSOTest*).

    b. Selezionare **Enabled**.

    c. Selezionare **Use new SSO Certificate**.
             
    e. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Certificato provider di identità**.

    f. Incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure nella casella di testo **ID entità**.

    g. Incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure nella casella di testo **URL di accesso provider di identità**.

    h. Incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure nella casella di testo **URL di disconnessione provider di identità**.
     
    i. In **SAML User ID Type** (Tipo ID utente SAML) selezionare **Assertion contains User”s sprinklr.com username** (L'asserzione contiene il nome utente sprinklr.com dell'utente).

    j. In **SAML User ID Location** (Percorso ID utente SAML) selezionare **User ID is in the Name Identifier element of the Subject statement** (L'ID utente è nell'elemento identificatore nome dell'istruzione Subject).

    k. Fare clic su **Save**.
       
    ![SAML](./media/active-directory-saas-sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-sprinklr-test-user"></a>Creazione di un utente di test di Sprinklr

1. Accedere al sito aziendale di Sprinklr come amministratore.

2. Passare ad **Administration (Amministrazione) \> Settings (Impostazioni)**.
   
    ![Amministrazione](./media/active-directory-saas-sprinklr-tutorial/ic782907.png "Amministrazione")

3. Passare a **Manage Client (Gestisci client) \> Users (Utenti)** dal riquadro di sinistra.
   
    ![Impostazioni](./media/active-directory-saas-sprinklr-tutorial/ic782914.png "Impostazioni")

4. Fare clic su **Add User**.
   
    ![Impostazioni](./media/active-directory-saas-sprinklr-tutorial/ic782915.png "Impostazioni")

5. Nella finestra di dialogo **Edit User** seguire la procedura seguente:
   
    ![Modifica degli utenti](./media/active-directory-saas-sprinklr-tutorial/ic782916.png "Modifica degli utenti") 

    a. Nelle caselle di testo **Email** (E-mail), **First name** (Nome) e **Last name** (Cognome) digitare i dati di un account utente di Azure AD di cui eseguire il provisioning.

    b. Selezionare **Password disabled**.

    c. Selezionare **Language** (Lingua).

    d. Selezionare **User Type** (Tipo di utente).

    e. Fare clic su **Update**.
   
     >[!IMPORTANT]
     >**Password disabled** deve essere selezionata per consentire agli utenti di accedere tramite un provider di identità. 
     
6. Passare a **Role**ed eseguire la procedura seguente:
   
    ![Ruoli partner](./media/active-directory-saas-sprinklr-tutorial/ic782917.png "Ruoli partner")

    a. Dall'elenco **Global** (Globale), selezionare **ALL\_Permissions** (Tutte le autorizzazioni).  

    b. Fare clic su **Update**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento di creazione di account utente di Sprinklr o API fornita da Sprinklr per eseguire il provisioning degli account utente di Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Sprinklr.

![Assegna utente][200] 

**Per assegnare Britta Simon a Sprinklr, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Sprinklr**.

    ![Configure Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Sprinklr nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Sprinklr. Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_203.png

