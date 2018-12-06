---
title: 'Esercitazione: Integrazione di Azure Active Directory con Egnyte | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2018
ms.author: jeedes
ms.openlocfilehash: 33989d04cba06c789089dfc0db62d177d258292a
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976109"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Esercitazione: Integrazione di Azure Active Directory con Egnyte

Questa esercitazione descrive come integrare Egnyte con Azure Active Directory (Azure AD).

L'integrazione di Egnyte con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Egnyte.
- È possibile abilitare gli utenti per l'accesso automatico a Egnyte (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Egnyte sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Egnyte abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Egnyte dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-egnyte-from-the-gallery"></a>Aggiunta di Egnyte dalla raccolta

Per configurare l'integrazione di Egnyte in Azure AD è necessario aggiungere Egnyte dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Egnyte dalla raccolta seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Egnyte**, selezionare **Egnyte** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Egnyte nell'elenco risultati](./media/egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Egnyte usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Egnyte corrisponde a un determinato utente di Azure AD. In altre parole è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Egnyte.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Egnyte è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Egnyte](#creating-an-egnyte-test-user)**: per avere una controparte di Britta Simon in Egnyte collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Egnyte.

**Per configurare l'accesso Single Sign-On di Azure AD con Egnyte, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Egnyte** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Egnyte](./media/egnyte-tutorial/tutorial_egnyte_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Poiché non è reale, Aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore contattare il [team di supporto clienti di Egnyte](https://www.egnyte.com/corp/contact_egnyte.html). 

5. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Certificato (Base64)** e salvare il file di certificato nel computer in uso.

    ![Collegamento di download del certificato](./media/egnyte-tutorial/tutorial_egnyte_certificate.png) 

6. Nella sezione **Configura Egnyte** copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di Egnyte](common/configuresection.png)

7. In un'altra finestra del Web browser accedere al sito aziendale di Egnyte come amministratore.

8. Fare clic su **Impostazioni**.
   
    ![Impostazioni](./media/egnyte-tutorial/ic787819.png "Impostazioni")

9. Scegliere **Settings**dal menu.

    ![Impostazioni](./media/egnyte-tutorial/ic787820.png "Impostazioni")

10. Fare clic sulla scheda **Configuration** (Configurazione) e quindi fare clic su **Security** (Sicurezza).

    ![Sicurezza](./media/egnyte-tutorial/ic787821.png "Sicurezza")

11. Nella sezione **Single Sign-On Authentication** seguire questa procedura:

    ![Single Sign On Authentication](./media/egnyte-tutorial/ic787822.png "Single Sign On Authentication")   
    
    a. In **Single sign-on authentication** (Autenticazione Single Sign-On) selezionare **SAML 2.0**.
   
    b. In **Identity provider** (Provider di identità) selezionare **AzureAD**.
   
    c. Incollare il valore di **URL di accesso** copiato dal portale di Azure nella casella di testo **Identity provider login URL** (URL di accesso provider di identità).
   
    d. Incollare l'**identificatore di Azure AD** copiato dal portale di Azure nella casella di testo **Identity provider entity ID** (ID entità del provider di identità).
      
    e. Aprire nel Blocco note il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Identity provider certificate** (Certificato provider di identità).
   
    f. In **Default user mapping** (Mapping utente predefinito) selezionare **Email address** (Indirizzo posta elettronica).
   
    g. In **Use domain-specific issuer value** (Usa valore autorità emittente specifica del dominio) selezionare **disabled** (disabilitato).
   
    h. Fare clic su **Save**.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="creating-an-egnyte-test-user"></a>Creazione di un utente test di Egnyte

Per consentire agli utenti di Azure AD di accedere a Egnyte, è necessario eseguire il provisioning degli utenti in Egnyte. Nel caso di Egnyte, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Egnyte** come amministratore.

2. Passare a **Settings (Impostazioni) \> Users & Groups (Utenti e gruppi)**.

3. Fare clic su **Add New User**e quindi selezionare il tipo di utente da aggiungere.
   
    ![Utenti](./media/egnyte-tutorial/ic787824.png "Utenti")

4. Nella sezione **New Power User** (Nuovo utente esperto) seguire questa procedura:
    
    ![New Standard User](./media/egnyte-tutorial/ic787825.png "New Standard User")   

    a. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon@contoso.com**.

    b. Nella casella di testo **Username** (Nome utente) digitare il nome dell'utente, ad esempio **Brittasimon**.

    c. Selezionare **Single Sign-On** per **Authentication Type** (Tipo di autenticazione).
   
    d. Fare clic su **Save**.
    
    >[!NOTE]
    >Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica di notifica.
    >

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Egnyte per eseguire il provisioning degli account utente di AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Egnyte.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Egnyte**.

    ![Configure Single Sign-On](./media/egnyte-tutorial/tutorial_egnyte_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Egnyte nel pannello di accesso si accede automaticamente all'applicazione Egnyte.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
