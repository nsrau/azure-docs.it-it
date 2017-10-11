---
title: 'Esercitazione: Integrazione di Azure Active Directory con LiquidFiles | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LiquidFiles.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: cb517134-0b34-4a74-b40c-5a3223ca81b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: b858c6d26b78be4641a46b3453f53d103b512356
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2017
---
# <a name="tutorial-azure-active-directory-integration-with-liquidfiles"></a>Esercitazione: Integrazione di Azure Active Directory con LiquidFiles

Questa esercitazione descrive come integrare LiquidFiles con Azure Active Directory (Azure AD).

L'integrazione di LiquidFiles con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a LiquidFiles
- È possibile abilitare gli utenti per l'accesso automatico a LiquidFiles (Single Sign-On) con gli account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con LiquidFiles, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di LiquidFiles abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di LiquidFiles dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-liquidfiles-from-the-gallery"></a>Aggiunta di LiquidFiles dalla raccolta
Per configurare l'integrazione di LiquidFiles in Azure AD, è necessario aggiungere LiquidFiles dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere LiquidFiles dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **LiquidFiles**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_search.png)

5. Nel pannello dei risultati selezionare **LiquidFiles** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LiquidFiles usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di LiquidFiles corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LiquidFiles.

Per stabilire la relazione di collegamento, in LiquidFiles assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con LiquidFiles, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di LiquidFiles](#creating-a-liquidfiles-test-user)**: per avere una controparte di Britta Simon in LiquidFiles collegata alla rappresentazione in Azure AD dell'utente.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione LiquidFiles.

**Per configurare l'accesso Single Sign-On di Azure AD con LiquidFiles, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **LiquidFiles** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_samlbase.png)

3. Nella sezione **URL e dominio LiquidFiles** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<YOUR_SERVER_URL>/saml/init`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<YOUR_SERVER_URL>`

    c. b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<YOUR_SERVER_URL>/saml/consume`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di LiquidFiles](https://www.liquidfiles.com/support.html). 
 
4. Nella sezione **Certificato di firma SAML** copiare il valore **IDENTIFICAZIONE PERSONALE** del certificato.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di LiquidFiles** fare clic su **Configura LiquidFiles** per visualizzare la finestra **Configura accesso**. Copiare l'**URL di disconnessione e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_configure.png)
 
7. Accedere al sito aziendale LiquidFiles come amministratore.

8. Fare clic su **Single Sign-On** nel menu **Admin (Amministrazione) > Configuration (Configurazione)**.

9. Nella pagina **Single Sign-On Configuration** (Configurazione Single Sign-On) seguire questa procedura

    ![Configura accesso Single Sign-On](./media/active-directory-saas-liquidfiles-tutorial/tutorial_single_01.png)

    a. In **Single Sign On Method** (Metodo Single Sign-On) selezionare **SAML 2**.

    b. Nella casella di testo **IDP Login URL** (URL di accesso IDP) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    c. Nella casella di testo **IDP Logout URL** (URL di disconnessione IDP) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    d. Nella casella di testo **Certificate Fingerprint** (Impronta digitale certificato) incollare il valore di **Identificazione personale** copiato dal portale di Azure.

    e. Nella casella di testo Name Identifier Format (Formato ID nome) digitare il valore **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    f. Nella casella di testo Authn Context (Contesto autorizzazione) digitare il valore **urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport**.

    g. Fare clic su **Salva**.  

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-liquidfiles-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-liquidfiles-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-liquidfiles-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-liquidfiles-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-liquidfiles-test-user"></a>Creazione di un utente test di LiquidFiles

Questa sezione descrive come creare un utente chiamato Britta Simon in LiquidFiles. Contattare l'amministratore del server LiquidFiles per richiedere di essere aggiunti come utente prima di accedere all'applicazione LiquidFiles.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LiquidFiles.

![Assegna utente][200] 

**Per assegnare Britta Simon a LiquidFiles, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **LiquidFiles**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro LiquidFiles nel pannello di accesso, si accederà automaticamente all'applicazione LiquidFiles.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_203.png

