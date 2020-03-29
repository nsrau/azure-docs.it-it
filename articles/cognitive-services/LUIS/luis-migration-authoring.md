---
title: Eseguire la migrazione alla risorsa di Azure per la creazioneMigrate to Azure resource for authoring
titleSuffix: Azure Cognitive Services
description: Eseguire la migrazione a una chiave di risorsa di creazione di Azure.Migrate to an Azure authoring resource key.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194510"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Eseguire la migrazione a una chiave di creazione delle risorse di AzureMigrate to an Azure resource authoring key

L'autenticazione di creazione della lingua (LUIS) è stata modificata da un account di posta elettronica a una risorsa di Azure.Language Understanding (LUIS) authoring authentication changed from an email account to an Azure resource. Anche se non è attualmente necessario, il passaggio a una risorsa di Azure verrà applicato in futuro.

## <a name="why-migrate"></a>Perché eseguire la migrazione?

L'uso di una risorsa di Azure per la creazione consente, in qualità di proprietario della risorsa, di controllare l'accesso alla creazione. È possibile creare e assegnare un nome alle risorse di creazione per gestire diversi gruppi di autori.

Ad esempio, sei il proprietario di 2 app LUIS e hai membri diversi che sono collaboratori in ogni app. Puoi creare due diverse risorse di creazione e assegnare ogni app a ogni risorsa separata. Assegnare quindi ogni membro come collaboratore alla risorsa di creazione appropriata a seconda dell'app a cui collabora. La risorsa di creazione di Azure controlla l'autorizzazione.

> [!Note]
> Prima della migrazione, i coautori sono noti come _collaboratori_ a livello di app LUIS. Dopo la migrazione, il ruolo Azure del _collaboratore_ viene usato per la stessa funzionalità ma a livello di risorsa di Azure.After migration, the Azure role of contributor is used for the same functionality but on the Azure resource level.

## <a name="what-is-migrating"></a>Che cos'è la migrazione?

La migrazione include:

* Tutti gli utenti di LUIS, proprietari e collaboratori.
* **Tutte le** app.
* Migrazione **unidirezionale.**

Il proprietario non può scegliere un sottoinsieme di app di cui eseguire la migrazione e il processo non è reversibile.

La migrazione non è:

* Processo che raccoglie i collaboratori e sposta o aggiunge automaticamente la risorsa di creazione di Azure.A process that collects collaborators and automatically moves or adds to the Azure authoring resource. L'utente, in qualità di proprietario dell'app, deve completare questo passaggio. Questo passaggio richiede autorizzazioni per la risorsa appropriata.
* Processo per creare e assegnare una risorsa di runtime di stima. Se è necessaria una risorsa di runtime di stima, che è [un processo separato](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) e viene invariato.

## <a name="how-are-the-apps-migrating"></a>Come vengono le app a migrazione?

