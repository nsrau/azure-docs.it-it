---
title: 'Esercitazione: Integrazione di Azure Active Directory con NetSuite | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Netsuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: jeedes
ms.openlocfilehash: 23e39f20f5b3faa4f6cfba57508d1649e2bbdaa3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Esercitazione: Integrazione di Azure Active Directory con NetSuite

Questa esercitazione descrive come integrare Netsuite con Azure Active Directory (Azure AD).

L'integrazione di Netsuite con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Netsuite
- È possibile abilitare gli utenti per l'accesso automatico a Netsuite (Single Sign-On) con gli account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Netsuite, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Netsuite abilitata per il Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Netsuite dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-netsuite-from-the-gallery"></a>Aggiunta di Netsuite dalla raccolta
Per configurare l'integrazione di Netsuite in Azure AD, è necessario aggiungere Netsuite dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Netsuite dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Netsuite**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. Nel pannello dei risultati selezionare **Netsuite** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Netsuite in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di Netsuite corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Netsuite.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Netsuite.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Netsuite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Netsuite](#creating-a-netsuite-test-user)**: per avere una controparte di Britta Simon in Netsuite collegata alla relativa rappresentazione in Azure AD dell'utente.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Netsuite.

**Per configurare l'accesso Single Sign-On di Azure AD con Netsuite, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Netsuite** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. Nella sezione **URL e dominio Netsuite** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    Nella casella di testo **URL di risposta** digitare un URL usando il modello seguente: `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta effettivo. Per ottenere i valori, contattare il [team di supporto di Netsuite](http://www.netsuite.com/portal/services/support.shtml).
 
4. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Netsuite** fare clic su **Configura Netsuite** per visualizzare la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. Aprire una nuova scheda nel browser e accedere al sito della società Netsuite come amministratore.

8. Sulla barra degli strumenti nella parte superiore della pagina fare clic su **Setup** (Configurazione) e quindi su **Setup Manager** (Configura gestore).

    ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. Dall'elenco **Setup Tasks** (Configura attività) selezionare **Integration** (Integrazione).

    ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. Nella sezione **Manage Authentication** (Gestione autenticazione) fare clic su **SAML Single Sign-on** (Single Sign-on di SAML).

    ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. Nella pagina **SAML Setup** eseguire i passaggi seguenti:
   
    a. Copiare il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) dalla sezione di **Quick Reference** (Riferimento rapido) di **Configure sign-on** (Configura accesso) e incollarlo nel campo **Identity Provider Login Page** (Pagina di accesso provider di identità) in Netsuite.

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    b. In NetSuite selezionare **Primary Authentication Method** (Metodo di autenticazione primario).

    c. Per il campo con etichetta **SAMLV2 Identity Provider Metadata** (Metadati provider identità SAMLV2) selezionare **Upload IDP Metadata File** (Carica file di metadati IDP). Fare quindi clic su **Browse** (Sfoglia) per caricare il file di metadati scaricato dal portale di Azure.

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. Fare clic su **Submit**.

12. In Azure AD fare clic sulla casella di controllo **Visualizza e modifica tutti gli altri attributi utente** e aggiungere l'attributo.

    ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. Nel campo **Nome attributo** immettere `account`. Nel campo **Valore attributo** immettere l'ID dell'account NetSuite. Questo valore è costante e cambia con l'account. Per istruzioni su come individuare l'ID dell'account, vedere di seguito:

      ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. In Netsuite fare clic su **Setup** (Configurazione) nel menu di spostamento superiore.

    b. Fare quindi clic sulla sezione **Setup Tasks** (Configura attività) del menu di spostamento sinistro, selezionare la sezione **Integration** (Integrazione), quindi fare clic su **Web Services Preferences** (Preferenze servizi Web).

    c. Copiare l'ID dell'account NetSuite e incollarlo nel campo **Valore attributo** in Azure AD.

    ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. Per consentire l'accesso Single Sign-On a NetSuite, è prima di tutto necessario che agli utenti vengano assegnate le autorizzazioni appropriate in NetSuite. Per assegnare le autorizzazioni, seguire le istruzioni seguenti.

    a. Scegliere **Setup** (Configurazione) dal menu di navigazione principale, quindi fare clic su **Setup Manager** (Configura gestore).
      
      ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    b. Scegliere **Users/Roles** (Utenti/Ruoli) dal menu di navigazione a sinistra, quindi fare clic su **Manage Roles** (Gestisci ruoli).
      
      ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    c. Fare clic su **New Role**.

    d. Digitare un nome per il nuovo ruolo in **Name** e selezionare la casella di controllo **Single Sign-On Only** (Solo Single Sign-On).
      
      ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    e. Fare clic su **Save**.

    f. Scegliere **Permissions**dal menu disponibile nella parte superiore. Fare quindi clic su **Setup**.
      
       ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    g. Selezionare **Set Up SAM Single Sign-on** (Configura Single Sign-on SAM), quindi fare clic su **Aggiungi**.

    h. Fare clic su **Save**.

    i. Scegliere **Setup** (Configurazione) dal menu di navigazione principale, quindi fare clic su **Setup Manager** (Configura gestore).
      
       ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. Scegliere **Users/Roles** (Utenti/Ruoli) dal menu di navigazione a sinistra, quindi fare clic su **Manage Users** (Gestisci utenti).
      
       ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. Selezionare un utente di test. Fare quindi clic su **Edit**.
      
       ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. Nella finestra di dialogo Roles selezionare il ruolo creato e quindi fare clic su **Add**.
      
       ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. Fare clic su **Save**.
    
> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**. 

### <a name="creating-a-netsuite-test-user"></a>Creazione di un utente test di Netsuite

In questa sezione si crea un utente di nome Britta Simon in Netsuite. Netsuite supporta il provisioning JIT, abilitato per impostazione predefinita.
Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in Netsuite, ne viene creato uno nuovo quando si tenta di accedere a Netsuite.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Netsuite.

![Assegna utente][200] 

**Per assegnare Britta Simon a Netsuite, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Netsuite**.

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com/), quindi accedere all'account di test e fare clic su **Netsuite**.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configura provisioning utenti](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png

