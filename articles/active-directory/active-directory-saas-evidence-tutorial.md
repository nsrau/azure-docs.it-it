<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Evidence.com | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Evidence.com."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="femila"
	editor=""/>  

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="asmalser"/>


# Esercitazione: Integrazione di Azure Active Directory con Evidence.com

Questa esercitazione mostra come configurare l'accesso Single Sign-On tra Azure Active Directory (AAD) ed Evidence.com. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:
	
* Sottoscrizione valida di Microsoft Azure
* Sottoscrizione di Evidence.com con accesso Single Sign-On abilitato. Se l'accesso Single Sign-On basato su SAML non è abilitato, inviare un messaggio di posta elettronica a earlyaccess@evidence.com.

Al termine dell'esercitazione, gli utenti di AAD a cui è stato assegnato l'accesso Evidence.com potranno accedere all'applicazione usando il Pannello di accesso di AAD.

## Aggiungere Evidence.com alla directory

Questa sezione indica come aggiungere Evidence.com come applicazione integrata in Azure Active Directory.

**Per abilitare l'integrazione dell'applicazione per Evidence:**

1.	Nel [portale di Azure classico](https://manage.windowsazure.com) fare clic su **Active Directory** nel riquadro di spostamento sinistro.

2.	Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3.	Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

4.	Per aprire la Raccolta di applicazioni fare clic su **Aggiungi** e quindi su **Aggiungere un'applicazione dalla raccolta**.

5.	Nella casella di ricerca digitare **Evidence.com**.

6.	Nel riquadro dei risultati selezionare **Evidence.com** e quindi fare clic su **Completa** per aggiungere l'applicazione.


## Configurazione dell'accesso Single Sign-On

Questa sezione descrive come permettere agli utenti di eseguire l'autenticazione a Evidence.com con il proprio account di Azure Active Directory usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1.	Dopo aver aggiunto Evidence.com nel portale di Azure classico, fare clic su **Configura accesso Single Sign-On**.
 
2.	Nella schermata successiva selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

3.	Nella schermata Configura URL immettere l'URL che verrà digitato dagli utenti per accedere al tenant Evidence.com, ad esempio https://yourtenant.evidence.com , quindi fare clic su **Avanti**.

4.	Fare clic sul collegamento **Scarica certificato** e salvare il file nell'unità locale. Questo certificato e gli URL dei metadati (ID entità, URL di accesso SSO e URL di disconnessione SSO) verranno usati per configurare l'accesso Single Sign-On nel sito Evidence.com.

5.	In una finestra del separata del Web browser accedere al tenant Evidence.com come amministratore e passare alla scheda **Admin**.
      
6.	Fare clic su **Agency Single Sign On**.
 
7.	Selezionare **SAML Based Single Sign On**.
 
8.	Copiare i valori **URL autorità di certificazione**, **Single Sign-On** e **Single Sign-Out** visualizzati nel portale di Azure classico nei campi corrispondenti in Evidence.com.

9.	Aprire il certificato scaricato nel passaggio 4 usando un editor di testo, come Notepad.exe, quindi copiare e incollare i contenuti nella casella **Security Certificate**.

10. Salvare la configurazione in Evidence.com.
 
11.	Nel portale di Azure classico selezionare l'opzione **Confermare che Single Sign-On è stato configurato come descritto in precedenza**. Selezionando questa opzione si abilita la casella di controllo per l'utilizzo del certificato corrente per questa applicazione.
 
12.	Nella pagina Conferma Single Sign-on fare clic su **Completa**.


## Creazione di un utente test per Evidence.com

Per consentire agli utenti di Azure AD di accedere, è necessario effettuarne il provisioning per l'accesso nell'applicazione Evidence.com. Questa sezione descrive come creare account utente di Azure AD in Evidence.com.

**Per effettuare il provisioning di un account utente in Evidence.com:**

1.	In una finestra del Web browser accedere al sito aziendale Evidence.com come amministratore.

2.	Passare alla scheda **Admin**.

3.	Fare clic su **Add User**.

4.	Fare clic su **Add**.

5.  Il valore per **Email Address** dell'utente aggiunto deve corrispondere al nome utente degli utenti in Azure AD a cui si vuole concedere l'accesso. Se il nome utente e l'indirizzo di posta elettronica dell'organizzazione sono diversi, è possibile usare la sezione **Evidence.com > Attributi > Single Sign-On** del portale di Azure classico per cambiare il valore nameidenitifer inviato a Evidence.com in modo che venga usato l'indirizzo di posta elettronica.


## Assegnazione di utenti a Evidence.com

Per fare in modo che gli utenti di AAD di cui è stato effettuato il provisioning possano visualizzare Evidence.com nel pannello di accesso, è necessario assegnare loro l'accesso nel portale di Azure classico.

**Per assegnare utenti a Evidence.com:**

1.	Nella pagina di avvio rapido per Evidence.com nel portale di Azure classico fare clic su **Assegna utenti a Evidence.com**.
 
2.	Nel menu **Mostra** scegliere se assegnare un utente o un gruppo a Evidence.com e quindi fare clic sul pulsante con il segno di spunta.
 
3.	Nell'elenco **Utenti** selezionare gli utenti o il gruppo a cui si vuole assegnare Evidence.com.
 
4.	Fare clic sul pulsante **Assegna** nel piè di pagina.

<!---HONumber=AcomDC_0928_2016-->