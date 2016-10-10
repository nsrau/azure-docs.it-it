<properties
	pageTitle="Azure AD e le applicazioni: assegnazione dei gruppi a un'applicazione | Microsoft Azure"
	description="Come implementare l'assegnazione di gruppo per le applicazioni Azure."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="femila"
	editor=""/>  

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/03/2015"
	ms.author="inhenk"/>

# Azure AD e le applicazioni: assegnazione dei gruppi a un'applicazione
Prima di assegnare utenti e gruppi a un'applicazione, è necessario richiedere l'assegnazione dell’utente. Per informazioni su come richiedere l'assegnazione dell'utente, vedere l'articolo [Richiedere l'assegnazione utente](active-directory-applications-guiding-developers-requiring-user-assignment.md).

In questo articolo si presuppone che siano già stati creati gruppi nella active directory che si utilizza per questa applicazione.

## Assegnazione dei gruppi a un'applicazione
1. Accedere al portale di Azure con un account amministratore.
2. Fare clic sulla voce **Tutti gli elementi** del menu principale.
3. Scegliere la directory utilizzata per l'applicazione.
4. Fare clic sulla scheda **APPLICAZIONI**.
5. Selezionare l'applicazione dall'elenco di applicazioni associate a questa directory.
6. Fare clic sulla scheda **UTENTI E GRUPPI**.
7. Filtrare l'elenco dei gruppi nella active directory utilizzando l’elenco a discesa **Gruppi**.
8. Selezionare il gruppo.
9. Fare clic su **ASSEGNARE**.
10. Fare clic su **sì** quando richiesto.

## Passaggi successivi
[AZURE.INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]

<!---HONumber=AcomDC_0928_2016-->