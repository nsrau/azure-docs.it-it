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
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: come registrare l'applicazione

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Prerequisito.

Per costruire un'applicazione che accetta l’iscrizione e l’accesso dell’utente, è innanzitutto necessario registrarla con una directory di Azure Active Directory B2C. Ottenere le directory utilizzando la procedura descritta [qui](active-directory-b2c-get-started.md). Dopo avere eseguito tutti i passaggi in questo articolo, è necessario disporre del pannello delle funzionalità B2C aggiunto alla schermata iniziale.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Passare al pannello delle funzionalità B2C.

È possibile spostarsi nel pannello delle funzionalità B2C in uno dei due modi seguenti.

### 1. Direttamente nel portale di anteprima di Azure:

Se si dispone del pannello delle funzionalità B2C aggiunto alla schermata iniziale, lo si vedrà non appena si accede al [portale di anteprima di Azure](https://portal.azure.com/) come amministratore generale della directory B2C.

È inoltre possibile accedere al pannello delle funzionalità B2C direttamente, passando a [https://portal.azure.com/ {directory}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com) dove **{directory}** deve essere sostituito con il nome utilizzato al momento della creazione di directory (ad esempio, contosob2c) e accedendo come amministratore generale della directory B2C. È possibile creare un segnalibro su questo collegamento per avere un riferimento futuro.

### 2. Accesso tramite il portale di Azure

Accedere al [portale di Azure](https://manage.windowsazure.com/) come amministratore della sottoscrizione (questo è lo stesso account di lavoro o scolastico o lo stesso Account Microsoft utilizzato per l'iscrizione ad Azure). Passare all'estensione di Active Directory a sinistra e fare clic sul nome della directory B2C. Nella scheda **Avvio rapido** (la prima visualizzata), fare clic su **Gestire impostazioni di B2C** in **Amministra**. Si aprirà così il pannello delle funzionalità di B2C in una nuova finestra o scheda del browser.

È inoltre possibile trovare il collegamento alle **impostazioni di gestione di B2C** (nella sezione **amministrazione B2C**) nella scheda **Configura**.

## Registrare un'applicazione

1. Nel pannello delle funzionalità B2C nel portale di anteprima di Azure, fare clic su **Applicazioni**.
2. Fare clic su **+Aggiungi** nella parte superiore del pannello.
3. Il **Nome** dell'applicazione deve essere una descrizione per gli utenti. Ad esempio, immettere "Contoso B2C app".
4. Se si sta scrivendo un'applicazione basata su web, commutare **Includi app Web o API Web** su **Sì**. Gli **URL di risposta** sono endpoint in cui AD B2C di Azure restituirà qualsiasi token richiesto dall'applicazione. Ad esempio, immettere `https://localhost:44321/`. Se l'applicazione include un componente lato server (API) che deve essere protetto, è opportuno creare (e copiare) un **Applicazione segreta** anche facendo clic sul tasto **Genera chiave**.

    > [AZURE.NOTE]
    **Applicazione Segreta**è una credenziale di sicurezza importante.

5. Se si sta scrivendo un'applicazione per dispositivi mobili, attivare o disattivare il **Includi native client** passare a **Sì**. Copia verso il basso il valore predefinito **URI di reindirizzamento** creato automaticamente per l'utente.
6. Fare clic su **Crea** per registrare l'applicazione.
7. Fare clic sull’applicazione appena creata e copiare l’**ID applicazione** univoca globale che verrà utilizzata successivamente nel codice.

## Creare un'applicazione di avvio rapido

Dopo aver creato un'applicazione registrata con AD B2C di Azure, è possibile completare una delle nostre esercitazioni di avvio rapido per l’ottenimento e l’esecuzione. Di seguito sono elencati alcuni suggerimenti:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!----HONumber=Oct15_HO1-->