---
title: Eseguire la migrazione a una risorsa di Azure per la creazione di 2
titleSuffix: Azure Cognitive Services
description: Eseguire la migrazione alla chiave 2 della risorsa di creazione di Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/17/2020
ms.author: diberry
ms.openlocfilehash: 5f0778436db7bd8c3a09e3ba346d8a9a6c4af454
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84983794"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Eseguire la migrazione a una chiave di creazione delle risorse di Azure

Language Understanding (LUIS) la creazione dell'autenticazione è cambiata da un account di posta elettronica a una risorsa di Azure. Sebbene non sia attualmente necessario, il trasferimento a una risorsa di Azure verrà applicato in futuro.


## <a name="what-is-migration"></a>Che cos'è la migrazione?

La migrazione è il processo di modifica dell'autenticazione di creazione da un account di posta elettronica a una risorsa di Azure. L'account verrà collegato a una sottoscrizione di Azure e a una risorsa di creazione di Azure dopo la migrazione. **Tutti gli utenti di LUIS (proprietari o collaboratori) dovranno eseguire la migrazione.** La migrazione deve essere eseguita dal portale LUIS. Se si creano le chiavi di creazione, ad esempio con l'interfaccia della riga di comando LUIS, sarà comunque necessario completare il processo di migrazione nel portale LUIS. È ancora possibile avere co-autori nelle applicazioni dopo la migrazione, ma questi verranno aggiunti a livello di risorsa di Azure invece che a livello di applicazione.

> [!Note]
> Prima della migrazione, i coautori sono noti come _collaboratori_ a livello di app Luis. Dopo la migrazione, il ruolo di _collaboratore_ di Azure viene usato per la stessa funzionalità, ma a livello di risorse di Azure.

## <a name="note-before-you-migrate"></a>Nota prima della migrazione

* La migrazione è un processo **unidirezionale** . Non è possibile tornare indietro dopo la migrazione.
* Le applicazioni verranno **automaticamente migrate** con l'utente se si è **proprietari** dell'applicazione.
* Il proprietario non può scegliere un subset di app da migrare e il processo non è reversibile.
* Le applicazioni **scompariranno dai lati del collaboratore** dopo la **migrazione del proprietario**.
* Ai proprietari viene richiesto di inviare messaggi di posta elettronica ai collaboratori per informarli della migrazione.
* Se si è un **collaboratore** nell'applicazione, le applicazioni **non verranno migrate** .
* Non esiste alcun modo per un proprietario per capire che i suoi collaboratori sono stati migrati.
* La **migrazione** non raccoglie automaticamente collaboratori e li sposta o li aggiunge alla risorsa di creazione di Azure. Il proprietario dell'app è quello che deve completare questo passaggio dopo la migrazione. Questo passaggio richiede [le autorizzazioni per la risorsa di creazione di Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Dopo l'assegnazione alla risorsa di Azure, i **collaboratori dovranno eseguire la migrazione per accedere alle applicazioni**. In caso contrario, non avranno accesso per la creazione delle applicazioni.
* Un utente migrato non può essere aggiunto come collaboratore dell'applicazione.
* Se **si possiedono chiavi di stima assegnate alle applicazioni di proprietà di un altro utente**, la **migrazione verrà bloccata** sia per il proprietario che per i collaboratori. Per indicazioni, vedere di seguito.

> [!Note]
> Se è necessario creare una risorsa di runtime di stima, esiste [un processo separato](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) per crearlo.

## <a name="migration-prerequisites"></a>Prerequisiti per la migrazione

