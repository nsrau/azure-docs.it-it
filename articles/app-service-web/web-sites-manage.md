<properties 
	pageTitle="Gestione delle app Web tramite il portale di Azure" 
	description="Panoramica delle attività di gestione delle app Web in Microsoft Azure attraverso il portale di Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	writer="mwasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>

# Gestione delle app Web tramite il portale di Azure

Nel [portale di Azure](https://portal.azure.com), è possibile gestire le app Web attraverso una serie di *pannelli*, che sono contenitori di diversi componenti. (Per una panoramica del portale di Azure, fare riferimento alla guida sulle modifiche del [portale](http://go.microsoft.com/fwlink/?LinkId=529715)).

Per visualizzare il pannello dell'app Web, fare clic su **Home** e sul nome dell'app o fare clic su **Sfoglia** per visualizzare tutte le risorse di Azure.

![](./media/web-sites-manage/manage01.png)

La parte superiore del pannello presenta comandi per alcuni azioni comuni:

- **Impostazioni** per vedere un elenco di tutte le impostazioni di gestione.

- **Sfoglia** per aprire l'app Web nella finestra del browser.

- Sono presenti altri pulsanti per aprire o chiudere l'app, scambiare gli slot di distribuzione, eliminare l'app e così via.

Elementi **essenziali** elenca informazioni di base relative all'app, tra cui URL, percorso, piano del [servizio app](http://go.microsoft.com/fwlink/?LinkId=529714) e stato corrente (in esecuzione, arrestata, ecc.).

In Elementi **essenziali** sono presenti diversi componenti che possono essere usati per monitorare, gestire e distribuire l'app. Per personalizzare il layout del pannello, fare clic con il pulsante destro del mouse e selezionare **Personalizza** o **Aggiungi componenti**. La parte restante del documento descrive i componenti nel layout predefinito.

## Monitoraggio

In **Monitoraggio** è presente un grafico che mostra le metriche dell'app Web. Per configurare il grafico, fare clic su **Modifica**. È possibile selezionare l'intervallo di tempo e le metriche da visualizzare. Le metriche disponibili includono numero delle richieste, tempo di risposta medio, errori del server e tempo CPU.

![](./media/web-sites-manage/manage02.png)

Per aggiungere una regola avvisi, fare clic sul grafico, quindi fare clic su **Aggiungi avviso**. Una regola avvisi avvisa quando una metrica specifica raggiunge una determinata soglia. Ad esempio, è possibile essere avvisati quando il numero di errori del server supera un valore specifico in un periodo di 5 minuti.

**Monitoraggio** inoltre include componenti per configurare l'analisi, configurare il monitoraggio dell'applicazione e creare test di disponibilità. Per ulteriori informazioni, vedere [Informazioni di base sul monitoraggio di App Web nel servizio app di Azure](web-sites-monitor.md).

## Utilizzo

![](./media/web-sites-manage/manage03.png)

- **Archiviazione file system** mostra quanto spazio di archiviazione file sta usando l'app.
- **Quote** mostra il modo in cui l'app sta usando la sua quota utilizzo risorse. Fare clic sul componente per visualizzare i dettagli.
- **Scalabilità** consente di scalare il numero di istanze e configurare la scalabilità automatica. Vedere [Scalare un'app Web nel servizio app di Azure](../how-to-scale-websites.md).  
- **Spesa stimata** mostra una stima dei costi dell'app.
- **Livello di prezzo** consente di modificare il livello di prezzo.

## Operazioni

![](./media/web-sites-manage/manage04.png)

- **Eventi**. Fare clic per visualizzare i log eventi.  
- **Regole di avviso**. Fare clic per visualizzare le regole avvisi e aggiungere nuovi avvisi.
- **Log di streaming** (A). Fare clic per visualizzare i log delle applicazioni. Per attivare i log, andare a **Impostazioni** e aprire il pannello **Log di diagnostica**. 
- **Console**. Fare clic per aprire una riga di comando in esecuzione nel portale. È possibile usarla per eseguire comandi quali `mkdir` e `dir`.  
- **Esplora processi** (B). Fare clic per visualizzare i processi in esecuzione nell'app, tra cui working set e conteggio dei thread.

## Distribuzione
 
![](./media/web-sites-manage/manage05.png)

- Configurare la distribuzione continua. Vedere [Uso di Git per distribuire App Web nel servizio app di Azure](web-sites-publish-source-control.md)
- Slot di distribuzione. Vedere [Distribuzione di app Web negli ambienti temporanei in Azure App Service](web-sites-staged-publishing.md)
- Configurare le credenziali di distribuzione per Git o FTP. 

## Rete

- Connessione dell'app a una rete virtuale
- Aggiunta di connessioni ibride

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Passaggi successivi

- [Scalare un'app Web in Servizio app di Azure](../how-to-scale-websites.md)
- [Eseguire attività in background con processi Web](web-sites-create-web-jobs.md)
- [Backup di App Web di Azure](web-sites-backup.md) e [ripristino](web-sites-restore.md)
- [Informazioni di base sul monitoraggio di App Web nel servizio app di Azure](web-sites-monitor.md)

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=62-->