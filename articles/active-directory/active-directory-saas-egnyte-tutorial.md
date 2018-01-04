---
title: 'Esercitazione: Integrazione di Azure Active Directory con Egnyte | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 94c71859aff01b60cee1b49664ad62257f9f9f2a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Esercitazione: Integrazione di Azure Active Directory con Egnyte

Questa esercitazione descrive come integrare Egnyte con Azure Active Directory (Azure AD).

L'integrazione di Egnyte con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Egnyte
- È possibile abilitare gli utenti per l'accesso automatico Single Sign-On a Egnyte con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Egnyte sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Egnyte abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Egnyte dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-egnyte-from-the-gallery"></a>Aggiunta di Egnyte dalla raccolta
Per configurare l'integrazione di Egnyte in Azure AD è necessario aggiungere Egnyte dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Egnyte dalla raccolta seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Egnyte**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_search.png)

5. Nel pannello dei risultati selezionare **Egnyte** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Egnyte mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Egnyte corrisponde a un determinato utente di Azure AD. In altre parole è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Egnyte.

Per stabilire la relazione di collegamento, in Egnyte assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Egnyte è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Egnyte](#creating-an-egnyte-test-user)**: per avere una controparte di Britta Simon in Egnyte collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Egnyte.

**Per configurare l'accesso Single Sign-On di Azure AD con Egnyte, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Egnyte** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_samlbase.png)

3. Nella sezione **URL e dominio Egnyte** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Poiché non è reale, Aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore contattare il [team di supporto clienti di Egnyte](https://www.egnyte.com/corp/contact_egnyte.html). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-egnyte-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Egnyte** fare clic su **Configura Egnyte** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido.**

    ![Configure Single Sign-On](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di Egnyte come amministratore.

8. Fare clic su **Impostazioni**.
   
   ![Impostazioni](./media/active-directory-saas-egnyte-tutorial/ic787819.png "Impostazioni")

9. Scegliere **Settings**dal menu.

   ![Impostazioni](./media/active-directory-saas-egnyte-tutorial/ic787820.png "Impostazioni")

10. Fare clic sulla scheda **Configuration** (Configurazione) e quindi fare clic su **Security** (Sicurezza).

    ![Sicurezza](./media/active-directory-saas-egnyte-tutorial/ic787821.png "Sicurezza")

11. Nella sezione **Single Sign-On Authentication** seguire questa procedura:

    ![Single Sign On Authentication](./media/active-directory-saas-egnyte-tutorial/ic787822.png "Single Sign On Authentication")   
    
    a. In **Single sign-on authentication** (Autenticazione Single Sign-On) selezionare **SAML 2.0**.
   
    b. In **Identity provider** (Provider di identità) selezionare **AzureAD**.
   
    c. Incollare il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure nella casella di testo **URL di accesso provider di identità**.
   
    d. Incollare il valore **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure nella casella di testo **Identity provider entity ID** (ID entità provider di identità).
      
    e. Aprire nel Blocco note il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Identity provider certificate** (Certificato provider di identità).
   
    f. In **Default user mapping** (Mapping utente predefinito) selezionare **Email address** (Indirizzo posta elettronica).
   
    g. In **Use domain-specific issuer value** (Usa valore autorità emittente specifica del dominio) selezionare **disabled** (disabilitato).
   
    h. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-egnyte-test-user"></a>Creazione di un utente test di Egnyte

Per consentire agli utenti di Azure AD di accedere a Egnyte, è necessario eseguire il provisioning degli utenti in Egnyte. Nel caso di Egnyte, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Egnyte** come amministratore.

2. Passare a **Settings (Impostazioni) \> Users & Groups (Utenti e gruppi)**.

3. Fare clic su **Add New User**e quindi selezionare il tipo di utente da aggiungere.
   
   ![Utenti](./media/active-directory-saas-egnyte-tutorial/ic787824.png "Utenti")

4. Nella sezione **New Standard User** seguire questa procedura:
   
   ![New Standard User](./media/active-directory-saas-egnyte-tutorial/ic787825.png "New Standard User")   

   a. Immettere i valori **Email** (Posta elettronica), **Username** (Nome utente) e gli altri dettagli di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
   
   b. Fare clic su **Save**.
    
    >[!NOTE]
    >Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica di notifica.
    >

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Egnyte per eseguire il provisioning degli account utente di AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Egnyte.

![Assegna utente][200] 

**Per assegnare Britta Simon a Egnyte, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Egnyte**.

    ![Configure Single Sign-On](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Egnyte nel pannello di accesso si accede automaticamente all'applicazione Egnyte.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_203.png

