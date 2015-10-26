<properties
	pageTitle="Provisioning dell'app basato su attributi con filtri per la definizione dell'ambito"
	description="Informazioni su come usare i filtri per la definizione dell'ambito per evitare che venga eseguito il provisioning degli oggetti inclusi nelle app che supportano il provisioning automatico degli utenti se un oggetto non soddisfa i requisiti aziendali."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Provisioning dell'app basato su attributi con filtri per la definizione dell'ambito

Questa sezione spiega come usare i filtri per la definizione dell'ambito per definire regole basate su attributi per determinare gli utenti per i quali verrà eseguito il provisioning nell'applicazione.





## Clausole e gruppi di ambiti


![Filtro per la definizione dell’ambito][1]




I filtri per la definizione dell'ambito sono definiti da uno o più **gruppi di ambiti**, ognuno dei quali contiene una o più **clausole**. Per visualizzare le clausole per un gruppo di ambiti specifico, espanderlo facendo clic sulla freccia a sinistra del nome del gruppo.

Una **clausola** determina gli utenti che verranno restituiti dal filtro per la definizione dell'ambito valutando gli attributi di ogni utente. Ad esempio, se una clausola richiede che l'attributo 'state' di un utente sia uguale a New York, nell'applicazione verrà eseguito il provisioning solo degli utenti di New York.

![Nome del gruppo di ambiti][2]



Ogni **gruppo di ambiti** inizia con una **clausola** obbligatoria, come mostrato nella schermata. Questa clausola indica semplicemente che l'utente deve essere assegnato all'applicazione prima di essere valutato dai filtri per la definizione dell'ambito. Questa clausola non può essere eliminata né modificata.

Per aggiungere nuove clausole o nuovi gruppi di ambiti, fare clic sul pulsante corrispondente. Per assegnare un nome a ogni gruppo di ambiti, modificare la relativa proprietà **Nome del gruppo di ambiti**.





## Modalità di valutazione dei filtri per la definizione dell'ambito

Durante il provisioning, ogni utente assegnato viene verificato in base ai filtri per la definizione dell'ambito per stabilire se l'utente è autorizzato ad accedere all'applicazione. Si può pensare a una clausola come a un test che deve essere superato affinché l'utente non venga escluso.

Se sono stati definiti più gruppi di ambiti, ogni utente deve superarne almeno uno per poter accedere all'applicazione. All'interno di ogni gruppo di ambiti, tuttavia, l'utente deve superare ogni singola clausola per soddisfare i criteri del gruppo di ambiti specifico.

In altre parole, è come se i gruppi di ambiti fossero collegati tramite operatore OR e le clausole in essi contenute fossero collegate tramite operatore AND. Vedere ad esempio il filtro per la definizione dell'ambito seguente:


![Nome del gruppo di ambiti][2]


In base a questo filtro per la definizione dell'ambito, affinché sia possibile eseguire il provisioning degli utenti nell'applicazione, questi devono soddisfare i criteri seguenti:

1. Devono essere assegnati all'applicazione.

2. Devono lavorare nel reparto Engineering.

3. La sede di lavoro deve trovarsi a San Francisco o in Canada.


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png

<!---HONumber=Oct15_HO3-->