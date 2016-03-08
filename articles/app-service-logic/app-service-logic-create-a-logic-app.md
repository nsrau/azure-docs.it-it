<properties
	pageTitle="Creare un'app per la logica | Microsoft Azure"
	description="Informazioni su come creare un'app per la logica che connette servizi SaaS"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/01/2016"
	ms.author="stepsic"/>

# Creare una nuova app per la logica che connette servizi SaaS

| Riferimento rapido |
| --------------- |
| [Linguaggio di definizione delle app per la logica](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Documentazione del connettore delle app per la logica](https://azure.microsoft.com/documentation/articles/app-service-logic-connectors-list/) |
| [Forum delle app per la logica](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=azurelogicapps) |

Questo argomento illustra come iniziare in pochi minuti a usare le [app per la logica del servizio app](app-service-logic-what-are-logic-apps.md). Verrà illustrato un flusso di lavoro che consente di inviare un set di tweet a cui si è interessati a una cassetta postale.

Per usare questo scenario, sono necessari:

- Una sottoscrizione di Azure
- Un account Twitter
- Un account Office 365

## Creare una nuova app per la logica per inviare tramite posta elettronica i tweet

1. Nel dashboard del portale di Azure selezionare **Marketplace**. 
2. In Tutto cercare "logic apps" e quindi selezionare **Logic App (preview)**. È anche possibile selezionare **Nuovo**, **Web e dispositivi mobili** e selezionare **Logic App (preview)**. 
3. Immettere un nome per l'app per la logica, selezionare il piano del servizio app e selezionare **Crea**. In questo passaggio si presume che esista già un piano del servizio app e che si abbia familiarità con le proprietà obbligatorie. In caso contrario, è possibile iniziare con [Panoramica approfondita dei piani per Servizio app di Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md). 

4. Quando l'app per la logica si apre per la prima volta, sarà necessario un trigger. Cercare **twitter** nella casella di ricerca del trigger e selezionarlo.

7. Digitare ora la parola chiave in base alla quale cercare il tweet. ![Ricerca di Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Selezionare il segno più e quindi scegliere **Aggiungi un'azione** o **Aggiungi una condizione**: ![Più](./media/app-service-logic-create-a-logic-app/plus.png)
6. Quando si seleziona **Aggiungi un'azione**, vengono elencati tutti i connettori con le azioni disponibili. È quindi possibile scegliere il connettore e l'azione da aggiungere all'app per la logica. Ad esempio, è possibile selezionare **Office 365 - Send Email**, e altre azioni di Office 365: ![Actions](./media/app-service-logic-create-a-logic-app/actions.png)

7. Ora è necessario inserire i parametri preferiti per il messaggio di posta elettronica: ![Parametri](./media/app-service-logic-create-a-logic-app/parameters.png)

8. È infine possibile selezionare **Salva** per attivare l'app per la logica.

## Gestire l'app per la logica dopo la creazione

A questo punto l'app per la logica è operativa. Ogni volta che viene eseguito, il flusso di lavoro pianificato verifica la presenza di tweet con l'hashtag specificato. Quando viene trovato un tweet corrispondente, viene inserito nella cartella Dropbox. Infine, ecco come disabilitare l'applicazione e verificarne lo stato.

1. Fare clic su **Sfoglia** sul lato sinistro dello schermo e selezionare **App per la logica**.

2. Fare clic sulla nuova app per la logica appena creata per visualizzare informazioni generali e sullo stato corrente.

3. Per modificare la nuova app per la logica, fare clic su **Trigger e azioni**.

5. Per disattivare l'app, fare clic su **Disabilita** nella barra dei comandi.

In meno di 5 minuti è stato possibile configurare una semplice app per la logica eseguita nel cloud. Per altre informazioni sull'uso delle funzionalità delle app per la logica, vedere [Usare le funzionalità delle app per la logica]. Per informazioni sulle definizioni stesse dell'app per la logica, vedere l'articolo relativo alla [creazione di definizioni di app per la logica](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Usare le funzionalità delle app per la logica]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0302_2016-->