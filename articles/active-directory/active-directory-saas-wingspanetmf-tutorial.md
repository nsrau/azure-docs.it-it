---
title: 'Esercitazione: Integrazione di Azure Active Directory con Wingspan eTMF | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Wingspan eTMF.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ace320d3-521c-449c-992f-feabe7538de7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: 8c76fb64229abcad0cabb910e7c170979a79d839
ms.contentlocale: it-it
ms.lasthandoff: 05/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-wingspan-etmf"></a>Esercitazione: Integrazione di Azure Active Directory con Wingspan eTMF

Questa esercitazione descrive come integrare Wingspan eTMF con Azure Active Directory (Azure AD).

L'integrazione di Wingspan eTMF con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Wingspan eTMF.
- È possibile abilitare gli utenti per l'accesso automatico a Wingspan eTMF (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Wingspan eTMF sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Wingspan eTMF abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Wingspan eTMF dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-wingspan-etmf-from-the-gallery"></a>Aggiunta di Wingspan eTMF dalla raccolta
Per configurare l'integrazione di Wingspan eTMF in Azure AD è necessario aggiungere Wingspan eTMF dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Wingspan eTMF dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Wingspan eTMF**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wingspanetmf-tutorial/tutorial_wingspanetmf_search.png)

5. Nel pannello dei risultati selezionare **Wingspan eTMF** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wingspanetmf-tutorial/tutorial_wingspanetmf_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione si configura e si testa l'accesso Single Sign-On di Azure AD con Wingspan eTMF in base a un utente test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Wingspan eTMF che corrisponde a un utente di Azure AD. Deve essere stabilita, in altre parole, una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Wingspan eTMF.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in Wingspan eTMF.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Wingspan eTMF, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Wingspan eTMF](#creating-a-wingspan-etmf-test-user)**: per avere una controparte di Britta Simon in Wingspan eTMF collegata alla rappresentazione in Azure AD dell'utente.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione si abilita l'accesso Single Sign-On di Azure AD nel portale di Azure e si configura l'accesso Single Sign-On nell'applicazione Wingspan eTMF.

**Per configurare l'accesso Single Sign-On di Azure AD con Wingspan eTMF, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Wingspan eTMF** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-wingspanetmf-tutorial/tutorial_wingspanetmf_samlbase.png)

3. Nella sezione **URL e dominio Wingspan eTMF** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wingspanetmf-tutorial/tutorial_wingspanetmf_url11.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<customer name>.<instance name>.mywingspan.com/saml`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `http://saml.<instance name>.wingspan.com/shibboleth`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<customer name>.<instance name>.mywingspan.com/`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare i valori con l'URL di accesso, l'identificatore e l'URL di risposta effettivi inclusi il nome del cliente e il nome dell'istanza effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Wingspan eTMF](http://www.wingspan.com/contact-us/). 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wingspanetmf-tutorial/tutorial_wingspanetmf_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wingspanetmf-tutorial/tutorial_general_400.png)

6. Per configurare l'accesso Single Sign-On sul lato **Wingspan eTMF**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di Wingspan eTMF](http://www.wingspan.com/contact-us/). L'applicazione verrà configurata in modo che la connessione SAML SSO sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wingspanetmf-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wingspanetmf-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wingspanetmf-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wingspanetmf-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-wingspan-etmf-test-user"></a>Creazione di un utente test di Wingspan eTMF

In questa sezione si crea un utente di nome Britta Simon in Wingspan eTMF. Collaborare con il [supporto di Wingspan eTMF](http://www.wingspan.com/contact-us/) per aggiungere gli utenti nell'applicazione Wingspan eTMF. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Wingspan eTMF.

![Assegna utente][200] 

**Per assegnare Britta Simon a Wingspan eTMF, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Wingspan eTMF**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wingspanetmf-tutorial/tutorial_wingspanetmf_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso. 

Fare clic sul riquadro Wingspan eTMF nel pannello di accesso e si verrà reindirizzati alla pagina di accesso dell'organizzazione. Dopo aver eseguito l'accesso, si verrà registrati nell'applicazione Wingspan eTMF. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wingspanetmf-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wingspanetmf-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wingspanetmf-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wingspanetmf-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wingspanetmf-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wingspanetmf-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wingspanetmf-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wingspanetmf-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wingspanetmf-tutorial/tutorial_general_203.png


