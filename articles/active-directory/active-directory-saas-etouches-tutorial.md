---
title: 'Esercitazione: Integrazione di Azure Active Directory con etouches | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e etouches.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: bc111d6805af9e4f86ab91b6673a0d52dca78223
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Esercitazione: Integrazione di Azure Active Directory con eTouches

Questa esercitazione descrive come integrare etouches con Azure Active Directory (Azure AD).

L'integrazione di etouches con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a etouches
- È possibile abilitare gli utenti per l'accesso automatico a etouches (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con etouches, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di etouches abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di etouches dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-etouches-from-the-gallery"></a>Aggiunta di etouches dalla raccolta
Per configurare l'integrazione di etouches in Azure AD, è necessario aggiungere etouches dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere etouches dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **etouches**, selezionare **etouches** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![etouches nell'elenco risultati](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con etouches usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di etouches corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in etouches.

Per stabilire la relazione di collegamento, in etouches assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con etouches, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di etouches](#create-an-etouches-test-user)**: per avere una controparte di Britta Simon in etouches collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione etouches.

**Per configurare l'accesso Single Sign-On di Azure AD con etouches, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **etouches** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_samlbase.png)

3. Nella sezione **URL e dominio etouches** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di etouches](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare i valori con URL di accesso e identificatore effettivi, come illustrato più avanti nell'esercitazione.
    > 

4. L'applicazione etouches prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi nella scheda **Attributo utente** dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione. 

    ![Attributo utente](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_attribute.png) 

5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |
    | Email | user.mail |    
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Aggiungi attributo](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_04.png)

    ![Finestra di dialogo Aggiungi attributo](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**. 

6. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_general_400.png)

8. Per configurare l'accesso SSO per l'applicazione, seguire questa procedura nell'applicazione etouches: 

    ![Configurazione di etouches](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png) 

    a. Accedere all'applicazione **etouches** con diritti di amministratore.
   
    b. Passare alla pagina di configurazione per **SAML**.

    c. Nella sezione **General Settings** (Impostazioni generali) aprire il certificato scaricato dal portale di Azure nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo dei metadati IDP. 

    d. Fare clic sul pulsante **Save & Stay** (Salva e rimani).
  
    e. Fare clic sul pulsante **Update Metadata** (Aggiorna metadati) nella sezione relativa ai metadati SAML. 

    f. La pagina verrà aperta e verrà eseguito l'accesso SSO. Quando l'accesso SSO funziona, è possibile impostare il nome utente.

    g. Nel campo Username (Nome utente) selezionare **emailaddress** (indirizzo e-mail), come mostrato nell'immagine seguente. 

    h. Copiare il valore dell'**ID entità del provider di servizi** e incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio etouches** del portale di Azure.

    i. Copiare il valore dell'**URL SSO/ACS** e incollarlo nella casella di testo **URL di accesso** nella sezione **URL e dominio etouches** del portale di Azure.
   
> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-etouches-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-etouches-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Pulsante Aggiungi](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Finestra di dialogo Utente](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-etouches-test-user"></a>Creare un utente di test di etouches

In questa sezione viene creato un utente di nome Britta Simon in etouches. Collaborare con il [team di supporto clienti di etouches](https://www.etouches.com/event-software/support/customer-support/) per aggiungere gli utenti nella piattaforma etouches.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a etouches.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a etouches, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **etouches**.

    ![Collegamento di etouches nell'elenco delle applicazioni](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On


Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro etouches nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione etouches.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png