* È necessario essere associati a una sottoscrizione di Azure valida. Chiedere all'amministratore del tenant di aggiungere l'utente alla sottoscrizione. in alternativa, è possibile iscriversi per ottenere una versione gratuita [qui](https://azure.microsoft.com/free/).
* È necessario creare una risorsa di creazione di LUIS Azure dal portale LUIS o dalla portale di Azure. La creazione di una risorsa di creazione dal portale LUIS fa parte del flusso di migrazione illustrato nella sezione successiva.
* Se si è un **collaboratore sulle applicazioni**, le applicazioni non verranno migrate automaticamente. È **consigliabile eseguire il backup di queste applicazioni** esportandole o usare l' [API di esportazione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). È possibile importare di nuovo l'app in LUIS dopo la migrazione. Il processo di importazione crea una nuova app con un nuovo ID app, di cui si è proprietari.
* Se l'utente è il **proprietario dell'applicazione**, non sarà necessario esportare le app in quanto verranno migrate automaticamente. È **consigliabile salvare l'elenco collaborator's di ogni app.** Un modello di messaggio di posta elettronica con questo elenco viene fornito facoltativamente come parte del processo di migrazione.


|Portale|Scopo|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Creare le risorse di stima e creazione.<br>* Assegnare collaboratori alle risorse.|
|[LUIS](https://www.luis.ai)|* Eseguire la migrazione alle nuove risorse di creazione.<br>* Creare una nuova risorsa di creazione nel flusso di migrazione.<br>* Assegnare o annullare l'assegnazione delle risorse di stima e creazione alle app dalla pagina **Gestisci-> risorse di Azure** . <br>* Spostare le applicazioni da una risorsa di creazione a un'altra.  |

> [!Note]
> La creazione di un' **app Luis è gratuita**, indicata dal `F0` livello. [Altre informazioni sui piani tariffari](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Passaggi della migrazione

1. Nel portale LUIS su cui si sta lavorando è possibile avviare il processo di migrazione dall'icona di **Azure** sulla barra degli strumenti superiore.

   > [!div class="mx-imgBorder"]
   > ![Icona di migrazione](./media/migrate-authoring-key/migration-button.png)

2. La finestra popup migrazione consente di continuare la migrazione o eseguire la migrazione in un secondo momento. Selezionare **migrate Now**.

   > [!div class="mx-imgBorder"]
   > ![Prima finestra popup del processo di migrazione, selezionare Esegui migrazione ora.](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Facoltativamente, se una delle app dispone di collaboratori, viene richiesto di **inviarle un messaggio di posta elettronica** che informa sulla migrazione. Si tratta di un passaggio facoltativo.

   Per ogni collaboratore e app, l'applicazione di posta elettronica predefinita si apre con un messaggio di posta elettronica in formato lieve. È possibile modificare il messaggio di posta elettronica prima di inviarlo. Il modello di messaggio di posta elettronica include l'ID app esatto e il nome dell'app.
   
   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources
      
   Thank you
   ```
   
   > [!Note]
   > Dopo aver eseguito la migrazione dell'account ad Azure, le app non saranno più disponibili per i collaboratori.

4. Facoltativamente, se si è un collaboratore di qualsiasi applicazione, viene richiesto di **esportare una copia delle app** selezionando questa opzione durante il flusso di migrazione. Dopo aver selezionato l'opzione, è possibile trovare la pagina seguente in cui si fa clic sul pulsante download a sinistra per esportare le app desiderate. È possibile importare queste app di nuovo dopo la migrazione perché non verranno automaticamente migrate con l'utente. Si tratta di un passaggio facoltativo.

   > [!div class="mx-imgBorder"]
   > ![Richiedere l'esportazione dell'applicazione.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. È possibile scegliere di creare una nuova risorsa di creazione LUIS o di eseguire la migrazione a una risorsa di creazione esistente, se ne è già stata creata una da Azure. Scegliere l'opzione desiderata selezionando il pulsante appropriato riportato di seguito.

   > [!div class="mx-imgBorder"]
   > ![Creare la risorsa di creazione](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Crea una nuova risorsa di creazione da LUIS per eseguire la migrazione

Se si desidera creare una nuova risorsa di creazione, selezionare **Crea una nuova risorsa** di creazione e specificare le informazioni seguenti nella finestra successiva.

> [!div class="mx-imgBorder"]
> ![Creare la risorsa di creazione](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Nome della risorsa**: nome personalizzato scelto dall'utente, che viene usato nell'URL per le query dell'endpoint di creazione e stima.
* **Nome sottoscrizione** : la sottoscrizione che verrà associata alla risorsa. Se si dispone di più di una sottoscrizione appartenente al tenant, selezionare quella desiderata dall'elenco a discesa.
* **Gruppo di risorse** : nome del gruppo di risorse personalizzato scelto dall'elenco a discesa. I gruppi di risorse consentono di raggruppare le risorse di Azure per l'accesso e la gestione.
* **Tenant**: tenant a cui è associata la sottoscrizione di Azure. Questa impostazione viene impostata per impostazione predefinita sul tenant attualmente selezionato. È possibile cambiare tenant selezionando l'avatar più a destra che contiene le iniziali.

Dopo aver immesso le informazioni sopra riportate, selezionare **fine**.

Si noti che è possibile avere 10 risorse di creazione gratuite per ogni area, per sottoscrizione. Se la sottoscrizione ha più di 10 risorse di creazione nella stessa area, non sarà possibile crearne una nuova.

* Quando viene creata la risorsa di creazione, viene visualizzato il messaggio di operazione riuscita. Selezionare **Chiudi** per chiudere la finestra popup.

  > [!div class="mx-imgBorder"]
  > ![Creazione della risorsa di creazione completata.](./media/migrate-authoring-key/migration-success-2.png)

### <a name="use-existing-authoring-resource-to-migrate"></a>Usa la risorsa di creazione esistente per eseguire la migrazione

Se la sottoscrizione è già associata a una risorsa di Azure per la creazione di LUIS o se è stata creata in dal portale di Azure e si vuole eseguire la migrazione al computer anziché creare una nuova risorsa, selezionare **USA risorsa di creazione esistente** e specificare le informazioni seguenti nella finestra successiva.

> [!div class="mx-imgBorder"]
>![Creare la risorsa di creazione](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Tenant**: tenant a cui è associata la sottoscrizione di Azure. Questa impostazione viene impostata per impostazione predefinita sul tenant attualmente selezionato.
* **Nome sottoscrizione** : la sottoscrizione che verrà associata alla risorsa. Se si dispone di più di una sottoscrizione appartenente al tenant, selezionare quella desiderata dall'elenco a discesa.
* **Nome risorsa** : selezionare la risorsa di creazione in cui si vuole eseguire la migrazione.

> [!Note]
> Se non è possibile visualizzare la risorsa di creazione nell'elenco a discesa, assicurarsi che sia stata creata nel **percorso corretto** in base al portale Luis che è stato eseguito l'accesso. Assicurarsi inoltre che gli elementi creati siano effettivamente una **risorsa di creazione** e non una **risorsa di stima**.


* Convalidare il nome della risorsa di creazione e fare clic sul pulsante **migrate Now** .

 > [!div class="mx-imgBorder"]
 > ![Creare la risorsa di creazione](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

* Viene visualizzato il messaggio di operazione riuscita. Selezionare **Chiudi** per chiudere la finestra popup.

 > [!div class="mx-imgBorder"]
 > ![Creazione della risorsa di creazione completata.](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Uso di app dopo la migrazione

* Dopo il processo di migrazione, tutte le app LUIS di cui si è proprietari, verranno ora assegnate a una singola risorsa di authoring LUIS.
* L'elenco **app personali** Mostra le app di cui è stata eseguita la migrazione alla nuova risorsa di creazione.
* Prima di accedere alle app, selezionare la sottoscrizione e la risorsa di authoring LUIS per visualizzare le app che è possibile creare.

 > [!div class="mx-imgBorder"]
 > ![Selezionare sottoscrizione e la risorsa di authoring LUIS per visualizzare le app che possono essere create.](./media/create-app-in-portal-select-subscription-luis-resource.png)

* Non è necessario essere a conoscenza della chiave della risorsa di creazione per continuare a modificare le app nel portale LUIS.
* Se si prevede di modificare le app a livello di codice, sono necessari i valori delle chiavi di creazione. Questi valori vengono visualizzati nella pagina **gestisci > risorse di Azure** nel portale Luis e sono disponibili anche nella portale di Azure nella pagina **chiavi** della risorsa. È anche possibile creare altre risorse di creazione e assegnarle dalla stessa pagina.

 > [!div class="mx-imgBorder"]
 > ![Gestire la risorsa di creazione.](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="add-contributors-to-authoring-resources"></a>Aggiungere collaboratori a risorse di creazione

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Informazioni su [come aggiungere collaboratori](luis-how-to-collaborate.md) alla risorsa di creazione. I collaboratori avranno accesso a tutte le applicazioni in tale risorsa.

È possibile aggiungere collaboratori alla risorsa di creazione dalla _portale di Azure_nella pagina **controllo di accesso (IAM)** per la risorsa. Per altre informazioni, vedere [aggiungere l'accesso al collaboratore](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Se il proprietario dell'app LUIS ha eseguito la migrazione e ha aggiunto il collaboratore come collaboratore nella risorsa di Azure, il collaboratore non potrà ancora accedere all'app, a meno che non ne venga eseguita la migrazione.

## <a name="luis-portal-migration-reminders"></a>Promemoria di migrazione del portale LUIS

Il [portale Luis](https://www.luis.ai) fornisce il processo di migrazione.

Verrà richiesto di eseguire la migrazione se:
* Sono disponibili app nel sistema di autenticazione della posta elettronica per la creazione.
* E il proprietario dell'app.

Con cadenza settimanale, viene richiesto di eseguire la migrazione delle app. È possibile annullare questa finestra senza eseguire la migrazione. Se si vuole eseguire la migrazione prima del successivo periodo pianificato, è possibile avviare il processo di migrazione dall'icona di **Azure** sulla barra degli strumenti superiore del portale Luis.

È possibile ritardare il processo di migrazione annullando la finestra. Viene richiesto periodicamente di eseguire la migrazione fino a quando non si esegue la migrazione o viene passata la scadenza della migrazione. È possibile avviare il processo di migrazione dall'icona di blocco della barra di spostamento superiore.

## <a name="prediction-resources-blocking-migration"></a>Risorse di stima che bloccano la migrazione
La migrazione influisce negativamente su qualsiasi runtime delle applicazioni. Quando si esegue la migrazione, tutti i collaboratori vengono rimossi dalle app e vengono rimossi come collaboratore da altre app. Questo processo indica che anche le chiavi assegnate da un collaboratore vengono rimosse, il che potrebbe comportare l'applicazione in fase di produzione. Questo è il motivo per cui la migrazione viene bloccata fino a quando non vengono rimossi manualmente i collaboratori o le chiavi a loro assegnati.

### <a name="when-does-prediction-resources-block-migration"></a>Quando le risorse di stima bloccano la migrazione?
* La migrazione viene bloccata se sono state assegnate risorse di stima/Runtime nelle app di cui non si è proprietari.
* La migrazione viene bloccata se sono presenti altri utenti che assegnano risorse di stima/Runtime alle app di cui si è proprietari.

### <a name="recommended-steps-to-do-if-you-are-the-owner-of-the-app"></a>Procedura consigliata se si è il proprietario dell'app
Se si è proprietari di alcune applicazioni e i collaboratori hanno assegnato una chiave di stima/Runtime a questa applicazione, viene visualizzato un errore quando si esegue la migrazione in cui sono elencati gli ID applicazione con chiavi di stima assegnate ad essi di proprietà di altri utenti.

È consigliabile:
* Inviare notifiche ai collaboratori sulla migrazione.
* Rimuovere tutti i collaboratori dalle applicazioni visualizzate nell'errore.
* Sottoporsi al processo di migrazione che dovrebbe avere esito positivo se si rimuovono manualmente i collaboratori.
* Assegnare collaboratori come collaboratori alla nuova risorsa di creazione.
* I collaboratori devono eseguire la migrazione e riassegnare nuovamente le risorse di stima alle applicazioni.
Si noti che questa operazione provocherà temporaneamente un'operazione di inserimento nell'applicazione fino a quando le risorse di stima non verranno riassegnate.

Un'altra soluzione qui è, prima della migrazione del proprietario, i collaboratori possono aggiungere i proprietari dell'app come collaboratori nelle sottoscrizioni di Azure dalla portale di Azure. Questo consentirà al proprietario di accedere alla risorsa di stima del runtime. Se il proprietario viene migrato usando la nuova sottoscrizione a cui sono stati aggiunti (che si troveranno in un nuovo tenant), questo non solo bloccherà il processo di migrazione per il collaboratore e il proprietario dell'app, ma consentirà una migrazione uniforme delle app con la chiave di stima ancora assegnata senza interruzioni delle app.


### <a name="recommended-steps-to-do-if-you-are-a-collaborator-on-an-app"></a>Procedura consigliata per l'esecuzione di un collaboratore in un'app
Se si sta collaborando alle applicazioni e la chiave di stima/Runtime è stata assegnata a questa applicazione, viene visualizzato un errore quando si esegue la migrazione in cui sono elencati gli ID applicazione e i percorsi chiave che bloccano la migrazione.

È consigliabile:
* Esporta le applicazioni come backup. Viene fornito come passaggio facoltativo del processo di migrazione.
* Annullare l'assegnazione delle risorse di stima dalla pagina **Gestisci-> risorse di Azure** .
* Sottoporsi al processo di migrazione.
* Importa le applicazioni dopo la migrazione.
* Riassegnare le chiavi di stima alla pagina di **gestione delle applicazioni > risorse di Azure** .

> [!Note]
> Quando si esegue la migrazione delle applicazioni dopo la migrazione, le applicazioni avranno ID app diversi e saranno diverse da quelle riportate nell'ambiente di produzione. A questo punto si sarà proprietari di queste applicazioni.

## <a name="troubleshooting-migration-process"></a>Risoluzione dei problemi del processo di migrazione

Quando si tenta di eseguire la migrazione, ma non è possibile trovare la sottoscrizione di Azure nell'elenco a discesa:
* Assicurarsi di avere una sottoscrizione di Azure valida autorizzata a creare risorse di servizi cognitivi. Passare a [portale di Azure](https://ms.portal.azure.com) e verificare lo stato della sottoscrizione. Se non si dispone di un, [creare una versione di valutazione gratuita](https://azure.microsoft.com/free/).
* Assicurarsi di trovarsi nel tenant appropriato associato alla sottoscrizione valida. È possibile passare i tenant dall'altro avatar a sinistra nella barra degli strumenti seguente: ![ Cambia tenant.](./media/migrate-authoring-key/switch-user-tenant-2.png)

 Se è già presente una risorsa di creazione, ma non è possibile trovarla quando si seleziona l'opzione "USA risorsa di creazione esistente":
* È possibile che la risorsa sia stata creata in un percorso diverso rispetto al portale con cui si accede. Controllare [le aree di creazione e i portali di Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)
* Creare una nuova risorsa dal portale LUIS

Se si seleziona l'opzione "crea una nuova risorsa di creazione" e la migrazione non è riuscita con il messaggio di errore "Impossibile recuperare le informazioni di Azure dell'utente, riprovare più tardi"
* È possibile che la sottoscrizione disponga di 10 o più risorse di creazione per area per sottoscrizione. In tal caso, non sarà possibile creare una nuova risorsa di creazione.
* Eseguire la migrazione selezionando l'opzione "USA risorsa di creazione esistente" e selezionare una delle risorse esistenti nella sottoscrizione.

Se viene visualizzato l'errore seguente, controllare le [procedure consigliate da eseguire se si è proprietari della sezione dell'app] la ![ migrazione non riesce per i proprietari](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Se viene visualizzato l'errore riportato di seguito, controllare le [procedure consigliate da eseguire se si è un collaboratore in una sezione dell'app] ![ la migrazione non riesce per i collaboratori](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Passaggi successivi

* Esaminare i [concetti](luis-concept-keys.md) relativi alla creazione e alle chiavi di runtime
* Esaminare [come assegnare chiavi](luis-how-to-azure-subscription.md) e aggiungere [collaboratori](luis-how-to-collaborate.md)
