<properties 
	pageTitle="Come creare test Web" 
	description="Informazioni su come creare test Web in Azure." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-17" 
	ms.author="awills"/>

# Test Web per Siti Web di Microsoft Azure
Il sito Web di Azure è ancora in esecuzione? Risponde correttamente e con rapidità sufficiente? È possibile testare il proprio sito Web a intervalli regolari configurando un test Web. Se il sito è fuori uso oppure risponde in maniera lenta o errata, si riceverà un avviso di posta elettronica contenente i grafici in cui sono indicati i periodi di disponibilità e di velocità di risposta nel tempo.  

*Si vuole eseguire il test di qualche altro sito Web? Usare i [test Web di Application Insights][disponibili] per app Web non di Azure*

![Browse Hub](./media/insights-create-web-tests/Inisghts_WebTestBlade.png)

È possibile configurare il monitoraggio della disponibilità per qualsiasi sito Web di Azure che usi un piano Basic o Standard,  creare fino a tre test Web ed eseguire ciascuno di essi da un massimo di tre posizioni geografiche. Non è assolutamente necessario modificare il sito Web.

È anche possibile sospendere i test Web durante le distribuzioni o durante le interruzioni pianificate, in modo che la disponibilità complessiva non venga interessata.  La disponibilità complessiva viene calcolata in base a tutti i test Web, incluse le diverse posizioni selezionate.

## Come configurare un test Web
1. Per configurare un test Web assicurarsi innanzitutto che il proprio sito usi un piano **Basic** o **Standard**.
2. Scegliere quindi la parte **Test Web** nel pannello del **sito Web**:  
    ![Configure Web Tests](./media/insights-create-web-tests/Insights_ConfigurePart.png)
3. Nel pannello **Crea test Web** assegnare un nome al test Web e specificare l'URL sul quale eseguirlo.  
    ![Create Web Test](./media/insights-create-web-tests/Insights_CreateTest.png)
4. Scegliere quindi fino a tre posizioni tra le otto disponibili
5. Specificare i criteri di successo includendo i controlli del codice di stato HTTP o il contenuto della stringa sul sito stesso.
6. Infine, scegliere le impostazioni avvisi, inclusa la riservatezza e i destinatari dei messaggi di posta elettronica.  
    ![Alerts](./media/insights-create-web-tests/Inisghts_AlertCreation.png)
    - Un livello di riservatezza elevato comporta la generazione di un avviso ogni qual volta viene rilevato un errore del test in una sola posizione.
    - Un livello di riservatezza medio richiede che si sia verificato un errore in almeno la metà delle posizioni nell'arco di 10 minuti.
    - Un livello di riservatezza basso richiede che il test non sia riuscito su nessuna posizione nell'arco di 15 minuti.

Al termine, fare clic sul pulsante **Crea**. Dopo la creazione, il test Web verrà eseguito ogni cinque minuti dalle posizioni specificate, pertanto potrebbero essere necessari alcuni minuti per visualizzare i dati.

### A cosa servono le posizioni?
Viene inviata una richiesta al sito Web da queste posizioni, con le stesse modalità con cui gli utenti accederanno al sito da diverse parti del mondo. Se il sito diviene non disponibile negli Stati Uniti ma lo è ancora in Europa, sarà facile comprendere che il problema è relativo alla rete piuttosto che al server.

### Uso dei criteri di successo
In genere un risultato auspicabile del test dovrebbe indicare che il codice di stato HTTP è uguale a 200, che indica che il server ha riconosciuto l'URI e ha restituito una pagina.

Non è possibile usare caratteri jolly nella stringa di corrispondenza contenuto, ma è possibile testare qualsiasi testo normale.

## Il sito Web è fuori servizio
Se il sito Web non soddisfa i criteri di successo, il test verrà contrassegnato come non superato e la disponibilità complessiva del sito verrà ridotta. I test non superati (così come i test superati) vengono visualizzati in un grafico a dispersione nel pannello del test Web specifico.  

![Failed Test](./media/insights-create-web-tests/Insights_FailedWebTest.png)

È possibile analizzare i test non superati per determinare il motivo dell'esito negativo.  Esaminare un test Web non superato quindi scaricare e aprire il file dei risultati del test Web di Visual Studio per analizzare e comprendere il motivo dell'errore.

![Open in VS](./media/insights-create-web-tests/Insights_OpenInVS.png)

[availability]: ../app-insights-monitor-web-app-availability/

<!--HONumber=46--> 
