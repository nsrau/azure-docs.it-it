<properties
	pageTitle="Modello app 2.0 | Microsoft Azure"
	description="Come registrare un'app con Microsoft per l'abilitazione dell'accesso e l'integrazione delle app con Modello app 2.0."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="dastrock"/>

# Anteprima di Modello app 2.0: Come registrare un'app con Microsoft

Per creare un'app che consente di accedere tramite account Microsoft e Azure AD, è innanzitutto necessario registrare un'app con Microsoft. Con gli account Azure AD o Microsoft non sarà possibile usare un'app esistente, ma è necessario crearne una nuova.

> [AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

## Visitare il portale di registrazione delle app Microsoft
Accedere innanzitutto al sito Web all'indirizzo [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com). Questo è il nuovo portale di registrazione delle app in cui è possibile gestire qualsiasi aspetto delle app Microsoft.

Accedere con un account Microsoft personale, aziendale o dell'istituto di istruzione. Se non si dispone di alcun account, iscriversi per creare un nuovo account personale. La procedura richiederà pochi minuti.

Dopo aver completato l'operazione, è possibile esaminare l'elenco delle app Microsoft, che probabilmente sarà vuoto. A tale scopo, seguire questa procedura.

<!-- TODO: Verify strings here -->
Fare clic sul pulsante per **aggiungere un'app** e attribuirle un nome. Il portale assegnerà all'app un ID applicazione univoco globale che verrà usato successivamente nel codice. Se l'app include un componente sul lato server che necessita di token di accesso per chiamare le API (ad esempio, Office, Azure o un componente di terze parti), è opportuno creare anche un **segreto dell'applicazione**. <!-- TODO: Link for app secrets -->

Aggiungere quindi le piattaforme usate dall'app. Per le app basate sul web, specificare un **URI di reindirizzamento** a cui è possibile inviare i messaggi di accesso. Per le app per dispositivi mobili, copiare l'URI di reindirizzamento predefinito generato automaticamente.

Facoltativamente, è possibile personalizzare l'aspetto della pagina di accesso nella sezione del profilo. Assicurarsi di **salvare** prima di procedere.

## Creare un'app di avvio rapido
Dopo aver creato un'app Microsoft, è possibile completare una delle esercitazioni rapide per iniziare a usare Modello app 2.0. Di seguito sono elencati alcuni suggerimenti:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=Nov15_HO3-->