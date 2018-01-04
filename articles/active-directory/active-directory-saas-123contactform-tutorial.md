---
title: 'Esercitazione: Integrazione di Azure Active Directory con 123ContactForm | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e 123ContactForm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 70955676e78642e6c8a6eb85f8165b327baece3f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Esercitazione: Integrazione di Azure Active Directory con 123ContactForm

Questa esercitazione descrive come integrare 123ContactForm con Azure Active Directory (Azure AD).

L'integrazione di 123ContactForm con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a 123ContactForm
- È possibile abilitare gli utenti per l'accesso automatico Single Sign-On a 123ContactForm con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con 123ContactForm sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di 123ContactForm abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di 123ContactForm dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-123contactform-from-the-gallery"></a>Aggiunta di 123ContactForm dalla raccolta
Per configurare l'integrazione di 123ContactForm in Azure AD è necessario aggiungere 123ContactForm dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere 123ContactForm dalla raccolta seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **123ContactForm**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_search.png)

5. Nel pannello dei risultati selezionare **123ContactForm** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con 123ContactForm mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di 123ContactForm corrisponde a un determinato utente di Azure AD. In altre parole è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in 123ContactForm.

Per stabilire la relazione di collegamento, in 123ContactForm assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con 123ContactForm è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di 123ContactForm](#creating-a-123contactform-test-user)**: per avere una controparte di Britta Simon in 123ContactForm collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione 123ContactForm.

**Per configurare l'accesso Single Sign-On di Azure AD con 123ContactForm, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **123ContactForm** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_samlbase.png)

3. Nella sezione **URL e dominio 123ContactForm** seguire questa procedura per configurare l'applicazione in **modalità avviata da IDP**:

    ![Configure Single Sign-On](./media/active-directory-saas-123contactform-tutorial/url1.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

4. Per configurare l'applicazione in **modalità avviata da SP** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-123contactform-tutorial/url2.png)

    a. Fare clic sull'opzione **Mostra impostazioni URL avanzate**.

    b. Nella casella di testo **URL di accesso** digitare un URL come indicato di seguito: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli in seguito con un URL e un identificatore reali. La procedura è descritta più avanti nell'esercitazione.
    
5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-123contactform-tutorial/tutorial_general_400.png)

7. Per configurare l'accesso Single Sign-On sul lato **123ContactForm** accedere a [https://www.123contactform.com/form-2709121/](https://www.123contactform.com/form-2709121/) e seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-123contactform-tutorial/submit.png) 

    a. Nella casella di testo **Email** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ovvero **BrittaSimon@Contoso.com**.

    b. Fare clic su **Carica** e selezionare il file XML metadati scaricato dal portale di Azure.

    c. Fare clic su **SUBMIT FORM** (Invia modulo).

8. In **Microsoft Azure AD - Single sign-on - Configure App Settings** (Microsoft Azure AD - Single Sign-On - Configura impostazioni app) seguire questa procedura:
    
    ![Configure Single Sign-On](./media/active-directory-saas-123contactform-tutorial/url3.png)

    a. Per configurare l'applicazione in **modalità avviata da IDP**, copiare il valore di **IDENTIFIER** (Identificatore) per l'istanza e incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio 123ContactForm** del portale di Azure.
    
    b. Per configurare l'applicazione in **modalità avviata da IDP**, copiare il valore di **REPLY URL** (URL di risposta) per l'istanza e incollarlo nella casella di testo **URL di risposta** nella sezione **URL e dominio 123ContactForm** del portale di Azure.

    c. Per configurare l'applicazione in **modalità avviata da SP**, copiare il valore di **SIGN ON URL** (URL di accesso) per l'istanza e incollarlo nella casella di testo **URL di accesso** nella sezione **URL e dominio 123ContactForm** del portale di Azure.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-123contactform-test-user"></a>Creazione di un utente test di 123ContactForm

L'applicazione supporta il provisioning dell'utente just-in-time e dopo l'autenticazione gli utenti verranno automaticamente creati nell'applicazione.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a 123ContactForm.

![Assegna utente][200] 

**Per assegnare Britta Simon a 123ContactForm, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **123ContactForm**.

    ![Configure Single Sign-On](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro 123ContactForm nel pannello di accesso si accede automaticamente all'applicazione 123ContactForm.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_203.png

