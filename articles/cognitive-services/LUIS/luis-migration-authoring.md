---
title: Eseguire la migrazione a una chiave di creazione delle risorse di Azure
titleSuffix: Azure Cognitive Services
description: Questo articolo descrive come eseguire la migrazione di Language Understanding (LUIS) la creazione dell'autenticazione da un account di posta elettronica a una risorsa di Azure.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 926b79e672c14249ec7c2b053dba7eb3a31443a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536045"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Eseguire la migrazione a una chiave di creazione delle risorse di Azure

Language Understanding (LUIS) la creazione dell'autenticazione è cambiata da un account di posta elettronica a una risorsa di Azure. Sebbene non sia attualmente necessario, il trasferimento a una risorsa di Azure verrà applicato in futuro.


## <a name="what-is-migration"></a>Che cos'è la migrazione?

La migrazione è il processo di modifica dell'autenticazione di creazione da un account di posta elettronica a una risorsa di Azure. L'account verrà collegato a una sottoscrizione di Azure e a una risorsa di creazione di Azure dopo la migrazione. *Tutti gli utenti di LUIS (proprietari o collaboratori) dovranno eseguire la migrazione.*

La migrazione deve essere eseguita dal portale LUIS. Se si creano le chiavi di creazione usando l'interfaccia della riga di comando di LUIS, ad esempio, sarà necessario completare il processo di migrazione nel portale LUIS. È comunque possibile disporre di coautori nelle applicazioni dopo la migrazione, ma questi verranno aggiunti a livello di risorsa di Azure invece che a livello di applicazione.

> [!Note]
> Prima della migrazione, i coautori sono noti come _collaboratori_ a livello di app Luis. Dopo la migrazione, il ruolo di _collaboratore_ di Azure viene usato per la stessa funzionalità a livello di risorsa di Azure.

## <a name="note-before-you-migrate"></a>Nota prima della migrazione

* È necessario eseguire la migrazione dell'esperienza di creazione entro il **2020 novembre, 2**. 
* La migrazione è un processo unidirezionale. Non è possibile tornare indietro dopo la migrazione.
* Se si è il proprietario dell'applicazione, verranno automaticamente migrate le applicazioni.
* Il proprietario non può scegliere un subset di app da migrare e il processo non è reversibile.
* Le applicazioni scompariranno dal lato del collaboratore dopo la migrazione del proprietario.
* Ai proprietari viene richiesto di inviare messaggi di posta elettronica ai collaboratori per informarli della migrazione.
* Se si è un collaboratore nell'applicazione, le applicazioni non verranno migrate.
* Un proprietario non può essere a conoscenza della migrazione dei collaboratori.
* La migrazione non raccoglie automaticamente collaboratori e li sposta o li aggiunge alla risorsa di creazione di Azure. Il proprietario dell'app è quello che deve completare questo passaggio dopo la migrazione. Questo passaggio richiede [le autorizzazioni per la risorsa di creazione di Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Dopo che i collaboratori sono stati assegnati alla risorsa di Azure, è necessario eseguire la migrazione per accedere alle applicazioni. In caso contrario, non avranno accesso per la creazione delle applicazioni.
* Un utente migrato non può essere aggiunto come collaboratore dell'applicazione.
* Se si possiedono chiavi di stima assegnate alle applicazioni di proprietà di un altro utente, la migrazione verrà bloccata sia per il proprietario che per i collaboratori. Vedere le raccomandazioni più avanti in questo articolo.

> [!Note]
> Se è necessario creare una risorsa di runtime di stima, esiste [un processo separato](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) per crearlo.

## <a name="migration-prerequisites"></a>Prerequisiti per la migrazione

