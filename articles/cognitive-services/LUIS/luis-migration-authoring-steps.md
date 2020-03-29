---
title: Eseguire la migrazione a una risorsa di creazione di AzureMigrate to an Azure authoring resource
titleSuffix: Azure Cognitive Services
description: Eseguire la migrazione a una risorsa di creazione di Azure.Migrate to an Azure authoring resource.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: f5197ae79670e4543c58224a33838706edae6218
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194640"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Passaggi per eseguire la migrazione alla risorsa di creazione di AzureSteps to migrate to the Azure authoring resource

Dal portale Language Understanding (LUIS), eseguire la migrazione di tutte le app di cui si è proprietari per usare la risorsa di creazione di Azure.From the Language Understanding (LUIS) portal, migrate all the apps you own to use the Azure authoring resource.

## <a name="prerequisites"></a>Prerequisiti

* **Facoltativamente,** eseguire il backup delle app dall'elenco delle app del portale LUIS esportando ogni app o utilizzare [l'API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)di esportazione.
* **Facoltativamente,** salvare l'elenco dei collaboratori di ogni app. Tutti i collaboratori possono essere inviati un messaggio di posta elettronica come parte del processo di migrazione.
* **Obbligatorio,** è necessario disporre di [una sottoscrizione](https://azure.microsoft.com/free/)di Azure . Una parte del processo di sottoscrizione richiede informazioni di fatturazione. Tuttavia, è possibile utilizzare i livelli dei prezzi liberi (F0) quando si utilizza LUIS. Si potrebbe eventualmente scoprire che è necessario un livello a pagamento, come l'utilizzo aumenta.

Se non si dispone di una sottoscrizione di Azure, [iscriversi](https://azure.microsoft.com/free/).

## <a name="access-the-migration-process"></a>Accedere al processo di migrazione

Su base settimanale, viene richiesto di eseguire la migrazione delle app. È possibile annullare questa finestra senza eseguire la migrazione. Se si vuole eseguire la migrazione prima del successivo periodo pianificato, è possibile iniziare il processo di migrazione dall'icona di **Azure** nella barra degli strumenti superiore del portale LUIS.

> [!div class="mx-imgBorder"]
> ![Icona Migrazione](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>Il proprietario dell'app inizia il processo di migrazione

Il processo di migrazione è disponibile se si è il proprietario di tutte le app LUIS.

1. Accedere al [portale LUIS](https://www.luis.ai) e accettare le condizioni per l'utilizzo.
1. La finestra popup di migrazione consente di continuare la migrazione o eseguire la migrazione in un secondo momento. Selezionare **Migra ora**. Se si sceglie di eseguire la migrazione in un secondo momento, sono disponibili 9 mesi per eseguire la migrazione alla nuova chiave di creazione in Azure.If you choose to migrate later, you have 9 months to migrate to the new authoring key in Azure.

    ![Prima finestra popup nel processo di migrazione, selezionare Esegui migrazione ora.](./media/migrate-authoring-key/migrate-now.png)

1. Facoltativamente, se una delle app dispone di collaboratori, viene richiesto di **inviare loro un** messaggio di posta elettronica per informarli della migrazione. Si tratta di un passaggio facoltativo.

    Dopo aver eseguito la migrazione dell'account in Azure, le app non saranno più disponibili per i collaboratori.

    Per ogni collaboratore e app, l'applicazione di posta elettronica predefinita si apre con un messaggio di posta elettronica leggermente formattato. È possibile modificare l'e-mail prima di inviarla.

    Il modello di messaggio di posta elettronica include l'ID app esatto e il nome dell'app.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Scegliere di creare una risorsa di creazione LUIS selezionando di usare una risorsa di creazione esistente esistente o di creare una nuova risorsa di creazione.

    > [!div class="mx-imgBorder"]
    > ![Creare la risorsa di creazione](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. Nella finestra successiva immettere le informazioni sulla chiave della risorsa. Dopo aver immesso le informazioni, selezionare **Crea risorsa**. È possibile avere 10 risorse di creazione gratuita per ogni area, per ogni sottoscrizione.

    ![Creare la risorsa di creazione](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    Durante la **creazione di una nuova risorsa di creazione** specificare le informazioni seguenti:

    * **Nome della risorsa**: nome personalizzato scelto dall'utente, che viene usato nell'URL per le query dell'endpoint di creazione e stima.
    * **Tenant**: tenant a cui è associata la sottoscrizione di Azure.
    * **Nome della sottoscrizione**: sottoscrizione a cui verrà fatturata la risorsa.
    * **Gruppo di risorse**: nome del gruppo di risorse scelto o creato. I gruppi di risorse consentono di raggruppare le risorse di Azure per l'accesso e la gestione.
    * **Località**: la località scelta dipende dalla selezione del **gruppo di risorse**.
    * **Piano tariffario**: il piano tariffario determina il numero massimo di transazioni al secondo e al mese.

1. Convalidare la risorsa di creazione ed **eseguire la migrazione ora**.

    ![Creare la risorsa di creazione](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. Quando viene creata la risorsa di creazione, viene visualizzato il messaggio di operazione riuscita. Selezionare **Chiudi** per chiudere la finestra popup.

    ![La risorsa di creazione è stata creata correttamente.](./media/migrate-authoring-key/migration-success.png)

    **L'elenco App** personali mostra le app migrate nella nuova risorsa di creazione.

    Non è necessario conoscere la chiave della risorsa di creazione per continuare a modificare le app nel portale LUIS. Se si prevede di modificare le app a livello di codice, sono necessari i valori della chiave di creazione. Questi valori vengono visualizzati nella pagina **Gestisci -> risorse** di Azure nel portale LUIS e sono disponibili anche nel portale di Azure nella pagina **Chiavi** della risorsa.

1. Prima di accedere alle app, selezionare la sottoscrizione e la risorsa di creazione LUIS per visualizzare le app che è possibile creare.

    ![Selezionare la sottoscrizione e la risorsa di creazione LUIS per visualizzare le app che è possibile autorizzare.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>Collaboratore dell'app inizia il processo di migrazione

Seguire la stessa procedura del proprietario dell'app per la migrazione. Il processo crea una nuova `LUIS.Authoring`risorsa di creazione di tipo .

È necessario eseguire la migrazione dell'account per poter essere aggiunto come collaboratore alle app migrate di proprietà di altri utenti.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Dopo il processo di migrazione, aggiungere i collaboratori alla risorsa di creazione

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Informazioni su [come aggiungere collaboratori.](luis-how-to-collaborate.md)

## <a name="troubleshooting-errors-with-the-migration-process"></a>Risoluzione degli errori con il processo di migrazioneTroubleshooting errors with the migration process

Se viene `MissingSubscriptionRegistration` visualizzato un errore nel portale LUIS con una barra di notifica rossa durante il processo di migrazione, creare una risorsa Servizio cognitivo nel portale di [Azure](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) o nell'interfaccia della riga di comando di [Azure.](luis-how-to-azure-subscription.md#create-resources-in-azure-cli) Ulteriori informazioni sulle [cause di questo errore](../../azure-resource-manager/templates/error-register-resource-provider.md#cause).

## <a name="next-steps"></a>Passaggi successivi


* Esaminare [i concetti](luis-concept-keys.md) relativi alle chiavi di creazione e di runtimeReview concepts about authoring and runtime keys
* Esaminare [come assegnare chiavi](luis-how-to-azure-subscription.md) e aggiungere [collaboratori](luis-how-to-collaborate.md)
