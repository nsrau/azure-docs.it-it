---
title: 'Esercitazione: Integrazione di Azure Active Directory con Front | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Front.
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
ms.date: 08/09/2016
ms.author: jeedes

---
# Esercitazione: Integrazione di Azure Active Directory con Front
Questa esercitazione descrive l'integrazione di Front con Azure Active Directory (Azure AD).

L'integrazione di Front con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Front
* È possibile abilitare gli utenti per l'accesso automatico a Front (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti
Per configurare l'integrazione di Azure AD con Front, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Front abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Front dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## Aggiunta di Front dalla raccolta
Per configurare l'integrazione di Front in Azure AD, è necessario aggiungere Front dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Front dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Active Directory][1]  
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni][2]  
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]  
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Front**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-front-tutorial/tutorial_front_01.png)  
7. Nel riquadro dei risultati selezionare **Front** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-front-tutorial/tutorial_front_0001.png)  

## Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Front in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Front che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Front.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in Front.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Front, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Front](#creating-a-front-test-user)**: per avere una controparte di Britta Simon in Front collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Front.

**Per configurare Single Sign-On di Azure AD con Front, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Front** del portale classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6]  
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Front** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_03.png)
3. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app**, se si desidera configurare l'applicazione in **modalità iniziata da IDP**,seguire la procedura seguente e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_04.png)  
   
    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<company name>.frontapp.com`
   
    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<company name>.frontapp.com/sso/saml/callback`
   
    c. Fare clic su **Avanti**
4. Se si vuole desidera l'applicazione in **SP initiated mode** (Modalità iniziata dal provider di servizi) nella finestra di dialogo **Configurare le impostazioni dell'app** fare clic su **"Mostra opzioni avanzate (facoltativo)"**, quindi digitare l'**URL di accesso** e fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_05.png)  
   
    a. Nella casella di testo **URL di accesso** digitare un URL corrispondente al modello seguente: `https://<company name>.frontapp.com`
   
    b. Fare clic su **Avanti**
   
   > [!NOTE]
   > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'URL di accesso, l'ID e l'URL di risposta effettivo. Per ottenere questi valori, fare riferimento ai dettagli descritti nel **Passaggio 12** oppure contattare Front scrivendo all'indirizzo [support@frontapp.com](emailTo:support@frontapp.com).
   > 
   > 
5. Nella pagina **Configura accesso Single Sign-On in Front** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_06.png)  
   
    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.
   
    b. Fare clic su **Avanti**.
6. Accedere al tenant di Front come amministratore.
7. Passare a **Impostazioni (l'icona dell'ingranaggio in fondo all'intestazione laterale a sinistra) > Preferenze**.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)
8. Fare clic sul collegamento **Single Sign On**.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)  
9. Selezionare **SAML** nell'elenco a discesa **Single Sign-On**.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)  
10. Nella casella di testo **Punto di ingresso** inserire il valore di **URL servizio Single Sign-On** dalla configurazione guidata dell'applicazione di Azure AD.
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)
11. Copiare il contenuto del certificato scaricato, copiarlo e incollarlo nella casella di testo **Certificato di firma**.
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)  
12. Verificare che gli URI corrispondano alla configurazione nel Passaggio 3.
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)  
13. Fare clic sul pulsante **Salva**.
14. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.
    
    ![Accesso Single Sign-On di Azure AD][10]  
15. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
    
    ![Accesso Single Sign-On di Azure AD][11]  

### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]  

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_09.png)  
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)  
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_05.png)
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_06.png)  
   
   a. Nella casella di testo **Nome** digitare **Britta**.
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo**, selezionare **Utente**.
   
   e. Fare clic su **Next**.
7. Nella pagina **Ottieni password temporanea**, fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_07.png)  
8. Nella pagina della finestra di dialogo **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_08.png)  
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.

### Creazione di un utente test di Front
Questa sezione consente di creare un utente chiamato Britta Simon in Front. Collaborare con il team di supporto Front per aggiungere gli utenti all'account Front.

### Assegnazione dell'utente test di Azure AD
L'obiettivo di questa sezione consiste nell'abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Front.

![Assegna utente][200]

**Per assegnare Britta Simon a Front, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]  
2. Nell'elenco delle applicazioni, selezionare **Front**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_50.png)  
3. Scegliere **Utenti** dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna** sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Front nel pannello di accesso, si accederà automaticamente all'applicazione Front.

## Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-front-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-front-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-front-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-front-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-front-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0810_2016-->
