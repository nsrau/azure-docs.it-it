<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con MCM | Microsoft Azure" 
    description="Informazioni su come usare MCM con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatico e altro ancora." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/30/2016" 
    ms.author="jeedes" />

#Esercitazione: Integrazione di Azure Active Directory con MCM
  
Questa esercitazione descrive l'integrazione di MCM con Azure Active Directory (Azure AD).

L'integrazione di MCM con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a MCM
- È possibile abilitare gli utenti per l'accesso automatico a MCM (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con MCM sono necessari gli elementi seguenti:

- Sottoscrizione di Azure valida
- Sottoscrizione di MCM abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di MCM dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## Aggiunta di MCM dalla raccolta
Per configurare l'integrazione di MCM in Azure AD è necessario aggiungere MCM dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere MCM dalla raccolta, seguire questa procedura:**

1.  Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory](./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2.  Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.  Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applicazioni](./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Applicazioni")

4.  Fare clic su **Add** nella parte inferiore della pagina.

    ![Aggiunta di un'applicazione](./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Aggiunta di un'applicazione")

5.  Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella di ricerca** digitare **MCM**.

    ![Raccolta di applicazioni](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Raccolta di applicazioni")

7.  Nel riquadro dei risultati selezionare **MCM** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![MCM](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con MCM in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di MCM che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in MCM.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in MCM.

Per configurare e testare l'accesso Single Sign-On di Azure AD con MCM, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di MCM](#creating-a-mcm-test-user)** - per avere una controparte di Britta Simon in MCM collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD
  
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione MCM.

**Per configurare Single Sign-On di Azure AD con MCM, seguire questa procedura:**

1.  Nella pagina di integrazione dell'applicazione **MCM** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Configura accesso Single Sign-On")

2.  Nella pagina **Stabilire come si desidera che gli utenti accedano a MCM** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

    ![Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Single Sign-On di Microsoft Azure AD")

3.  Nella pagina Configurare le impostazioni dell'app seguire questa procedura:

    ![Configura URL app](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configura URL app")

    a. Nella casella di testo **URL di accesso** digitare `https://myaba.co.uk/client-access/<company name>/saml.php`.
	
	b. Fare clic su **Avanti**.

4.  Nella pagina **Configura accesso Single Sign-On in MCM** fare clic su **Scarica metadati** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configura accesso Single Sign-On")

5. Per configurare l'accesso Single Sign-On per l'applicazione, contattare il team di supporto MCM. Allegare il file dei metadati scaricato e condividerlo con il team MCM perché possa svolgere le proprie attività di configurazione di SSO.

6.  Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configura accesso Single Sign-On")

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configura accesso Single Sign-On")


### Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creazione di un utente test di Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.
    
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Next**.

6.  Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:
    
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
    
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
    
	![Creazione di un utente test di Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Complete**.

###Creazione di un utente test di MCM
  
In questa sezione viene creato un utente di nome Britta Simon in MCM. Collaborare con il team di supporto di MCM per aggiungere gli utenti alla piattaforma MCM.

>[AZURE.NOTE]È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da MCM per eseguire il provisioning degli account utente di AAD.


###Assegnazione dell'utente test di Azure AD
  
L'obiettivo di questa sezione consiste nell'abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a MCM.
	
![Assegna utenti](./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Assegna utenti")

**Per assegnare Britta Simon a MCM seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
    
	![Assegna utenti](./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Assegna utenti")

2. Nell'elenco delle applicazioni selezionare **MCM**.
    
	![Configura accesso Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

1. Scegliere **Utenti** dal menu in alto.
    
	![Assegna utenti](./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Assegna utenti")

1. Nell'elenco di utenti selezionare **Britta Simon**.

2. Fare clic su **Assegna** sulla barra degli strumenti in basso.
    
	![Assegna utenti](./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Assegna utenti")


### Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.
 
Quando si fa clic sul riquadro MCM nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione MCM.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0907_2016-->