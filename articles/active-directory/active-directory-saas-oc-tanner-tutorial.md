---
title: 'Esercitazione: Integrazione di Azure Active Directory con O.C. Tanner - AppreciateHub | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e O.C. Tanner - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 9af12372b30d9ee1575e46be3b4144fc3b73ec69
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017

---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Esercitazione: Integrazione di Azure Active Directory con O.C. Tanner - AppreciateHub

Questa esercitazione descrive come integrare O.C. Tanner - AppreciateHub con Azure Active Directory (Azure AD).

L'integrazione di O.C. Tanner - AppreciateHub con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a O.C. Tanner - AppreciateHub
- È possibile abilitare gli utenti ad accedere automaticamente a O.C. Tanner - AppreciateHub (Single Sign-On) con i relativi account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con O.C. Tanner - AppreciateHub, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di O.C. Sottoscrizione di Tanner - AppreciateHub abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di O.C. Tanner - AppreciateHub dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Aggiunta di O.C. Tanner - AppreciateHub dalla raccolta
Per configurare l'integrazione di O.C. Tanner - AppreciateHub in Azure AD, è necessario aggiungere O.C. Tanner - AppreciateHub dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere O.C. Tanner - AppreciateHub dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **O.C. Tanner - AppreciateHub**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

5. Nel riquadro dei risultati selezionare **O.C. Tanner - AppreciateHub** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con O.C. Tanner - AppreciateHub in base a un utente test denominato "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di O.C. Tanner - AppreciateHub che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in O.C. Tanner - AppreciateHub.

In O.C. Tanner - AppreciateHub per stabilire la relazione di collegamento, assegnare il valore di **nome utente** in Azure AD come valore dell'attributo **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con O.C. Tanner - AppreciateHub, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di O.C. Tanner - AppreciateHub](#creating-a-oc-tanner---appreciatehub-test-user)** - per avere una controparte di Britta Simon in O.C. Tanner - AppreciateHub collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On con O.C. Tanner - AppreciateHub.

**Per configurare l'accesso Single Sign-On di Azure AD con O.C. Tanner - AppreciateHub, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **O.C. Tanner - AppreciateHub** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

3. Nella sezione **Dominio e URL O.C. Tanner - AppreciateHub**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<companyname>.appreciatehub.com/fed/sp/authnResponse20`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di risposta effettivo. Contattare il [team. di supporto O.C. Tanner - AppreciateHub](mailto:sso@octanner.com) per ottenere questo valore.

    b. Aprire il file dei metadati usando il collegamento seguente: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).
   
    c. Individuare il nodo **md:AssertionConsumerService** . 
   
    d. Copiare il valore dell'attributo **Location** . 
   
    ![Configurare le impostazioni dell'app][12]
   
    e. Nella casella di testo **Sign On URL** incollare il valore ottenuto nel passaggio precedente.

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_400.png)

6. Per configurare l’accesso Single Sign-On sul lato **O.C. Tanner - AppreciateHub**, è necessario inviare il file di **XML metadati** scaricato al team di supporto di [O.C. Tanner - AppreciateHub](mailto:sso@octanner.com).

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Creazione di un utente test di O.C. Tanner - AppreciateHub

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in O.C. Tanner - AppreciateHub.

**Per creare un utente denominato Britta Simon in O.C. Tanner - AppreciateHub, seguire questa procedura:**

Chiedere al [team. di supporto di O.C. Tanner - AppreciateHub](mailto:sso@octanner.com) di creare un utente con un valore per l'attributo nameID equivalente al nome utente di Britta Simon in Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a O.C. Tanner - AppreciateHub.

![Assegna utente][200] 

**Per assegnare Britta Simon a O.C. Tanner - AppreciateHub, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **O.C. Tanner - AppreciateHub**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  
Quando si fa clic sul riquadro O.C. Tanner - AppreciateHub nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione O.C. Tanner - AppreciateHub.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png


