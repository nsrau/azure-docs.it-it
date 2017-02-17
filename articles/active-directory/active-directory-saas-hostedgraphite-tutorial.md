---
title: 'Esercitazione: Integrazione di Azure Active Directory con Hosted Graphite | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Hosted Graphite.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f7286285828414000f90cf9d71facb9a9a51afd8


---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Esercitazione: Integrazione di Azure Active Directory con Hosted Graphite
Questa esercitazione ha l'obiettivo di mostrare come integrare Hosted Graphite in Azure Active Directory (Azure AD).

L'integrazione di Hosted Graphite in Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD gli utenti che possono accedere a Hosted Graphite
* È possibile abilitare gli utenti per l'accesso automatico a Hosted Graphite (Single Sign-On) con il proprio account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD in Hosted Graphite, è necessario quanto segue:

* Sottoscrizione di Azure AD.
* Sottoscrizione Hosted Graphite Single Sign-On attiva

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Hosted Graphite dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-hosted-graphite-from-the-gallery"></a>Aggiunta di Hosted Graphite dalla raccolta
Per configurare l'integrazione di Hosted Graphite in Azure AD, è necessario aggiungere Hosted Graphite dalla raccolta all'elenco delle app SaaS gestite.

**Per aggiungere Hosted Graphite dalla raccolta, eseguire i passaggi seguenti:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Hosted Graphite**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_01.png)
7. Nel pannello dei risultati selezionare **Hosted Graphite**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
L'obiettivo di questa sezione è mostrare come configurare e testare l'accesso Single Sign-On di AD Azure con Hosted Graphite in base a un utente test di nome "Britta Simon".

Affinché Single Sign-On funzioni, Azure AD deve sapere qual è l'utente di Hosted Graphite che fa da controparte all'utente di Azure AD. In altre parole, è necessario stabilire una relazione di collegamento fra un utente di AD Azure e l'utente correlato di Hosted Graphite.

La relazione di collegamento viene stabilita nel momento in cui il valore di **Nome utente** in Azure AD corrisponde al valore **Username** (Nome utente) in Hosted Graphite.

Per configurare e testare l'accesso Single Sign-On in AD Azure con Hosted Graphite, è necessario completare le operazioni seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Hosted Graphite](#creating-a-hosted-graphite-test-user)** : per avere una controparte di Britta Simon in Hosted Graphite collegata alla rappresentazione di Azure AD di quest'ultima.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione nel portale classico viene abilitato l'accesso Single Sign-On di Azure AD, che viene configurato nell'applicazione Hosted Graphite.

**Per configurare l'accesso Single Sign-On in Azure AD con Hosted Graphite, procedere come segue:**

1. Nella pagina di integrazione dell'applicazione **Hosted Graphite** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Hosted Graphite** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_03.png)
3. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app**, se si desidera configurare l'applicazione in **modalità iniziata da IDP**,seguire la procedura seguente e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_04.png)
   
    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://www.hostedgraphite.com/metadata/<user id>`
   
    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://www.hostedgraphite.com/complete/saml/<user id>`
   
    c. Fare clic su **Avanti**
4. Se si desidera configurare l'applicazione in **SP initiated mode** (Modalità iniziata dal provider di servizi) nella finestra di dialogo **Configurare le impostazioni dell'app** fare clic su **"Mostra opzioni avanzate (facoltativo)"**, quindi digitare l'**URL di accesso** e fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)
   
    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://www.hostedgraphite.com/login/saml/<user id>/`
   
    b. Fare clic su **Avanti**
   
   > [!NOTE]
   > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'URL di accesso, l'ID e l'URL di risposta effettivo. Per ottenere questi valori, è possibile passare a Access->SAML setup (Accesso -> Configurazione SAML) nell'applicazione oppure contattare Hosted Graphite.
   > 
   > 
5. Nella pagina **Configura accesso Single Sign-On in Hosted Graphite** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_05.png)
   
    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.
   
    b. Fare clic su **Next**.
6. Accedere al tenant di Hosted Graphite come amministratore.
7. Accedere alla **pagina SAML Setup** (Configurazione SAML) dalla barra laterale, facendo clic su **Accesso -> SAML Setup (Configurazione SAML)**.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)
8. Verificare che gli URI corrispondano alla configurazione nel Passaggio 3.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)
9. Copiare l'**URL dell'autorità di certificazione** e l'**URL SSO SAML** da Azure AD in **Entity or Issuer ID** (ID entità o ID autorità emittente) e **SSO Login URL** (URL di accesso SSO) in Hosted Graphite.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_003.png)
10. Selezionare "**Sola lettura**" come **ruolo utente predefinito**.
    
     ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)
11. Copiare il contenuto del certificato scaricato, copiarlo e incollarlo nella casella di testo **Certificato X.509** .
    
     ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)
12. Fare clic sul pulsante **Salva** .
13. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.
    
    ![Single Sign-On di Microsoft Azure AD][10]
14. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_04.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_05.png)
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_06.png)
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_07.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_08.png)
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-a-hosted-graphite-test-user"></a>Creazione di un utente test di Hosted Graphite
Questa sezione descrive come creare un utente chiamato Britta Simon in Hosted Graphite. Hosted Graphite supporta il provisioning JIT, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Hosted Graphite verrà creato un nuovo utente, se questo non esiste già.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il team di supporto di Hosted Graphite all'indirizzo <mailto:help@hostedgraphite.com>.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Hosted Graphite.

![Assegna utente][200]

**Per assegnare Britta Simon a Hosted Graphite, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]
2. Nell'elenco delle applicazioni selezionare **Hosted Graphite**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_50.png)
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Hosted Graphite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Hosted Graphite.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


