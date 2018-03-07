---
title: 'Esercitazione: Integrazione di Azure Active Directory con Andromeda SCM | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Andromeda SCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jeedes
ms.openlocfilehash: 72b66eec34995c334c6d65a1d03637fe21b9dc80
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda-scm"></a>Esercitazione: Integrazione di Azure Active Directory con Andromeda SCM

Questa esercitazione descrive come integrare Andromeda SCM con Azure Active Directory (Azure AD).

L'integrazione di Andromeda SCM con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Andromeda SCM.
- È possibile abilitare gli utenti per l'accesso automatico a Andromeda SCM (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Andromeda SCM, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Andromeda SCM abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di SCM Andromeda dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-andromeda-scm-from-the-gallery"></a>Aggiunta di SCM Andromeda dalla raccolta
Per configurare l'integrazione di Andromeda SCM in Azure AD, è necessario aggiungere Andromeda SCM dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Andromeda SCM dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Andromeda SCM**, selezionare **Andromeda SCM** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Andromeda SCM nell'elenco dei risultati](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Andromeda SCM usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Andromeda SCM che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Andromeda SCM.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Andromeda SCM, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Andromeda SCM](#create-an-andromeda-scm-test-user)**: per avere una controparte di Britta Simon in Andromeda SCM collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Andromeda SCM.

**Per configurare l'accesso Single Sign-On di Azure AD con Andromeda SCM, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Andromeda SCM** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. Nella sezione **URL e dominio Andromeda SCM** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Andromeda SCM](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<tenantURL>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<tenantURL>`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Andromeda SCM](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<tenantURL>/SAMLLogon.aspx`
     
    > [!NOTE] 
    > Il valore precedente non è un valore reale. È necessario aggiornare tale valore con i valori reali di identificatore, URL di risposta e URL di accesso. La procedura è descritta più avanti nell'esercitazione.

5. L'applicazione Andromeda SCM prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configurare l'attributo Single Sign-On](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > Cancellare le definizioni dello spazio dei nomi durante la configurazione.
    
6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |    
    | role        | DEMO |
    | type        | DEFAULT |
    | company       | COMP02    |

    > [!NOTE]
    > Non sono valori reali. Tali valori sono solo a scopo dimostrativo; usare i ruoli dell'organizzazione.

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configurare l'aggiunta del Single Sign-On](./media/active-directory-saas-andromedascm-tutorial/tutorial_attribute_04.png)

    ![Configurare l'aggiunta attributo Single Sign-On](./media/active-directory-saas-andromedascm-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    e. Fare clic su **OK**.

7. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-andromedascm-tutorial/tutorial_general_400.png)
    
9. Nella sezione **Configurazione di Andromeda SCM** fare clic su **Configura Andromeda SCM** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configurazione di Andromeda SCM](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Accedere al sito aziendale di Andromeda SCM come amministratore.

11. Nella parte superiore della barra dei menu fare clic su **Admin** (Amministratore) e passare ad **Administration** (Amministrazione).

    ![Amministrazione di Andromeda SCM](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. Sul lato sinistro della barra degli strumenti in **Interfaces** (Interfacce) fare clic su **SAML Configuration** (Configurazione SAML).

    ![Configurazione SAML di Andromeda SCM](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. Nella sezione **SAML Configuration** (Configurazione SAML) seguire la procedura seguente:

    ![Configurazione di Andromeda SCM](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Selezionare **Enable SSO with SAML** (Abilita SSO con SAML).

    b. Nella sezione **Andromeda Information** (Informazioni su Andromeda) copiare il valore del campo **SP Identity** (Identità SP) e incollarlo nella casella di testo **Identificatore** della sezione **URL e dominio Andromeda SCM**.

    c. Copiare il valore del campo **Consumer URL** (URL consumer) nella casella di testo **URL di risposta** della sezione **URL e dominio Andromeda SCM**.

    d. Copiare il valore del campo **URL di accesso** nella casella di testo **URL di accesso** della sezione **URL e dominio Andromeda SCM**.

    e. Nella sezione **Provider di identità SAML** digitare il nome del provider di identità.

    f. Nella casella di testo **Single Sign On End Point** (Endpoint servizio Single Sign-On) incollare il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.

    g. Aprire il **certificato con codifica Base64** scaricato dal portale di Azure nel Blocco note e incollarlo nella casella di testo **Certificato X.509**.
    
    h. Eseguire il mapping degli attributi seguenti con il rispettivo valore per implementare l'accesso SSO da Azure AD. L'attributo **ID utente** è richiesto per l'accesso. Ai fini del provisioning gli attributi relativi a **posta elettronica**, **società**, **tipo di utente** e **ruolo** sono obbligatori. In questa sezione viene definito il mapping degli attributi (nome e valori) associati agli attributi definiti nel portale di Azure.

    ![Mapping degli attributi di Andromeda SCM](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-andromeda-scm-test-user"></a>Creare un utente di test di Andromeda SCM

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Andromeda SCM. Andromeda SCM supporta il provisioning just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se ancora non esiste un nuovo utente, viene creato durante un tentativo di accesso ad Andromeda SCM.

>[!Note]
>Per creare un utente manualmente, contattare il [team di supporto clienti di Andromeda SCM](https://www.ngcsoftware.com/support/).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Andromeda SCM.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad Andromeda SCM seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Andromeda SCM**.

    ![Collegamento di Andromeda SCM nell'elenco delle applicazioni](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Andromeda SCM nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Andromeda SCM.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_203.png

