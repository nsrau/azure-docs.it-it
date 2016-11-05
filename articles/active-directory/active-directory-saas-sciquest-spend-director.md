---
title: 'Esercitazione: Integrazione di Azure Active Directory con SciQuest Spend Director | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SciQuest Spend Director.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: jeedes

---
# Esercitazione: Integrazione di Azure Active Directory con SciQuest Spend Director
Questa esercitazione descrive l’integrazione di SciQuest Spend Director con Azure Active Directory (Azure AD). L'integrazione di SciQuest Spend Director con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SciQuest Spend Director.
* È possibile abilitare gli utenti per l'accesso automatico a SciQuest Spend Director (Single Sign-On) con i propri account Azure AD.
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti
Per configurare l'integrazione di Azure AD con SciQuest Spend Director, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di SciQuest Spend Director abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SciQuest Spend Director dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## Aggiunta di SciQuest Spend Director dalla raccolta
Per configurare l'integrazione di SciQuest Spend Director in Azure AD, è necessario aggiungere SciQuest Spend Director dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SciQuest Spend Director dalla raccolta, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **sciQuest spend director**.
   
    ![Applicazioni][5]
7. Nel riquadro dei risultati selezionare **SciQuest Spend Director**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Applicazioni][6]

## Configurazione e test dell'accesso Single Sign-On di Azure AD
L'obiettivo di questa sezione è illustrare come configurare e testare l'accesso Single Sign-On di Azure AD con SciQuest Spend Director in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SciQuest Spend Director che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SciQuest Spend Director. La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **nome utente** in SciQuest Spend Director.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SciQuest Spend Director, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di SciQuest Spend Director](#creating-a-halogen-software-test-user)**: per avere una controparte di Britta Simon in SciQuest Spend Director collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione SciQuest Spend Director.

**Per configurare Single Sign-On di Azure AD con SciQuest Spend Director, eseguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **SciQuest Spend Director** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][8]
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a SciQuest Spend Director** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Accesso Single Sign-On di Azure AD][9]
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configurare le impostazioni dell'app][10]
   
     3\.1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione SciQuest Spend Director usando il modello seguente: *https://.*sciquest.com/.**
   
     3\.2. Nella casella di testo **URL di risposta** digitare lo stesso valore immesso nella casella di testo **URL di accesso**.
   
     3\.3. Fare clic su **Next**.
4. Nella pagina **Configura accesso Single Sign-On in SciQuest Spend Director** fare clic su **Scarica metadati**, quindi salvare il file di metadati localmente nel computer.
   
    ![Cos'è Azure AD Connect][11]
5. Contattare il supporto tecnico di SciQuest per abilitare questo metodo di autenticazione usando i metadati scaricati.
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Cos'è Azure AD Connect][15]
7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.

### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Cos'è Azure AD Connect][100]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.
   
    ![Cos'è Azure AD Connect][101]
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Cos'è Azure AD Connect][102]
5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:
   
    ![Cos'è Azure AD Connect][103]
   
    a. In **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su Avanti.
6. Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:
   
   ![Cos'è Azure AD Connect][104]
   
   a. Nella casella di testo **Nome** digitare **Britta**.
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo**, selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Cos'è Azure AD Connect][105]
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Cos'è Azure AD Connect][106]
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.

### Creazione di un utente test di SciQuest Spend Director
L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in SciQuest Spend Director.

È necessario contattare il team del supporto tecnico di SciQuest Spend Director e fornire i dettagli relativi all'account di test perché possa essere creato.

In alternativa, è anche possibile sfruttare il provisioning JIT, una funzionalità Single Sign-On supportata da SciQuest Spend Director. Se il provisioning JIT è abilitato, gli utenti vengono creati automaticamente da SciQuest Spend Director durante un tentativo di accesso Single Sign-ON, se non esistono già. Questa funzionalità elimina la necessità di creare manualmente le controparti per l'accesso Single Sign-On.

Per abilitare il provisioning JIT, è necessario contattare il team del supporto tecnico di SciQuest Spend Director.

### Assegnazione dell'utente test di Azure AD
In questa sezione viene descritto come abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SciQuest Spend Director.

![Cos'è Azure AD Connect][200]

**Per assegnare Britta Simon a SciQuest Spend Director, eseguire la procedura seguente:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Cos'è Azure AD Connect][201]
2. Nell'elenco delle applicazioni selezionare **SciQuest Spend Director**.
   
    ![Cos'è Azure AD Connect][202]
3. Scegliere **Utenti** dal menu in alto.
   
    ![Cos'è Azure AD Connect][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
   
    ![Cos'è Azure AD Connect][204]
5. Fare clic su **Assegna** sulla barra degli strumenti in basso.
   
    ![Cos'è Azure AD Connect][205]

### Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso. Quando si fa clic sul riquadro SciQuest Spend Director nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SciQuest Spend Director.

## Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_09.png
[101]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_10.png
[102]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_11.png
[103]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_12.png
[104]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_13.png
[105]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_14.png
[106]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_15.png
[200]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_16.png
[201]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_17.png
[202]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_20.png

<!---HONumber=AcomDC_0907_2016-->