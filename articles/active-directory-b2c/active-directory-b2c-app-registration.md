<properties
	pageTitle="Anteprima di Azure Active Directory B2C: registrazione dell'applicazione | Microsoft Azure"
	description="Come registrare l'applicazione con Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/25/2016"
	ms.author="swkrish"/>


# Anteprima di Azure Active Directory B2C: Registrare l'applicazione

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Prerequisito

Per compilare un'applicazione che accetta l'iscrizione e l'accesso dell'utente, è necessario innanzi tutto registrarla con tenant di Azure Active Directory B2C. Per ottenere il tenant, seguire la procedura illustrata in [Creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md). Dopo avere eseguito tutti i passaggi nell'articolo, si disporrà del pannello delle funzionalità B2C aggiunto alla schermata iniziale.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Passare al pannello delle funzionalità B2C

È possibile passare al pannello delle funzionalità B2C dal portale di Azure o dal portale di Azure classico.

### 1\. Accedere al portale di Azure

Se il pannello delle funzionalità B2C è stato aggiunto alla schermata iniziale, lo si vedrà non appena si accede al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant B2C.

È inoltre possibile accedere al pannello facendo clic su **Sfoglia** e quindi su **Azure AD B2C** nel riquadro di spostamento sinistro nel [portale di Azure](https://portal.azure.com/).

È inoltre possibile accedere al pannello direttamente, passando a [https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/) dove **{tenant}** deve essere sostituito con il nome usato al momento della creazione del tenant, ad esempio contosob2c. È possibile creare un segnalibro su questo collegamento per l'uso futuro.

    > [AZURE.IMPORTANT]
    You need to be a Global Administrator of the B2C tenant to be able to access the B2C features blade. A Global Administrator from any other tenant or a user from any tenant cannot access it.

### 2\. Accesso nel portale di Azure classico

Accedere al [portale di Azure classico](https://manage.windowsazure.com/) come amministratore della sottoscrizione. Si tratta dello stesso account aziendale o dell'istituto d'istruzione o dello stesso account Microsoft usato per l'iscrizione ad Azure. Passare all'estensione di Active Directory a sinistra e fare clic sul nome del tenant B2C. Nella scheda **Avvio rapido** (la prima visualizzata), fare clic su **Gestione impostazioni B2C** in **Amministra**. Si aprirà il pannello delle funzionalità di B2C in una nuova finestra o scheda del browser.

È inoltre possibile trovare il collegamento **Gestione impostazioni B2C** nella sezione **Amministrazione B2C** della scheda **Configura**.

## Registrare un'applicazione

1. Nel pannello delle funzionalità B2C nel portale di Azure fare clic su **Applicazioni**.
2. Fare clic su **+Aggiungi** nella parte superiore del pannello.
3. Immettere un **Nome** per l'applicazione che descriva l'applicazione per gli utenti. Ad esempio, immettere "App Contoso B2C".
4. Se si sta scrivendo un'applicazione basata su web, commutare **Includi app Web o API Web** su **Sì**. Gli **URL di risposta** sono gli endpoint a cui Azure AD B2C restituirà qualsiasi token richiesto dall'applicazione. Ad esempio, immettere `https://localhost:44321/`. Se l'applicazione include un componente lato server (API) che deve essere protetto, sarà opportuno creare (e copiare) anche una **chiave privata dell'applicazione** facendo clic sul pulsante **Rigenera chiave**.

    > [AZURE.NOTE]
    La **chiave privata dell'applicazione** è una credenziale di sicurezza importante.

5. Se si sta scrivendo un'applicazione per dispositivi mobili, impostare **Includi client nativo** su **Sì**. Copiare l'**URI di reindirizzamento** predefinito creato automaticamente.
6. Fare clic su **Crea** per registrare l'applicazione.
7. Fare clic sull'applicazione appena creata e copiare l'**ID applicazione** univoco globale che verrà usato in seguito nel codice.

## Creare un'applicazione di avvio rapido

Dopo aver creato un'applicazione registrata con Azure AD B2C, è possibile completare una delle esercitazioni di avvio rapido per essere subito operativi. Di seguito sono elencati alcuni suggerimenti:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0302_2016-->