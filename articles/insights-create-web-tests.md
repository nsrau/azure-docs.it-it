<properties title="How to create web test" pageTitle="How to create web test" description="Learn how to create web tests in Azure." authors="stepsic" />

Test Web
========

Il sito Web di Azure è ancora in esecuzione? Risponde correttamente e con rapidità sufficiente? È possibile testare il proprio sito Web a intervalli regolari configurando un test Web. Se il sito è fuori uso oppure risponde in maniera lenta o errata, si riceverà un avviso di posta elettronica contenente i grafici in cui sono indicati i periodi di disponibilità e di velocità di risposta nel tempo.

   ![Hub di esplorazione](./media/insights-create-web-tests/Inisghts_WebTestBlade.png)

È possibile configurare il monitoraggio della disponibilità per qualsiasi sito Web di Azure che utilizzi un piano Basic o Standard, creare fino a tre test Web ed eseguire ciascuno di essi da un massimo di tre posizioni geografiche. Non è assolutamente necessario modificare il sito Web.

È inoltre possibile sospendere i test Web durante le distribuzioni oppure le interruzioni di corrente note, in modo che la disponibilità complessiva non venga interessata. La disponibilità complessiva viene calcolata in base a tutti i test Web, incluse le diverse posizioni selezionate.

Come configurare un test Web
----------------------------

1.  Per configurare un test Web assicurarsi innanzitutto che il proprio sito utilizzi un piano **Basic** o **Standard**.
2.  Scegliere quindi la parte **Web Test** sul blade del **sito Web**:

    ![Configurare i test Web](./media/insights-create-web-tests/Insights_ConfigurePart.png)

3.  Nel blade **Create web test** assegnare un nome al test Web e specificare l'URL sul quale eseguirlo.

    ![Creare il sito Web](./media/insights-create-web-tests/Insights_CreateTest.png)

4.  Scegliere quindi fino a tre posizioni tra le otto disponibili

5.  Specificare i criteri di successo includendo i controlli del codice di stato HTTP o il contenuto della stringa sul sito stesso.

6.  Infine, scegliere le impostazioni avvisi, inclusa la riservatezza e i destinatari dei messaggi di posta elettronica.

    ![Avvisi](./media/insights-create-web-tests/Inisghts_AlertCreation.png)

    -   Un livello di riservatezza elevato comporta la generazione di un avviso ogni qual volta viene rilevato un errore del test in una sola posizione.
    -   Un livello di riservatezza medio richiede che si sia verificato un errore in almeno la metà delle posizioni nell'arco di 10 minuti.
    -   Un livello di riservatezza basso richiede che il test non sia riuscito su nessuna posizione nell'arco di 15 minuti.

Al termine, fare clic sul pulsante **Create**. Dopo la creazione, il test Web verrà eseguito ogni cinque minuti dalle posizioni specificate, pertanto potrebbero essere necessari alcuni minuti per visualizzare i dati.

### A cosa servono le posizioni?

Viene inviata una richiesta al sito Web da queste posizioni, così come gli utenti accederanno al sito da diverse parti del mondo. Se il sito non è disponibile negli Stati Uniti ma lo è ancora in Europa, sarà facile comprendere che il problema è relativo alla rete piuttosto che al server.

### Utilizzo dei criteri di successo

In genere un risultato auspicabile del test dovrebbe indicare che il codice di stato HTTP è uguale a 200, che indica che il server ha riconosciuto l'URI e ha restituito una pagina.

Non è possibile utilizzare caratteri jolly nella stringa di corrispondenza contenuto, ma è possibile testare qualsiasi testo normale.

Il sito Web è fuori servizio
----------------------------

Se il sito Web non soddisfa i criteri di successo, il test verrà contrassegnato come non superato e la disponibilità complessiva del sito verrà ridotta. I test non superati (nonché i test superati) vengono visualizzati in un grafico a dispersione nel blade di test Web specifico.

   ![Test non superato](./media/insights-create-web-tests/Insights_FailedWebTest.png)

È possibile analizzare i test non superati per determinare il motivo dell'esito negativo. Esaminare un test Web non superato quindi scaricare e aprire il file dei risultati del test Web di Visual Studio per analizzare e comprendere il motivo dell'errore.

   ![Apri in VS](./media/insights-create-web-tests/Insights_OpenInVS.png)

