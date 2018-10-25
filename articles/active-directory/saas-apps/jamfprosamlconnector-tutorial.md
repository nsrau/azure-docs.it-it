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
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: d28e28a2c4f8144da16c4838f07c9b8bb5ce67f0
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268158"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Esercitazione: Integrazione di Azure Active Directory con Jamf Pro

Questa esercitazione descrive come integrare Jamf Pro con Azure Active Directory (Azure AD).

L'integrazione di Jamf Pro con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Jamf Pro.
- È possibile abilitare gli utenti per l'accesso automatico a Jamf Pro (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Jamf Pro, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Jamf Pro abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Jamf Pro dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-jamf-pro-from-the-gallery"></a>Aggiunta di Jamf Pro dalla raccolta

Per configurare l'integrazione di Jamf Pro in Azure AD, è necessario aggiungere Jamf Pro dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Jamf Pro dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/jamfprosamlconnector-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/jamfprosamlconnector-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/jamfprosamlconnector-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **Jamf Pro**, selezionare **Jamf Pro** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/jamfprosamlconnector-tutorial/a_add_app.png)

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

1. Nella pagina di integrazione dell'applicazione **Jamf Pro** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_select_sso.png)

2. Fare clic su **Modifica modalità Single Sign-On** nella parte superiore della schermata per selezionare la modalità **SAML**.

      ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_ssso.png)

3. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare il Single Sign-On.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_sso.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/jamfprosamlconnector-tutorial/b1-domains_and_urlsedit.png)

5. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<subdomain>.jamfcloud.com/saml/metadata`.

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.jamfcloud.com/saml/SSO`.

    ![image](./media/jamfprosamlconnector-tutorial//b2-domains_and_urls.png)

    c. Fare clic su **Impostare URL aggiuntivi**.

    d. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.jamfcloud.com`.

    ![image](./media/jamfprosamlconnector-tutorial//b4-domains_and_urls.png)

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si otterrà il valore effettivo dell'identificatore dalla sezione **Single Sign-On** del portale di Jamf Pro, come illustrato più avanti nell'esercitazione. È possibile estrarre il valore effettivo di **subdomain** dal valore dell'identificatore e usare tali informazioni di **subdomain** in URL di accesso e URL di risposta.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![image](./media/jamfprosamlconnector-tutorial/C2_certificate.png)

7. Per automatizzare la configurazione all'interno di Jamf Pro, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installare l'estensione**.

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)
 
8. Dopo aver aggiunto l'estensione al browser, fare clic su **setup Jamf Pro** per passare direttamente all'applicazione Jamf Pro. Da qui, fornire le credenziali di amministratore per accedere a Jamf Pro. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 9 a 12.

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

9. Se si vuole configurare manualmente Jamf Pro, aprire una nuova finestra del Web browser, accedere al sito aziendale di Jamf Pro come amministratore e seguire questa procedura:

10. Fare clic sull'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Configurazione di Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

11. Fare clic su **Single Sign On**.

    ![Configurazione di Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

12. Nella pagina **Single Sign-On** eseguire la procedura seguente:

    ![Single Sign-On di Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Selezionare **Jamf Pro Server** per abilitare l'accesso Single Sign-On.

    b. Se si seleziona **Allow bypass for all users** (Consenti bypass per tutti gli utenti) gli utenti non vengono reindirizzati alla pagina di accesso del provider di identità per l'autenticazione, ma possono accedere a Jamf Pro direttamente. Se un utente prova ad accedere a Jamf Pro tramite il provider di identità, l'autenticazione SSO e l'autorizzazione vengono avviate dal provider di identità.

    c. Selezionare l'opzione **NameID** (ID nome) per **USER MAPPING: SAML** (Mapping utenti: SAML). Per impostazione predefinita, questa impostazione corrisponde a **NameID** (ID nome), ma è possibile definire un attributo personalizzato.

    d. Selezionare **Email** (Posta elettronica) per **USER MAPPING: JAMF PRO** (Mapping utenti: Jamf Pro). Jamf Pro esegue il mapping degli attributi SAML inviati dal provider di identità nei modi seguenti: per utenti e per gruppi. Se un utente tenta di accedere a Jamf Pro, per impostazione predefinita Jamf Pro ottiene informazioni sull'utente dal provider di identità e cerca una corrispondenza tra i propri account utente. Se l'account utente in ingresso non esiste in Jamf Pro, cerca una corrispondenza tra i nomi di gruppo.

    e. Incollare il valore `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` nella casella di testo **GROUP ATTRIBUTE NAME** (Nome attributo gruppo).

13. Nella stessa pagina scorrere verso il basso fino a **IDENTITY PROVIDER** (Provider di identità) nella sezione **Single Sign-On** ed eseguire la procedura seguente:

    ![Configurazione di Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Selezionare l'opzione **Other** (Altro) nell'elenco a discesa **IDENTITY PROVIDER** (PROVIDER DI IDENTITÀ).

    b. Nella casella di testo **OTHER PROVIDER** (ALTRO PROVIDER) immettere **Azure AD**.

    c. Selezionare l'opzione **Metadata URL** (URL dei metadati) nell'elenco a discesa **IDENTITY PROVIDER METADATA SOURCE** (ORIGINE METADATI DEL PROVIDER DI IDENTITÀ) e nella casella di testo seguente incollare il valore di **URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    d. Copiare il valore di **Entity ID** (ID entità) e incollarlo nella casella di testo **Identificatore (ID entità)** nella sezione **URL e dominio Jamf Pro** del portale di Azure.

    >[!NOTE]
    > Il valore disattivato visualizzato corrisponde alla parte relativa al sottodominio. Usare questo valore per completare l'URL di accesso e l'URL di risposta nella sezione **URL e dominio Jamf Pro** del portale di Azure.

    e. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Azure Active Directory**, selezionare **Utenti** e quindi selezionare **Tutti gli utenti**.

    ![image](./media/jamfprosamlconnector-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/jamfprosamlconnector-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/jamfprosamlconnector-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="create-a-jamf-pro-test-user"></a>Creare un utente di test di Jamf Pro

Per consentire agli utenti di Azure AD di accedere a Jamf Pro, è necessario effettuarne il provisioning in Jamf Pro. Nel caso di Jamf Pro, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Jamf Pro come amministratore.

2. Fare clic sull'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Aggiungere un dipendente](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Fare clic su **Jamf Pro User Accounts & Groups** (Gruppi e account utente di Jamf Pro).

    ![Aggiungere un dipendente](./media/jamfprosamlconnector-tutorial/user1.png)

4. Fare clic su **Nuovo**.

    ![Aggiungere un dipendente](./media/jamfprosamlconnector-tutorial/user2.png)

5. Fare clic su **Create Standard Account** (Crea account standard).

    ![Aggiungere un dipendente](./media/jamfprosamlconnector-tutorial/user3.png)

6. Nella finestra di dialogo **New Account** (Nuovo account) seguire questa procedura:

    ![Aggiungere un dipendente](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Nella casella di testo **USERNAME** (NOME UTENTE) digitare il nome e cognome BrittaSimon.

    b. Selezionare le opzioni appropriate per l'organizzazione per **ACCESS LEVEL** (LIVELLO DI ACCESSO), **PRIVILEGE SET** (SET DI PRIVILEGI) e **ACCESS STATUS** (STATO ACCESSO).

    c. Nella casella di testo **FULL NAME** (NOME COMPLETO) digitare il nome e cognome di Britta Simon.

    d. Nella casella di testo **EMAIL ADDRESS** (INDIRIZZO DI POSTA ELETTRONICA) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    e. Nella casella di testo **PASSWORD** digitare la password per l'utente.

    f. Nella casella di testo **VERIFY PASSWORD** (VERIFICA PASSWORD) digitare la password per l'utente.

    g. Fare clic su **Save**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Jamf Pro.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_applications.png)

2. Nell'elenco di applicazioni selezionare **Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_proapplications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/jamfprosamlconnector-tutorial/d_leftpaneusers.png)

4. Fare clic sul pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/jamfprosamlconnector-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Jamf Pro nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Jamf Pro.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
