---
title: Eseguire la migrazione a una risorsa di Azure per la creazione
titleSuffix: Azure Cognitive Services
description: Eseguire la migrazione a una chiave di risorsa di creazione di Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 6e1005e3d9c3769de3249f3244d65a656edc963e
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891746"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Eseguire la migrazione a una chiave di creazione delle risorse di Azure

Language Understanding (LUIS) la creazione dell'autenticazione è cambiata da un account di posta elettronica a una risorsa di Azure. Sebbene non sia attualmente necessario, il trasferimento a una risorsa di Azure verrà applicato in futuro.

## <a name="why-migrate"></a>Perché eseguire la migrazione?

L'uso di una risorsa di Azure per la creazione consente, come proprietario della risorsa, di controllare l'accesso alla creazione. È possibile creare e denominare le risorse di creazione per gestire diversi gruppi di autori.

Ad esempio, si è il proprietario di 2 app LUIS e si hanno membri diversi che sono collaboratori in ogni app. È possibile creare due diverse risorse di creazione e assegnare ogni app a ogni risorsa separata. Assegnare quindi ogni membro come collaboratore alla risorsa di creazione appropriata a seconda dell'app in cui collaborano. La risorsa di creazione di Azure controlla l'autorizzazione.

> [!Note]
> Prima della migrazione, i coautori sono noti come _collaboratori_ a livello di app Luis. Dopo la migrazione, il ruolo di _collaboratore_ di Azure viene usato per la stessa funzionalità, ma a livello di risorse di Azure.

## <a name="what-is-migrating"></a>Che cosa sta migrando?

La migrazione include:

* Tutti gli utenti di LUIS, proprietari e collaboratori.
* **Tutte le** app.
* Una migrazione **unidirezionale** .

Il proprietario non può scegliere un subset di app da migrare e il processo non è reversibile.

La migrazione non è:

* Processo che raccoglie collaboratori e si sposta o aggiunge automaticamente alla risorsa di creazione di Azure. Il proprietario dell'app deve completare questo passaggio. Questo passaggio richiede le autorizzazioni per la risorsa appropriata.
* Processo per la creazione e l'assegnazione di una risorsa di runtime di stima. Se è necessaria una risorsa di runtime di stima, si tratta [di un processo separato](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) che è invariato.

## <a name="how-are-the-apps-migrating"></a>In che modo vengono migrate le app?

Il [portale Luis](https://www.luis.ai) fornisce il processo di migrazione.

Verrà richiesto di eseguire la migrazione se:

* Sono disponibili app nel sistema di autenticazione della posta elettronica per la creazione.
* E il proprietario dell'app.

È possibile ritardare il processo di migrazione annullando la finestra. Viene richiesto periodicamente di eseguire la migrazione fino a quando non si esegue la migrazione o viene passata la scadenza della migrazione. È possibile avviare il processo di migrazione dall'icona di blocco della barra di spostamento superiore.

## <a name="migration-for-the-app-owner"></a>Migrazione per il proprietario dell'app

### <a name="before-you-migrate"></a>Prima della migrazione

* **Obbligatorio**, è necessario avere una [sottoscrizione di Azure](https://azure.microsoft.com/free/). Una parte del processo di sottoscrizione richiede informazioni sulla fatturazione. Tuttavia, è possibile usare il piano tariffario gratuito (`F0`) quando si usa LUIS.
* **Facoltativamente**, eseguire il backup delle app dall'elenco di app del portale Luis esportando ogni app o usando l' [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)di esportazione.
* **Facoltativamente**, salvare l'elenco collaborator's di ogni app. Questo elenco di messaggi di posta elettronica viene fornito come parte del processo di migrazione.


La creazione di un' **app Luis è gratuita**, indicata dal livello `F0`. [Altre informazioni sui piani tariffari](luis-boundaries.md#key-limits).

Se non si ha una sottoscrizione di Azure, [iscriversi](https://azure.microsoft.com/free/).

### <a name="migration-steps"></a>Passaggi della migrazione

Seguire [questa procedura di migrazione](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Dopo la migrazione

Al termine del processo di migrazione, tutte le app LUIS sono ora assegnate a una singola risorsa di authoring LUIS.

È possibile creare altre risorse di creazione e assegnazione dalla pagina **Gestisci-> risorse di Azure** nel _portale Luis_.

È possibile aggiungere collaboratori alla risorsa di creazione dalla _portale di Azure_nella pagina **controllo di accesso (IAM)** per la risorsa. Per ulteriori informazioni, vedere [aggiungere l'accesso al collaboratore](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource) .

|di Microsoft Azure|Finalità|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Creare le risorse di stima e creazione.<br>* Assegnare collaboratori.|
|[LUIS](https://www.luis.ai)|* Eseguire la migrazione alle nuove risorse di creazione.<br>* Assegnare o annullare l'assegnazione delle risorse di stima e creazione alle app dalla pagina **Gestisci-> risorse di Azure** .|

## <a name="migration-for-the-app-contributor"></a>Migrazione per il collaboratore app

Ogni utente di LUIS deve eseguire la migrazione, inclusi collaboratori/collaboratori. Un collaboratore deve eseguire la migrazione per poter accedere all'app.

> [!Note]
> Se il proprietario dell'app LUIS ha eseguito la migrazione e ha aggiunto il collaboratore come collaboratore nella risorsa di Azure, il collaboratore non potrà ancora accedere all'app, a meno che non ne venga eseguita la migrazione.

### <a name="before-the-app-is-migrated"></a>Prima della migrazione dell'app

È possibile scegliere di esportare un'app a cui si è collaboratori, quindi importare di nuovo l'app in LUIS. Il processo di importazione crea una nuova app con un nuovo ID app, di cui si è proprietari.

### <a name="after-the-app-is-migrated"></a>Dopo la migrazione dell'app

Il proprietario dell'app deve [aggiungere la posta elettronica alla risorsa di creazione di Azure come collaboratore](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource).

Dopo il processo di migrazione, le app di cui si è proprietari sono disponibili nella pagina **app personali** del portale Luis.

## <a name="troubleshooting"></a>risoluzione dei problemi

* Le chiavi di authoring LUIS sono visibili solo nel portale LUIS al termine del processo di migrazione. Se si creano le chiavi di creazione, ad esempio con l'interfaccia della riga di comando LUIS, l'utente deve comunque completare il processo di migrazione.
* Se un utente migrato aggiunge un utente non migrato come collaboratore nella propria risorsa di Azure, l'utente non migrato non avrà accesso alle app a meno che non ne venga eseguita la migrazione.
* Se un utente non migrato non è un proprietario di alcuna app, ma è un collaboratore ad altre app di proprietà di altri e i proprietari hanno subito il processo di migrazione, l'utente dovrà eseguire la migrazione per poter accedere alle app.
* Se un utente non migrato ha aggiunto un altro utente migrato come collaboratore alla propria app, si verificherà un errore in quanto non sarà possibile aggiungere un utente migrato come collaboratore a un'app. L'utente non migrato dovrà quindi eseguire il processo di migrazione e creare una risorsa di Azure e aggiungere l'utente migrato come collaboratore a tale risorsa.

## <a name="next-steps"></a>Passaggi successivi

* [Come eseguire la migrazione dell'app a una risorsa di creazione](luis-migration-authoring-steps.md)
