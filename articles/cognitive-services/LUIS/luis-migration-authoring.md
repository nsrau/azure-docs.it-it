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
ms.date: 09/08/2019
ms.author: diberry
ms.openlocfilehash: ee185ca5d2e2cdda89945f0fc1e7aae32030c1be
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803904"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Eseguire la migrazione a una chiave di creazione delle risorse di Azure

Language Understanding (LUIS) la creazione dell'autenticazione è cambiata da un account di posta elettronica a una risorsa di Azure. 

## <a name="why-migrate"></a>Perché eseguire la migrazione?

L'uso di una risorsa di Azure per la creazione consente, come proprietario della risorsa, di controllare l'accesso alla creazione. È possibile creare e denominare le risorse di creazione per gestire diversi gruppi di autori. 

Ad esempio, se si dispone di 2 tipi di app LUIS che si sta creando con membri diversi, è possibile creare due diverse risorse di creazione e assegnare collaboratori. La risorsa di creazione di Azure controlla l'autorizzazione. 

> [!Note]
> Prima della migrazione, i coautori sono noti come _collaboratori_. Dopo la migrazione, il ruolo di _collaboratore_ di Azure viene usato per la stessa funzionalità.

## <a name="what-is-migrating"></a>Che cosa sta migrando?

La migrazione include:

* **Tutte** le app del proprietario.
* Una migrazione **unidirezionale** .

Il proprietario non può scegliere un subset di app da migrare e il processo non è reversibile. 

La migrazione non è: 

* Processo che raccoglie collaboratori e si sposta o aggiunge automaticamente alla risorsa di creazione di Azure. Il proprietario dell'app deve completare questo passaggio. Questo passaggio richiede le autorizzazioni per la risorsa appropriata.
* Processo per la creazione e l'assegnazione di una risorsa di runtime di stima. Se è necessaria una risorsa di runtime di stima, si tratta [di un processo separato](luis-how-to-azure-subscription.md##create-resources-in-the-azure-portal) che è invariato. 

## <a name="how-are-the-apps-migrating"></a>In che modo vengono migrate le app?

Il [portale Luis](https://www.luis.ai) fornisce il processo di migrazione. 

Verrà richiesto di eseguire la migrazione se:

* Sono disponibili app nel sistema di autenticazione della posta elettronica per la creazione.
* E il proprietario dell'app. 

È possibile ritardare il processo di migrazione, annullando la finestra. Viene richiesto periodicamente di eseguire la migrazione fino a quando non si esegue la migrazione o viene passata la scadenza della migrazione. È possibile avviare il processo di migrazione dall'icona di blocco della barra di spostamento superiore.

## <a name="migration-for-the-app-owner"></a>Migrazione per il proprietario dell'app

### <a name="before-you-migrate"></a>Prima della migrazione

* **Facoltativamente**, eseguire il backup delle app dall'elenco di app del portale Luis esportando ogni app o usando l' [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)di esportazione.
* **Facoltativamente**, salvare l'elenco collaborator's di ogni app. Questo elenco di messaggi di posta elettronica viene fornito come parte del processo di migrazione.
* **Obbligatorio**, è necessario avere una [sottoscrizione di Azure](https://azure.microsoft.com/free/). Una parte del processo di sottoscrizione richiede informazioni sulla fatturazione. È tuttavia possibile usare piani tariffari gratuiti (F0) quando si usa LUIS. È possibile che sia necessario un livello a pagamento, a mano a mano che aumenta l'utilizzo. 

Se non si ha una sottoscrizione di Azure, [iscriversi](https://azure.microsoft.com/free/). 

### <a name="migration-steps"></a>Passaggi della migrazione

Seguire [questa procedura di migrazione](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Dopo la migrazione 

Al termine del processo di migrazione, tutte le app LUIS sono ora assegnate a una singola risorsa di authoring LUIS.

È possibile creare altre risorse di creazione e assegnazione dalla pagina **Gestisci-> risorse di Azure** nel _portale Luis_. 

È possibile aggiungere collaboratori alla risorsa di creazione dalla _portale di Azure_nella pagina **controllo di accesso (IAM)** per la risorsa. Per ulteriori informazioni, vedere [aggiungere l'accesso al collaboratore](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource) .

|Portale|Scopo|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Creare le risorse di stima e creazione.<br>* Assegnare collaboratori.|
|[LUIS](https://www.luis.ai)|* Eseguire la migrazione alle nuove risorse di creazione.<br>* Assegnare o annullare l'assegnazione delle risorse di stima e creazione alle app dalla pagina **Gestisci-> risorse di Azure** .| 

## <a name="migration-for-the-app-contributor"></a>Migrazione per il collaboratore app

### <a name="before-the-app-is-migrated"></a>Prima della migrazione dell'app

È possibile scegliere di esportare un'app a cui si è collaboratori, quindi importare di nuovo l'app in LUIS. Il processo di importazione crea una nuova app con un nuovo ID app, di cui si è proprietari.

### <a name="after-the-app-is-migrated"></a>Dopo la migrazione dell'app

Dopo il processo di migrazione, se è necessario accedere all'app originale, è necessario essere aggiunti alla risorsa di creazione di Azure come collaboratore dal proprietario dell'app.  

## <a name="next-steps"></a>Passaggi successivi

* [Come eseguire la migrazione dell'app a una risorsa di creazione](luis-migration-authoring-steps.md)