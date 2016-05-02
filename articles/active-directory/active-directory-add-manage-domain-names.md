<properties
	pageTitle="Gestione dei nomi di dominio personalizzati in Azure Active Directory | Microsoft Azure"
	description="Concetti relativi alla gestione e procedure dettagliate per gestire un dominio personalizzato in Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# Gestione dei nomi di dominio personalizzati in Azure Active Directory

Un nome di dominio è una parte importante dell'identificatore per numerose risorse directory: è parte di un nome utente o di un indirizzo di posta elettronica per un utente, parte dell'indirizzo per un gruppo e può essere parte dell'URI dell'ID app per un'applicazione. Una risorsa in Azure Active Directory (Azure AD) può includere un nome di dominio già verificato come di proprietà della directory contenente il servizio. Solo un amministratore globale può eseguire attività di gestione del dominio in Azure AD.

## Impostare il nome di dominio primario per la directory di Azure AD

Quando viene creata la directory, il nome di dominio iniziale, ad esempio 'contoso.onmicrosoft.com', è anche il nome di dominio primario per la directory. Il dominio primario è il nome di dominio predefinito per un nuovo utente quando viene creato nel [portale di Azure classico](https://manage.windowsazure.com/) o in altri portali, ad esempio il portale dell'amministratore di Office 365. Questo contribuisce a semplificare il processo di creazione dei nuovi utenti per gli amministratori.

Per cambiare il nome di dominio primario per la directory:

1.  Accedere al [portale di Azure classico](https://manage.windowsazure.com/) con un account utente amministratore globale per la directory di Azure AD.

2.  Selezionare **Active Directory** sulla barra di spostamento a sinistra.

3.  Aprire la directory.

4.  Selezionare la scheda **Domini**.

5.  Fare clic sul pulsante **Modifica primario** sulla barra dei comandi.

6.  Selezionare il dominio da impostare come nuovo dominio primario per la directory.

Per il nome di dominio primario per la directory è possibile impostare qualsiasi dominio personalizzato verificato che non sia federato. La modifica del dominio primario per la directory non comporta la modifica dei nomi utente per gli utenti esistenti.

## Aggiungere nomi di dominio personalizzati alla directory di Azure AD

È possibile aggiungere fino a 900 nomi di dominio personalizzati a ogni directory di Azure AD. Per [aggiungere altri nomi di dominio personalizzati](active-directory-add-domain.md) è sufficiente seguire la stessa procedura usata per aggiungere il primo nome di dominio personalizzato.

## Aggiungere sottodomini di un dominio personalizzato

Per aggiungere un nome di dominio di terzo livello, ad esempio 'europe.contoso.com' alla directory, è prima necessario aggiungere e verificare il dominio di secondo livello, ovvero contoso.com. Il sottodominio verrà automaticamente verificato da Azure AD. Per ottenere la conferma dell'avvenuta verifica del dominio appena aggiunto, aggiornare la pagina del browser in cui sono visualizzati i domini della directory.

## Cosa fare se si modifica il registrar DNS per il nome di dominio personalizzato

Se si modifica il registrar DNS per il nome di dominio personalizzato, è possibile continuare a usare il nome di dominio personalizzato in Azure AD senza interruzioni e senza ulteriori attività di configurazione. Se si usa il nome di dominio personalizzato con Office 365, Intune o altri servizi che si basano sui nomi di dominio personalizzati in Azure AD, vedere la documentazione per tali servizi.

## Eliminare un nome di dominio personalizzato

È possibile eliminare da Azure AD i nomi di dominio personalizzati che l'organizzazione non usa più o che vuole usare in un'altra directory di Azure AD.

Per eliminare un nome di dominio personalizzato, è prima necessario assicurarsi che nella directory non siano presenti risorse che si basano sul nome di dominio. Non è possibile eliminare un nome di dominio dalla directory nei casi seguenti:

-   Sono presenti utenti con un nome utente, un indirizzo di posta elettronica o un indirizzo proxy che include il nome di dominio.

-   Sono presenti gruppi con un indirizzo di posta elettronica o un indirizzo proxy che include il nome di dominio.

-   In Azure AD sono presenti applicazioni con un URI di ID app che include il nome di dominio.

È necessario modificare o eliminare tali risorse dalla directory di Azure AD prima di poter eliminare il nome di dominio personalizzato.

## Usare API Graph o PowerShell per gestire i nomi di dominio

La maggior parte delle attività di gestione relative ai nomi di dominio in Azure Active Directory può anche essere eseguita usando Microsoft PowerShell oppure a livello di codice usando l'API Graph (versione di anteprima pubblica).

-   [Uso di PowerShell per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Uso dell'API Graph per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

**Passaggi successivi**

-   [Informazioni sui nomi di dominio in Azure AD](active-directory-add-domain-concepts.md)

-   [Gestire i nomi di dominio personalizzati](active-directory-add-manage-domain-names.md)

<!---HONumber=AcomDC_0420_2016-->