* È necessario essere associati a una sottoscrizione di Azure valida. Chiedere all'amministratore del tenant di aggiungere l'utente alla sottoscrizione o [iscriversi per una versione gratuita](https://azure.microsoft.com/free/cognitive-services).
* È necessario creare una risorsa di creazione di LUIS Azure dal portale LUIS o dalla portale di Azure. La creazione di una risorsa di creazione dal portale LUIS fa parte del flusso di migrazione illustrato nella sezione successiva.
* Se si è un collaboratore sulle applicazioni, le applicazioni non verranno migrate automaticamente. Si consiglia di eseguire il backup di queste applicazioni esportandole o usando l' [API di esportazione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). È possibile importare di nuovo l'app in LUIS dopo la migrazione. Il processo di importazione crea una nuova app con un nuovo ID app, per il quale si è proprietari.
* Se si è proprietari dell'applicazione, non è necessario esportare le app perché verranno migrate automaticamente. Si consiglia di salvare l'elenco collaboratore di ogni app. Un modello di messaggio di posta elettronica con questo elenco viene fornito facoltativamente come parte del processo di migrazione.


|Portale|Scopo|
|--|--|
|[Azure](https://azure.microsoft.com/free/cognitive-services)| Creare risorse di stima e di creazione.<br> Assegnare collaboratori alle risorse.|
|[LUIS](https://www.luis.ai)| Eseguire la migrazione alle nuove risorse di creazione.<br> Creare nuove risorse di creazione nel flusso di migrazione.<br> Assegnare o annullare l'assegnazione delle risorse di stima e creazione alle app dalla pagina **Gestisci**  >  **risorse di Azure** . <br> Spostare le applicazioni da una risorsa di creazione a un'altra.  |

> [!Note]
> La creazione di un'app LUIS è gratuita, come indicato dal livello F0. [Altre informazioni sui piani tariffari](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Passaggi della migrazione

1. Nel portale LUIS su cui si sta lavorando è possibile avviare il processo di migrazione dall'icona di **Azure** sulla barra degli strumenti superiore.

   > [!div class="mx-imgBorder"]
   > ![Icona di migrazione](./media/migrate-authoring-key/migration-button.png)

2. La finestra popup migrazione consente di continuare la migrazione o eseguire la migrazione in un secondo momento. Selezionare **migrate Now**.

   > [!div class="mx-imgBorder"]
   > ![Prima finestra popup del processo di migrazione in cui si seleziona Esegui migrazione](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Se una qualsiasi delle app dispone di collaboratori, viene richiesto di inviare loro un messaggio di posta elettronica che informa sulla migrazione. Si tratta di un passaggio facoltativo.

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

4. Se si è un collaboratore in qualsiasi applicazione, viene richiesto di esportare una copia delle app selezionando questa opzione durante il flusso di migrazione. Si tratta di un passaggio facoltativo.

   Se si seleziona l'opzione, viene visualizzata la pagina seguente. Selezionare i pulsanti Scarica a sinistra per esportare le app desiderate. È possibile importare di nuovo queste app dopo la migrazione, perché non verranno automaticamente migrate con l'utente.

   > [!div class="mx-imgBorder"]
   > ![Richiedere l'esportazione delle applicazioni.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. È possibile scegliere di creare una nuova risorsa di creazione LUIS o di eseguire la migrazione a una risorsa di creazione esistente, se ne è già stata creata una da Azure. Scegliere l'opzione desiderata selezionando uno dei pulsanti seguenti.

   > [!div class="mx-imgBorder"]
   > ![Pulsanti per la creazione di una nuova risorsa di creazione e utilizzo di una risorsa di creazione esistente](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Crea una nuova risorsa di creazione da LUIS per eseguire la migrazione

Se si vuole creare una nuova risorsa di creazione, selezionare **Crea una nuova risorsa** di creazione e specificare le informazioni seguenti nella finestra successiva. Al termine selezionare **Done** (Fine).

> [!div class="mx-imgBorder"]
> ![Finestra per la creazione di una risorsa di creazione](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Nome del tenant**: il tenant a cui è associata la sottoscrizione di Azure. Per impostazione predefinita, questa opzione è impostata sul tenant attualmente in uso. È possibile cambiare i tenant selezionando l'avatar più a destra, che contiene le iniziali.
* **Nome risorsa**: nome personalizzato scelto. Viene usato come parte dell'URL per le query di creazione e di endpoint di stima.
* **Nome sottoscrizione**: la sottoscrizione che verrà associata alla risorsa. Se si dispone di più di una sottoscrizione appartenente al tenant, selezionare quella desiderata dall'elenco a discesa.
* **Nome del gruppo di risorse di Azure**: nome del gruppo di risorse personalizzato scelto dall'elenco a discesa. I gruppi di risorse consentono di raggruppare le risorse di Azure per l'accesso e la gestione.

Si noti che è possibile avere 10 risorse di creazione gratuite per ogni area, per sottoscrizione. Se la sottoscrizione ha più di 10 risorse di creazione nella stessa area, non sarà possibile crearne una nuova.

Quando viene creata la risorsa di creazione, viene visualizzato il messaggio di operazione riuscita. Selezionare **Chiudi** per chiudere la finestra popup.

  > [!div class="mx-imgBorder"]
  > ![Messaggio che indica che la risorsa di creazione è stata creata correttamente](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>Usa la risorsa di creazione esistente per eseguire la migrazione

Se la sottoscrizione è già associata a una risorsa di Azure per la creazione di LUIS o se è stata creata una risorsa dal portale di Azure e si vuole eseguirne la migrazione anziché crearne una nuova, selezionare **USA risorsa di creazione esistente**. Nella finestra successiva specificare le informazioni seguenti e quindi fare clic su **fine**.

> [!div class="mx-imgBorder"]
>![Finestra per la modifica di una risorsa di creazione esistente](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Nome del tenant**: il tenant a cui è associata la sottoscrizione di Azure. Per impostazione predefinita, questa opzione è impostata sul tenant attualmente in uso.
* **Nome sottoscrizione**: la sottoscrizione che verrà associata alla risorsa. Se si dispone di più di una sottoscrizione appartenente al tenant, selezionare quella desiderata dall'elenco a discesa.
* **Nome della risorsa**: la risorsa di creazione di cui si vuole eseguire la migrazione.

> [!Note]
> Se non è possibile visualizzare la risorsa di creazione nell'elenco a discesa, assicurarsi che sia stata creata nel percorso corretto in base al portale LUIS a cui è stato effettuato l'accesso. Assicurarsi inoltre che gli elementi creati siano una risorsa di creazione e non una risorsa di stima.


Convalidare il nome della risorsa di creazione e selezionare il pulsante **migra** .

> [!div class="mx-imgBorder"]
> ![Finestra per scegliere una risorsa di creazione, con il pulsante Migra ora disponibile](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

Viene visualizzato un messaggio che indica l'esito positivo dell'operazione. Selezionare **Chiudi** per chiudere la finestra popup.

> [!div class="mx-imgBorder"]
> ![Messaggio che indica che la migrazione della risorsa di creazione è stata completata](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Uso di app dopo la migrazione

Al termine del processo di migrazione, tutte le app LUIS per cui si è proprietari saranno ora assegnate a una singola risorsa di authoring LUIS.

L'elenco **app personali** Mostra le app di cui è stata eseguita la migrazione alla nuova risorsa di creazione. Prima di accedere alle app, selezionare la sottoscrizione e la risorsa di authoring LUIS per visualizzare le app che è possibile creare.

 > [!div class="mx-imgBorder"]
 > ![Caselle per la sottoscrizione e la risorsa di creazione](./media/create-app-in-portal-select-subscription-luis-resource.png)

Non è necessario essere a conoscenza della chiave della risorsa di creazione per continuare a modificare le app nel portale LUIS.

Se si prevede di modificare le app a livello di codice, saranno necessari i valori delle chiavi di creazione. Questi valori vengono visualizzati nella pagina **Gestisci**  >  **risorse di Azure** nel portale Luis. Sono disponibili anche nella portale di Azure nella pagina **chiavi** della risorsa. È anche possibile creare altre risorse di creazione e assegnarle dalla stessa pagina.

 > [!div class="mx-imgBorder"]
 > ![Pagina per la gestione delle risorse di creazione](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>Aggiunta di collaboratori a risorse di creazione

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Informazioni su [come aggiungere collaboratori](luis-how-to-collaborate.md) alla risorsa di creazione. I collaboratori avranno accesso a tutte le applicazioni in tale risorsa.

È possibile aggiungere collaboratori alla risorsa di creazione dalla portale di Azure nella pagina **controllo di accesso (IAM)** per la risorsa. Per altre informazioni, vedere [aggiungere l'accesso al collaboratore](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Se il proprietario dell'app LUIS ha eseguito la migrazione e ha aggiunto il collaboratore come collaboratore nella risorsa di Azure, il collaboratore non potrà ancora accedere all'app, a meno che non ne venga eseguita la migrazione.

## <a name="luis-portal-migration-reminders"></a>Promemoria di migrazione del portale LUIS

Il [portale Luis](https://www.luis.ai) fornisce il processo di migrazione.

Verrà richiesto di eseguire la migrazione se si verificano entrambe le condizioni seguenti:
* Sono disponibili app nel sistema di autenticazione della posta elettronica per la creazione.
* Sei il proprietario dell'app.

Con cadenza settimanale, viene richiesto di eseguire la migrazione delle app. È possibile chiudere questa finestra senza eseguire la migrazione. Se si vuole eseguire la migrazione prima del successivo periodo pianificato, è possibile avviare il processo di migrazione dall'icona di **Azure** sulla barra degli strumenti superiore del portale Luis.

## <a name="prediction-resources-blocking-migration"></a>Risorse di stima che bloccano la migrazione
La migrazione influisce negativamente sul runtime dell'applicazione. Quando si esegue la migrazione, tutti i collaboratori vengono rimossi dalle app e si viene rimossi come collaboratore da altre app. Questo processo indica che vengono rimosse anche le chiavi assegnate da un collaboratore, che potrebbe interrompere l'applicazione se è in produzione. Questo è il motivo per cui la migrazione viene bloccata fino a quando non si rimuovono manualmente i collaboratori o le chiavi a loro assegnati.

La migrazione viene bloccata se si verifica una delle condizioni seguenti:

* Sono state assegnate risorse di stima/Runtime nelle app di cui non si è proprietari.
* Sono presenti altri utenti che assegnano risorse di stima/Runtime alle app di cui si è proprietari.

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>Procedura consigliata se si è il proprietario dell'app
Se si è proprietari di alcune applicazioni e i collaboratori hanno assegnato una chiave di stima/Runtime a queste applicazioni, viene visualizzato un errore durante la migrazione. Nell'errore sono elencati gli ID applicazione con chiavi di stima assegnate a loro proprietà da altri utenti.

È consigliabile:
* Inviare notifiche ai collaboratori sulla migrazione.
* Rimuovere tutti i collaboratori dalle applicazioni visualizzate nell'errore.
* Sottoporsi al processo di migrazione, che dovrebbe avere esito positivo se si rimuovono manualmente i collaboratori.
* Assegnare collaboratori come collaboratori alla nuova risorsa di creazione. I collaboratori eseguiranno la migrazione e riassegnano le risorse di stima alle applicazioni. Si noti che questo provocherà temporaneamente un'operazione di break nell'applicazione fino a quando le risorse di stima non verranno riassegnate.

Qui è disponibile un'altra soluzione potenziale. Prima della migrazione del proprietario, i collaboratori possono aggiungere i proprietari delle app come collaboratori nelle sottoscrizioni di Azure dalla portale di Azure. Questo passaggio consentirà all'utente di accedere alla risorsa di stima del runtime. Se il proprietario viene migrato usando la nuova sottoscrizione a cui sono stati aggiunti (che si troveranno in un nuovo tenant), questo passaggio non riuscirà a sbloccare il processo di migrazione sia per il collaboratore che per il proprietario dell'app. Consente inoltre di eseguire una migrazione uniforme delle app, con la chiave di stima ancora assegnata che non interrompe le app.


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>Procedura consigliata se si è un collaboratore in un'app
Se si sta collaborando alle applicazioni ed è stata assegnata una chiave di stima/Runtime a queste applicazioni, viene visualizzato un errore durante la migrazione. L'errore elenca gli ID applicazione e i percorsi chiave che bloccano la migrazione.

È consigliabile:
* Esportare le applicazioni come backup. Questo è un passaggio facoltativo del processo di migrazione.
* Annullare l'assegnazione delle risorse di stima dalla pagina **Gestisci**  >  **risorse di Azure** .
* Sottoporsi al processo di migrazione.
* Importa le applicazioni dopo la migrazione.
* Riassegnare le chiavi di stima alle applicazioni dalla pagina **Gestisci**  >  **risorse di Azure** .

> [!Note]
> Quando si esegue la migrazione delle applicazioni dopo la migrazione, verranno riportate ID app diversi. Inoltre, saranno diversi da quelli colpiti nell'ambiente di produzione. A questo punto si sarà proprietari di queste applicazioni.

## <a name="troubleshooting-the-migration-process"></a>Risoluzione dei problemi relativi al processo di migrazione

Quando si tenta di eseguire la migrazione, ma non è possibile trovare la sottoscrizione di Azure nell'elenco a discesa:
* Assicurarsi di disporre di una sottoscrizione di Azure valida autorizzata a creare risorse di servizi cognitivi. Passare alla [portale di Azure](https://ms.portal.azure.com) e verificare lo stato della sottoscrizione. Se non è disponibile, [creare un account Azure gratuito](https://azure.microsoft.com/free/cognitive-services/).
* Assicurarsi di trovarsi nel tenant appropriato associato alla sottoscrizione valida. È possibile passare i tenant dall'avatar a sinistra delle iniziali sulla barra degli strumenti: ![ barra degli strumenti in cui è possibile cambiare tenant](./media/migrate-authoring-key/switch-user-tenant-2.png)

Se si dispone di una risorsa di creazione esistente ma non è possibile trovarla quando si seleziona l'opzione **USA risorsa di creazione esistente** :
* Probabilmente la risorsa è stata creata in un percorso diverso dal portale in cui è stato eseguito l'accesso. Controllare le [aree e i portali di authoring Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions).
* In alternativa, creare una nuova risorsa dal portale LUIS.

Se si seleziona l'opzione **Crea nuova risorsa** di creazione e la migrazione ha esito negativo con il messaggio di errore "Impossibile recuperare le informazioni di Azure dell'utente, riprovare più tardi":
* La sottoscrizione potrebbe avere 10 o più risorse di creazione per ogni area, per sottoscrizione. In tal caso, non sarà possibile creare una nuova risorsa di creazione.
* Eseguire la migrazione selezionando l'opzione **Usa la risorsa di creazione esistente** e selezionando una delle risorse esistenti nella sottoscrizione.

Se viene visualizzato l'errore seguente, verificare i [passaggi consigliati se si è proprietari dell'app](#recommended-steps-if-youre-the-owner-of-the-app).
![Errore che mostra la migrazione non riuscita per i proprietari](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Se viene visualizzato l'errore seguente, controllare la [procedura consigliata se si è un collaboratore in un'app](#recommended-steps-if-youre-a-collaborator-on-an-app).
![Errore che mostra la migrazione non riuscita per i collaboratori](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Passaggi successivi

* Esaminare i [concetti relativi alla creazione e alle chiavi di runtime](luis-how-to-azure-subscription.md).
* Vedere come [assegnare chiavi](luis-how-to-azure-subscription.md) e [aggiungere collaboratori](luis-how-to-collaborate.md).