Il [portale LUIS](https://www.luis.ai) fornisce il processo di migrazione.

Verrà chiesto di eseguire la migrazione se:

* Sono disponibili app nel sistema di autenticazione e-mail per la creazione.
* E tu sei il proprietario dell'app.

È possibile ritardare il processo di migrazione annullando la finestra. Viene chiesto periodicamente di eseguire la migrazione fino a quando non si esegue la migrazione o viene superata la scadenza della migrazione. È possibile avviare il processo di migrazione dall'icona del lucchetto della barra di spostamento superiore.

## <a name="migration-for-the-app-owner"></a>Migrazione per il proprietario dell'app

### <a name="before-you-migrate"></a>Prima di eseguire la migrazione

* **Obbligatorio,** è necessario disporre di [una sottoscrizione](https://azure.microsoft.com/free/)di Azure . Una parte del processo di sottoscrizione richiede informazioni di fatturazione. Tuttavia, è possibile`F0`utilizzare il piano tariffario Gratuito ( ) quando si utilizza LUIS.
* **Facoltativamente,** eseguire il backup delle app dall'elenco delle app del portale LUIS esportando ogni app o utilizzare [l'API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)di esportazione.
* **Facoltativamente,** salvare l'elenco dei collaboratori di ogni app. Questo elenco di posta elettronica viene fornito come parte del processo di migrazione.


**La creazione dell'app LUIS è gratuita,** indicata dal `F0` livello. Ulteriori informazioni [sui piani tariffari](luis-boundaries.md#key-limits).

Se non si dispone di una sottoscrizione di Azure, [iscriversi](https://azure.microsoft.com/free/).

### <a name="migration-steps"></a>Passaggi della migrazione

Attenersi [alla seguente procedura di migrazione](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Dopo la migrazione

Dopo il processo di migrazione, tutte le app LUIS vengono ora assegnate a una singola risorsa di creazione e modifica LUIS.

È possibile creare altre risorse di creazione e assegnazione dalla pagina **Gestisci risorse di Azure >** nel portale _LUIS._

È possibile aggiungere collaboratori alla risorsa di creazione dal portale di _Azure_nella pagina **Controllo di accesso (IAM)** per tale risorsa. Per ulteriori informazioni, consultate [Aggiungere l'accesso di collaboratore.](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)

|Portale|Scopo|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|Creare risorse di stima e creazione.<br>- Assegnare i collaboratori.|
|[Luis](https://www.luis.ai)|- Eseguire la migrazione a nuove risorse di creazione.<br>Assegnare o annullare l'assegnazione di risorse di stima e creazione alle app dalla pagina **Gestisci-> risorse** di Azure.- Assign or unsassign prediction and authoring resources to apps from Manage -> Azure resources page.|

## <a name="migration-for-the-app-contributor"></a>Migrazione per il collaboratore dell'app

Ogni utente di LUIS deve eseguire la migrazione, inclusi i collaboratori/collaboratori. Un collaboratore deve eseguire la migrazione per avere accesso all'app.

> [!Note]
> Se il proprietario dell'app LUIS ha eseguito la migrazione e ha aggiunto il collaboratore come collaboratore nella risorsa di Azure, il collaboratore non avrà comunque accesso all'app a meno che non eseda anche la migrazione.

### <a name="before-the-app-is-migrated"></a>Prima della migrazione dell'app

Puoi scegliere di esportare un'app per cui sei un collaboratore, quindi importarla di nuovo in LUIS. Il processo di importazione crea una nuova app con un nuovo ID app, di cui sei il proprietario.

### <a name="after-the-app-is-migrated"></a>Dopo la migrazione dell'app

Il proprietario dell'app deve [aggiungere la posta elettronica alla risorsa](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)di creazione di Azure come collaboratore.

Dopo il processo di migrazione, tutte le app di cui si è proprietari sono disponibili nella pagina **App personali** del portale LUIS.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>Risoluzione dei problemi relativi al processo di migrazione per la creazione di LUISTroubleshooting the migration process for LUIS authoring

* Le chiavi di creazione LUIS sono visibili nel portale LUIS solo al termine del processo di migrazione. Se si creano le chiavi di creazione, ad esempio con l'interfaccia della riga di comando DI LUIS, l'utente deve comunque completare il processo di migrazione nel portale LUIS.
* Se un utente migrato aggiunge un utente non migrato come collaboratore nella risorsa di azure, l'utente non migrato non avrà accesso alle app a meno che non esechino la migrazione.
* Se un utente non migrato non è proprietario di alcuna app, ma è un collaboratore di altre app di proprietà di altri utenti e i proprietari hanno subito il processo di migrazione, l'utente dovrà eseguire la migrazione per avere accesso alle app.
* Se un utente non migrato ha aggiunto un altro utente migrato come collaboratore alla propria app, si verificherà un errore poiché non sarà possibile aggiungere un utente migrato come collaboratore a un'app. L'utente non migrato dovrà quindi passare attraverso il processo di migrazione e creare una risorsa azure e aggiungere l'utente migrato come collaboratore a tale risorsa.

Viene visualizzato un errore durante il processo di migrazione se:You receive an error during the migration process if:
* La sottoscrizione non autorizza l'utente a creare risorse di Servizi cognitivi
* La migrazione influisce negativamente su qualsiasi runtime delle applicazioni. Durante la migrazione, tutti i collaboratori vengono rimossi dalle app e l'utente viene rimosso come collaboratore da altre app. Questo processo significa che anche i tasti assegnati vengono rimossi. La migrazione viene bloccata se sono state assegnate chiavi in altre app. Rimuovere la chiave assegnata in modo sicuro prima della migrazione. Se si sa che la chiave assegnata non viene utilizzata nel runtime, è necessario rimuoverla per poter procedere nella migrazione.

Accedere all'elenco di risorse di Azure dell'app usando il formato URL seguente:Access your app's Azure resource list using the following URL format:

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>Passaggi successivi

* [Come eseguire la migrazione dell'app a una risorsa di creazioneHow to migrate your app to an authoring resource](luis-migration-authoring-steps.md)
