---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mimecast Admin Console | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mimecast Admin Console.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: 64bb18caa046f8a08be8f229e4c82a34306a8f72
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Esercitazione: Integrazione di Azure Active Directory con Mimecast Admin Console

Questa esercitazione descrive come integrare Mimecast Admin Console con Azure Active Directory (Azure AD).

L'integrazione di Mimecast Admin Console con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Mimecast Admin Console.
- È possibile abilitare gli utenti per l'accesso automatico a Mimecast Admin Console (Single Sign-On) con gli account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Mimecast Admin Console, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Mimecast Admin Console abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Mimecast Admin Console dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Aggiunta di Mimecast Admin Console dalla raccolta
Per configurare l'integrazione di Mimecast Admin Console in Azure AD, è necessario aggiungere Mimecast Admin Console dalla raccolta all'elenco delle app SaaS gestite.

**Per aggiungere Mimecast Admin Console dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Mimecast Admin Console**, selezionare **Mimecast Admin Console** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Mimecast Admin Console nell'elenco risultati](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Mimecast Admin Console con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di Mimecast Admin Console corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Mimecast Admin Console.

Per stabilire la relazione di collegamento, in Mimecast Admin Console assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Mimecast Admin Console, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Mimecast Admin Console](#create-a-mimecast-admin-console-test-user)**: per avere una controparte di Britta Simon in Mimecast Admin Console collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Mimecast Admin Console.

**Per configurare l'accesso Single Sign-On di Azure AD con Mimecast Admin Console, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Mimecast Admin Console** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

3. Nella sezione **URL e dominio Mimecast Admin Console** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    Nella casella di testo **URL accesso** digitare l'URL:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > L’URL di accesso è specifico dell’area geografica.

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Mimecast Admin Console** fare clic su **Configura Mimecast Admin Console** per visualizzare la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido.**

    ![Configurazione di Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

7. In un'altra finestra del Web browser accedere a Mimecast Admin Console come amministratore.

8. Passare a **Services \> Application**.

    ![Servizi](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794998.png "Servizi")

9. Fare clic su **Authentication Profiles**.

    ![Profili di autenticazione](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794999.png "Profili di autenticazione")
    
10. Fare clic su **New Authentication Profile**.

    ![Nuovi profili di autenticazione](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795000.png "Nuovi profili di autenticazione")

11. Nella sezione **Authentication Profile** , eseguire la procedura seguente:

    ![Profilo di autenticazione](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795015.png "Profilo di autenticazione")
    
    a. Nella casella di testo **Description** digitare un nome per la configurazione.
    
    b. Selezionare **Enforce SAML Authentication for Mimecast Admin Console**.
    
    c. Come **Provider** selezionare **Azure Active Directory**.
    
    d. Incollare il valore di **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure nella casella di testo **Issuer URL** (URL autorità di certificazione).
    
    e. Incollare l'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure nella casella di testo **Login URL** (URL di accesso).

    f. Incollare l'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure nella casella di testo **Login URL** (URL di disconnessione).
    
    >[!NOTE]
    >Il valore di Login URL e il valore Logout URL per Mimecast Admin Console sono identici.
    
    g. Aprire il certificato con codifica Base 64 scaricato dal portale di Azure nel Blocco note, rimuovere la prima riga ("*--*") e l'ultima riga ("*--*"), copiare il contenuto rimanente negli Appunti e quindi incollarlo nella casella di testo **Identity Provider Certificate (Metadata)** (Certificato provider di identità - Metadati).
    
    h. Selezionare **Allow Single Sign On**.
    
    i. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD). 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Creare un utente test di Mimecast Admin Console

Per consentire agli utenti di Azure AD di accedere a Mimecast Admin Console, è necessario eseguirne il provisioning in Mimecast Admin Console. Nel caso di Mimecast Admin Console, il provisioning è un’attività manuale.

* Per poter creare gli utenti è necessario registrare un dominio.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere a **Mimecast Admin Console** come amministratore.
2. Accedere a **Directories \> Internal**.
   
   ![Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795003.png "Directory")
3. Fare clic su **Register New Domain**.
   
   ![Registra nuovo dominio](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795004.png "Registra nuovo dominio")
4. Dopo aver creato il nuovo dominio, fare clic su **New Address**.
   
   ![Nuovo indirizzo](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795005.png "Nuovo indirizzo")
5. Nella finestra di dialogo del nuovo indirizzo, seguire questa procedura:
   
   ![Salvare](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795006.png "Salvare")
   
   a. Nelle caselle di testo corrispondenti digitare gli attributi **indirizzo di posta elettronica**, **nome globale**, **password** e **conferma password** di un account Azure AD valido di cui si intende effettuare il provisioning.

   b. Fare clic su **Save**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento di creazione di account utente di Mimecast Admin Console o le API fornite da Mimecast Admin Console per effettuare il provisioning degli account utente di Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure tramite la concessione dell'accesso a Mimecast Admin Console.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Mimecast Admin Console, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Mimecast Admin Console**.

    ![Collegamento Mimecast Admin Console nell'elenco Applicazioni](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Mimecast Admin Console nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Mimecast Admin Console.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_203.png

