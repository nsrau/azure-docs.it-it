<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Facebook at Work | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Facebook at Work."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="asmalser"/>


# Esercitazione: Integrazione di Azure Active Directory con Facebook at Work

Questa esercitazione illustra come integrare Facebook at Work con Azure Active Directory (Azure AD).<br>L'integrazione di Facebook at Work con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Facebook at Work 
- È possibile effettuare automaticamente il provisioning degli account per gli utenti autorizzati ad accedere a Facebook at Work
- È possibile abilitare gli utenti per l'accesso automatico a Facebook at Work (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centralizzata 

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).


## Prerequisiti 

Per configurare l'integrazione di Azure AD con CS Stars, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Facebook at Work con accesso Single Sign-On abilitato

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si ha un ambiente di prova di Azure AD, fare clic [qui](https://azure.microsoft.com/pricing/free-trial/) per ottenere una versione di valutazione valida un mese. 


## Aggiunta di Facebook at Work dalla raccolta
Per configurare l'integrazione di Facebook at Work in Azure AD, è necessario aggiungere Facebook at Work dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Facebook at Work dalla raccolta, seguire questa procedura:**

1. Nel **portale di gestione di Azure** fare clic su **Active Directory** nel riquadro di spostamento sinistro. <br><br>![Active Directory][1]<br>

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto. <br><br>![Applicazioni][2]<br>

4. Fare clic su **Aggiungi** nella parte inferiore della pagina. <br><br>![Applicazioni][3]<br>

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**. <br><br>![Applicazioni][4]<br>

6. Nella casella di ricerca online digitare **Facebook at Work**.

7. Nel riquadro dei risultati selezionare **Facebook at Work** e quindi fare clic su **Completa** per aggiungere l'applicazione.


### Configurazione dell'accesso Single Sign-On di Azure AD

Questa sezione descrive come permettere agli utenti di eseguire l'autenticazione a Facebook at Work con il proprio account di Azure Active Directory usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso Single Sign-On di Azure AD con Facebook at Work, seguire questa procedura:**

1.	Dopo aver aggiunto Facebook at Work nel portale di gestione di Azure, fare clic su **Configura accesso Single Sign-On**.

2.	Nella schermata **Configura URL app** immettere l'URL che verrà digitato dagli utenti per accedere all'applicazione Facebook at Work. Si tratta dell'URL del tenant di Facebook at Work, ad esempio https://example.facebook.com/). Al termine, fare clic su **Avanti**.

3.	In un'altra finestra del Web browser accedere al sito aziendale di Facebook at Work come amministratore.

4. Seguire le istruzioni disponibili nell'URL seguente per configurare Facebook at Work per l'uso di Azure AD come provider di identità. [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.	Al termine della procedura, tornare alle finestre del browser in cui è visualizzato il portale di gestione di Azure, selezionare la casella di controllo per confermare di aver completato la procedura e quindi fare clic su **Avanti** e **Completa**.


## Provisioning automatico degli utenti in Facebook at Work

Azure AD consente di sincronizzare automaticamente i dettagli dell'account degli utenti assegnati a Facebook at Work. La sincronizzazione automatica consente a Facebook at Work di ottenere i dati necessari per autorizzare gli utenti all'accesso, prima che questi tentino di eseguire l'accesso per la prima volta. Esegue anche il deprovisioning degli utenti da Facebook at Work una volta che l'accesso viene revocato in Azure AD.

Per impostare il provisioning automatico, fare clic su **Configura provisioning account ** nel portale di gestione di Azure.

Per altre informazioni su come configurare il provisioning automatico, vedere [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## Assegnazione di utenti a Facebook at Work

Per fare in modo che gli utenti di AAD di cui è stato effettuato il provisioning possano visualizzare Facebook at Work nel pannello di accesso, è necessario assegnare loro l'accesso nel portale di gestione di Azure.

**Per assegnare gli utenti a Facebook at Work:**

1.	Nella pagina iniziale di Facebook at Work nel portale di gestione di Azure fare clic su **Assegna account**.

2.	Nel menu **Mostra** scegliere se assegnare un utente o un gruppo a Facebook at Work e quindi fare clic sul pulsante con il segno di spunta.

3.	Nell'elenco visualizzato selezionare gli utenti o il gruppo a cui si vuole assegnare Facebook at Work.

4.	Fare clic sul pulsante **Assegna** nel piè di pagina.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png

<!---HONumber=Nov15_HO2-->