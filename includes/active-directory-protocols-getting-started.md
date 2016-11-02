<properties
	pageTitle="Panoramica del protocollo .NET per Azure AD | Microsoft Azure"
	description="Come usare messaggi HTTP per autorizzare l'accesso ad applicazioni Web e API Web nel proprio tenant con Azure Active Directory."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

<!--TODO: Introduction -->

## Registrare l'applicazione nel tenant di Active Directory

Prima di tutto è necessario registrare l'applicazione nel tenant di Active Directory. Verrà fornito un ID client per l'applicazione, abilitandola inoltre per ricevere token.

- Accedere al portale di gestione di Azure.

- Nel riquadro di spostamento a sinistra fare clic su **Active Directory**.

- Selezionare un tenant in cui registrare l'applicazione.

- Fare clic sulla scheda **Applicazioni**, quindi fare clic su **Aggiungi** nel menu in basso.

- Seguire le istruzioni e creare una nuova applicazione. Per questa esercitazione non è rilevante che si tratti di un'applicazione Web o un'applicazione nativa, ma per esempi specifici per le applicazioni Web o native, consultare le guide introduttive [qui](../articles/active-directory/active-directory-developers-guide.md).

- Per le applicazioni Web fornire l'**URL accesso**, ovvero l'URL di base dell'app con cui gli utenti possono accedere, ad esempio `http://localhost:12345`. L'**URI ID app** è un identificatore univoco dell'applicazione. Per convenzione si usa `https://<tenant-domain>/<app-name>`, ad esempio `https://contoso.onmicrosoft.com/my-first-aad-app`.

- Per le applicazioni native fornire un **URI di reindirizzamento**, che Azure AD userà per restituire le risposte dei token. Immettere un valore specifico per l'applicazione in uso, ad esempio `http://MyFirstAADApp`.

- Dopo avere completato la registrazione, AAD assegnerà all'applicazione un identificatore client univoco. Questo valore sarà necessario nelle sezioni successive, quindi copiarlo nella scheda **Configura** dell'applicazione.

<!---HONumber=AcomDC_0601_2016-->