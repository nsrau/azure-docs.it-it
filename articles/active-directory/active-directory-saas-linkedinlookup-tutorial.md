---
title: 'Esercitazione: integrazione di Azure Active Directory con LinkedIn Lookup| Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e LinkedIn Lookup.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a2757a39-1ead-4a3e-91e4-270be3055683
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: f343d8dbbd80de1a50b625e949377aa7c48d0cae
ms.contentlocale: it-it
ms.lasthandoff: 04/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-lookup"></a>Esercitazione: Integrazione di Azure Active Directory con LinkedIn Lookup

Questa esercitazione descrive come integrare LinkedIn Lookup con Azure Active Directory (Azure AD).

L'integrazione di LinkedIn Lookup con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a LinkedIn Lookup
- È possibile abilitare gli utenti per l'accesso automatico a LinkedIn Lookup (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con LinkedIn Lookup sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di LinkedIn Lookup abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiungere LinkedIn Lookup dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-linkedin-lookup-from-the-gallery"></a>Aggiungere LinkedIn Lookup dalla raccolta
Per configurare l'integrazione di LinkedIn Lookup in Azure AD è necessario aggiungere LinkedIn Lookup dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LinkedIn Lookup dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca online digitare **LinkedIn Lookup**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_search.png)

5. Nel pannello dei risultati selezionare **LinkedIn Lookup** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LinkedIn Lookup in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di LinkedIn Lookup che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LinkedIn Lookup.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** di Azure AD come valore di **Username** (nome utente) in LinkedIn Lookup.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LinkedIn Lookup è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di LinkedIn Lookup](#creating-an-linkedin-lookup-test-user)**: per avere una controparte di Britta Simon in LinkedIn Lookup collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione LinkedIn Lookup.

**Per configurare l'accesso Single Sign-On di Azure AD con LinkedIn Lookup seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **LinkedIn Lookup** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_samlbase.png)

3. In un'altra finestra del Web browser accedere al sito Web di **LinkedIn Lookup** come amministratore.

4. In **Account Center** (Centro account) fare clic su **Global Settings** (Impostazioni globali) in **Settings** (Impostazioni). Selezionare anche **Lookup** (Cerca) nell'elenco a discesa.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_011.png)

5. Fare clic su **OR Click Here to load and copy individual fields from the form** (O fare clic qui per caricare e copiare i singoli file dal modulo) e copiare l'**ID entità** e l'**URL ACS (Assertion Consumer Access)**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_032.png)

6. Nella sezione **URL e dominio LinkedIn Lookup** se si vuole configurare l'applicazione in modalità avviata da **IDP**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url.png)

    a. Nella casella di testo **Identificatore** immettere l'**ID entità** copiato dal portale di LinkedIn 

    b. Nella casella di testo **URL di risposta** immettere l'**URL ACS (Assertion Consumer Access)** copiato dal portale di LinkedIn

7. Selezionare **Mostra impostazioni URL avanzate**, se si desidera configurare l'applicazione in modalità avviata da **SP**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://www.linkedIn.com/checkpoint/enterprise/login/<AccountId>?application=lookup`
     
    > [!NOTE] 
    > Si noti che non è il valore reale. L'utente deve aggiornare questi valori con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto di LinkedIn Lookup](https://business.LinkedIn.com/lookup).

8. L'applicazione **LinkedIn Lookup** si aspetta che le asserzioni SAML abbiano un formato specifico. L'utente deve aggiungere dei mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente mostra un esempio. Il valore predefinito dell'**ID utente** è **user.userprincipalname** ma LinkedIn Lookup prevede che venga mappato all'indirizzo di posta elettronica dell'utente. È possibile usare l'attributo **user.mail** dall'elenco oppure usare il valore di attributo appropriato in base alla configurazione dell'organizzazione. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/updateusermail.png)
    
9. Nella sezione **Attributi utente** fare clic su **Visualizza e modifica tutti gli altri attributi utente** e impostare gli attributi. È necessario aggiungere un'altra attestazione denominata **department** e il valore deve essere mappato con **user.department**.

    | Nome attributo | Valore attributo |
    | --- | --- |    
    | department| user.department |

       ![Creating an Azure AD test user](./media/active-directory-saas-LinkedInlookup-tutorial/userattribute.png)

       a. Click **Add attribute** to open the attribute details page add the department attribute as shown below-

       ![Creating an Azure AD test user](./media/active-directory-saas-LinkedInlookup-tutorial/adduserattribute.png)
   
       b. Click **Ok** to save the attribute.

10. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_certificate.png) 

11. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_400.png)

12. Accedere alla sezione **LinkedIn Admin Settings** (Impostazioni di amministrazione LinkedIn). Caricare il file XML scaricato dal portale di Azure facendo clic sull'opzione **Upload XML file** (Carica file XML).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_metadata_03.png)

13. Fare clic su **On** per abilitare SSO. Lo stato SSO passa da **Not Connected** (Non connesso) a **Connected** (Connesso)

    ![Configura accesso Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_admin_05.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_01.png) 

2. Fare clic **Utenti e gruppi** e su **Tutti gli utenti**.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_02.png) 

3. Fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Name** (Nome) digitare **Britta Simon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di Britta Simon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-linkedin-lookup-test-user"></a>Creazione di un utente test di LinkedIn Lookup

L'applicazione Linked Lookup supporta il provisioning dell'utente just-in-time (JIT) e dopo l'autenticazione gli utenti verranno automaticamente creati nell'applicazione. Attivare l'**assegnazione automatica delle licenze** per assegnare una licenza all'utente.
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedin_admin_license.png)

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso a LinkedIn Lookup per consentirle di usare l'accesso Single Sign-On di Azure.

![Assegna utente][200] 

**Per assegnare Britta Simon a LinkedIn Lookup seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **LinkedIn Lookup**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro LinkedIn Lookup nel pannello di accesso, si viene reindirizzati alla pagina dell'organizzazione, nella quale è necessario fornire i dettagli dell'account LinkedIn personale. In questo modo l'account personale viene collegato all'account aziendale di LinkedIn. 

Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_203.png


