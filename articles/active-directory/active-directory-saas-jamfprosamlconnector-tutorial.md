---
title: 'Esercitazione: Integrazione di Azure Active Directory con Jamf Pro | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 488787adfbae0147c3bd0425d839e2ad8c5ed786
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2018
ms.locfileid: "32308764"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Esercitazione: Integrazione di Azure Active Directory con Jamf Pro

Questa esercitazione descrive come integrare Jamf Pro con Azure Active Directory (Azure AD).

L'integrazione di Jamf Pro con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Jamf Pro.
- È possibile abilitare gli utenti per l'accesso automatico a Jamf Pro (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Jamf Pro, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Jamf Pro abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Jamf Pro dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-jamf-pro-from-the-gallery"></a>Aggiunta di Jamf Pro dalla raccolta
Per configurare l'integrazione di Jamf Pro in Azure AD, è necessario aggiungere Jamf Pro dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Jamf Pro dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Jamf Pro**, selezionare **Jamf Pro** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Jamf Pro nell'elenco risultati](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Jamf Pro in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Jamf Pro che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Jamf Pro.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Jamf Pro, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Jamf Pro](#create-a-jamf-pro-test-user)**: per avere una controparte di Britta Simon in Jamf Pro collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Jamf Pro.

**Per configurare l'accesso Single Sign-On di Azure AD con Jamf Pro, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Jamf Pro** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

3. Nella sezione **URL e dominio Jamf Pro** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il criterio seguente: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.jamfcloud.com/saml/SSO`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si otterrà il valore effettivo dell'identificatore dalla sezione **Single Sign-On** del portale di Jamf Pro, come illustrato più avanti nell'esercitazione. È possibile estrarre il valore effettivo di **subdomain** dal valore dell'identificatore e usare tali informazioni di **subdomain** in URL di accesso e URL di risposta.

5. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
7. In un'altra finestra del Web browser accedere al sito aziendale di Jamf Pro come amministratore.

8. Fare clic sull'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Configurazione di Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

9. Fare clic su **Single Sign On**.

    ![Configurazione di Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure2.png)

10. Scorrere verso il basso fino a **IDENTITY PROVIDER** (PROVIDER DI IDENTITÀ) nella sezione **Single Sign-On** e seguire questa procedura:

    ![Configurazione di Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure3.png)

    a. Selezionare l'opzione **Other** (Altro) nell'elenco a discesa **IDENTITY PROVIDER** (PROVIDER DI IDENTITÀ).

    b. Nella casella di testo **OTHER PROVIDER** (ALTRO PROVIDER) immettere **Azure AD**.

    c. Selezionare l'opzione **Metadata URL** (URL dei metadati) nell'elenco a discesa **IDENTITY PROVIDER METADATA SOURCE** (ORIGINE METADATI DEL PROVIDER DI IDENTITÀ) e nella casella di testo seguente incollare il valore di **URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    d. Copiare il valore di **Entity ID** (ID entità) e incollarlo nella casella di testo **Identificatore (ID entità)** nella sezione **URL e dominio Jamf Pro** del portale di Azure.

    >[!NOTE]
    > In questo caso, `aadsso` è la parte del sottodominio (a scopo di riferimento). Usare questo valore per completare l'URL di accesso e l'URL di risposta nella sezione **URL e dominio Jamf Pro** del portale di Azure.

    e. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-jamf-pro-test-user"></a>Creare un utente di test di Jamf Pro

Per consentire agli utenti di Azure AD di accedere a Jamf Pro, è necessario effettuarne il provisioning in Jamf Pro. Nel caso di Jamf Pro, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Jamf Pro come amministratore.

2. Fare clic sull'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Aggiungere un dipendente](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

3. Fare clic su **Jamf Pro User Accounts & Groups** (Gruppi e account utente di Jamf Pro).

    ![Aggiungere un dipendente](./media/active-directory-saas-jamfprosamlconnector-tutorial/user1.png)

4. Fare clic su **Nuovo**.

    ![Aggiungere un dipendente](./media/active-directory-saas-jamfprosamlconnector-tutorial/user2.png)

5. Fare clic su **Create Standard Account** (Crea account standard).

    ![Aggiungere un dipendente](./media/active-directory-saas-jamfprosamlconnector-tutorial/user3.png)

6. Nella finestra di dialogo **New Account** (Nuovo account) seguire questa procedura:

    ![Aggiungere un dipendente](./media/active-directory-saas-jamfprosamlconnector-tutorial/user4.png)

    a. Nella casella di testo **USERNAME** (NOME UTENTE) digitare il nome e cognome BrittaSimon.

    b. Selezionare le opzioni appropriate per l'organizzazione per **ACCESS LEVEL** (LIVELLO DI ACCESSO), **PRIVILEGE SET** (SET DI PRIVILEGI) e **ACCESS STATUS** (STATO ACCESSO).
    
    c. Nella casella di testo **FULL NAME** (NOME COMPLETO) digitare il nome e cognome di Britta Simon.

    d. Nella casella di testo **EMAIL ADDRESS** (INDIRIZZO DI POSTA ELETTRONICA) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    e. Nella casella di testo **PASSWORD** digitare la password per l'utente.

    f. Nella casella di testo **VERIFY PASSWORD** (VERIFICA PASSWORD) digitare la password per l'utente.

    g. Fare clic su **Save**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Jamf Pro.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Jamf Pro, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Jamf Pro**.

    ![Collegamento di Jamf Pro nell'elenco delle applicazioni](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Jamf Pro nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Jamf Pro.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_203.png

