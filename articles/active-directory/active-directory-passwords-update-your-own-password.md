<properties
	pageTitle="Procedura: Registrarsi per reimpostare la password di Azure AD | Microsoft Azure"
	description="Informazioni su come effettuare la registrazione per reimpostare la password in Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="asteen"/>  

# Mantenere l'accesso all'account

> [AZURE.IMPORTANT]
**Perché viene visualizzata questa pagina?** Se si è selezionato un collegamento, è probabile che questa pagina sia stata visualizzata perché l'amministratore richiede che venga eseguita la registrazione per la reimpostazione della password per ottenere l'accesso all'app. Potrebbero essere richieste informazioni relative al numero di telefono o all'indirizzo di posta elettronica oppure l'impostazione di domande di sicurezza. Queste informazioni non verranno usate per l'invio di posta indesiderata, ma solo per mantenere più sicuro l'account. La procedura qui illustrata consentirà di raggiungere l'obiettivo.

Il modo più rapido per registrarsi per la reimpostazione della password è visitare la pagina [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup).

 1. Passare a [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup).
 2. Immettere nome utente e password.
 3. Scegliere un'opzione per la registrazione facendo clic su **Imposta ora**. In questo caso viene illustrata la registrazione del **Telefono per l'autenticazione**.

    ![][011]

 4. Selezionare il codice paese nell'elenco a discesa e immettere il **numero di telefono esteso + indicativo di località**.

    ![][012] ![][013]

 5. Selezionare una opzione tra **Invia messaggio di testo** e **Chiama**. In questo caso viene selezionato **Invia messaggio di testo**. In questo modo verrà inviato un codice di 6 cifre al numero di telefono indicato. Attendere la ricezione del codice sul telefono.

    ![][014]  

 6. Immettere il codice ricevuto nella casella di input e fare clic su "Verifica".
 7. Quando viene visualizzato il messaggio **Grazie**, l'operazione è terminata. Ora è possibile usare i dati ottenuti con la registrazione per reimpostare la password in qualsiasi momento, visitando il sito [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).

    ![][015]

 >[AZURE.IMPORTANT] Se l'amministratore consente di registrarsi per più di una opzione, è consigliabile registrare anche un'opzione di backup nel caso in cui si perda il telefono o non sia possibile accedere alla posta elettronica.

## Altri metodi per aggiornare la password
Di seguito viene descritto come eseguire le operazioni più comuni, ad esempio modificare o reimpostare una password.

* [**Come modificare la password da Office 365**](active-directory-passwords-update-methods.md#how-to-change-your-password-from-o365)
* [**Come modificare la password dal pannello di accesso**](active-directory-passwords-update-methods.md#how-to-change-your-password-from-the-access-panel)
* [**Come reimpostare la password**](active-directory-passwords-update-methods.md#how-to-reset-your-password)
* [**Come sbloccare l'account**](active-directory-passwords-update-methods.md#how-to-unlock-your-account)
* [**Problemi frequenti e relative soluzioni**](active-directory-passwords-update-methods.md#common-problems-and-their-solutions)

## Collegamenti alla documentazione relativa alla reimpostazione della password
Di seguito elencati i collegamenti a tutte le pagine di documentazione sulla gestione della password in Azure AD:

* [**Funzionamento**](active-directory-passwords-how-it-works.md): informazioni sui sei diversi componenti del servizio e sulle relative funzioni
* [**Introduzione**](active-directory-passwords-getting-started.md): informazioni su come consentire agli utenti di reimpostare e modificare le password cloud o locali
* [**Personalizzazione**](active-directory-passwords-customize.md): informazioni su come personalizzare l'aspetto e il comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md): informazioni su come distribuire rapidamente e gestire in modo efficace le password nell'organizzazione
* [**Informazioni dettagliate**](active-directory-passwords-get-insights.md): informazioni sulle funzionalità di creazione report integrate
* [**Domande frequenti**](active-directory-passwords-faq.md): risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere rapidamente eventuali problemi con il servizio
* [**Ulteriori informazioni**](active-directory-passwords-learn-more.md): approfondimenti sui dettagli tecnici del funzionamento del servizio



[001]: ./media/active-directory-passwords-update-your-own-password/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-update-your-own-password/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-update-your-own-password/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-update-your-own-password/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-update-your-own-password/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-update-your-own-password/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-update-your-own-password/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-update-your-own-password/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-update-your-own-password/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-update-your-own-password/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-update-your-own-password/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-update-your-own-password/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-update-your-own-password/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-update-your-own-password/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-update-your-own-password/015.jpg "Image_015.jpg"

<!---HONumber=AcomDC_0824_2016-->