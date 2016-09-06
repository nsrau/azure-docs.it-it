<properties
	pageTitle="Azure Active Directory B2C: registrazione dell'applicazione | Microsoft Azure"
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
	ms.date="08/30/2016"
	ms.author="swkrish"/>  


# Azure Active Directory B2C: registrare l'applicazione

## Prerequisito

Per compilare un'applicazione che accetta l'iscrizione e l'accesso dell'utente, è necessario innanzi tutto registrarla con tenant di Azure Active Directory B2C. Per ottenere il tenant, seguire la procedura illustrata in [Azure Active Directory B2C: creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md). Dopo avere eseguito tutti i passaggi nell'articolo, si disporrà del pannello delle funzionalità B2C aggiunto alla schermata iniziale.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Passare al pannello delle funzionalità B2C

Se il pannello delle funzionalità B2C è stato aggiunto a Schermata iniziale, verrà visualizzato appena si accede al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant B2C.

È possibile accedere al pannello anche facendo clic su **Esplora** e quindi su **Azure AD B2C** nel riquadro di spostamento a sinistra nel [portale di Azure](https://portal.azure.com/).

> [AZURE.IMPORTANT]  È necessario avere diritti di amministratore globale del tenant B2C per poter accedere al pannello delle funzionalità B2C. Un amministratore globale o un utente di qualsiasi altro tenant non può accedere. È possibile passare al tenant B2C usando l'apposito controllo nell'angolo superiore destro del portale di Azure.

## Registrare un'applicazione

1. Nel pannello delle funzionalità B2C nel portale di Azure fare clic su **Applicazioni**.
2. Fare clic su **+Aggiungi** nella parte superiore del pannello.
3. Immettere un **nome** per l'applicazione che descriva l'applicazione agli utenti. Ad esempio, immettere "App Contoso B2C".
4. Se si sta scrivendo un'applicazione basata su web, commutare **Includi app Web o API Web** su **Sì**. Gli **URL di risposta** sono gli endpoint a cui Azure AD B2C restituirà qualsiasi token richiesto dall'applicazione. Ad esempio, immettere `https://localhost:44321/`. Se l'applicazione Web chiamerà un'API Web protetta da Azure AD B2C, è opportuno creare un **segreto dell'applicazione** facendo clic sul pulsante **Genera chiave**.

    > [AZURE.NOTE] Un **segreto dell'applicazione** è una credenziale di sicurezza importante e deve essere protetto in modo appropriato.

5. Se si sta scrivendo un'applicazione per dispositivi mobili, impostare **Includi client nativo** su **Sì**. Copiare l'**URI di reindirizzamento** predefinito creato automaticamente.
6. Fare clic su **Crea** per registrare l'applicazione.
7. Fare clic sull'applicazione appena creata e copiare l'**ID client applicazione** univoco globale che verrà usato in seguito nel codice.

> [AZURE.IMPORTANT] Le applicazioni create nel pannello delle funzionalità B2C devono essere gestite nella stessa posizione. Le applicazioni B2C, se vengono modificate usando PowerShell o un altro portale, non sono più supportate ed è probabile che non funzionino con Azure AD B2C.

## Creare un'applicazione di avvio rapido

Dopo aver creato un'applicazione registrata con Azure AD B2C, è possibile completare una delle esercitazioni di avvio rapido per essere subito operativi. Di seguito sono elencati alcuni suggerimenti:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0831_2016-->