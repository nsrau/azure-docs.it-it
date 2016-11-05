Una volta aggiunto un trigger, è possibile svolgere delle operazioni interessanti con i dati generati dal trigger. Attenersi alla procedura seguente per aggiungere l'azione **SharePoint Online - Creare file**. Questa azione consente di creare un file in SharePoint Online ogni volta che il trigger del nuovo elemento si attiva.

Per configurare questa azione, è necessario fornire le informazioni indicate di seguito. Si noterà che è facile usare i dati generati dal trigger come input per alcune proprietà del nuovo file:

| Creare proprietà file | Descrizione |
| --- | --- |
| Site URL |Si tratta dell'URL del sito di SharePoint Online in cui si desidera creare il nuovo file. Selezionare il sito dall'elenco visualizzato. |
| Percorso della cartella |Questa è la cartella (all'URL del sito) in cui verrà posizionato il nuovo file. Cercare e selezionare la cartella. |
| Nome file |Questo è il nome del file creato. |
| Contenuto del file |Il contenuto che verrà scritto nel file. |

1. Selezionare **+ nuovo passaggio** per aggiungere l'azione. ![](./media/connectors-create-api-sharepointonline/action-1.png)
2. Selezionare il collegamento **Aggiungi un'azione**. Viene aperta la casella di ricerca per cercare qualsiasi azione si desideri eseguire. In questo esempio, l'interesse è rivolto alle azioni di SharePoint. ![](./media/connectors-create-api-sharepointonline/action-2.png)
3. Immettere *sharepoint* per cercare le azioni correlate a SharePoint.
4. Selezionare **SharePoint Online - Creare file** come azione da eseguire. **Nota**: verrà richiesto di autorizzare l'app per la logica ad accedere all'account di SharePoint, se non lo si è già fatto in precedenza. ![](./media/connectors-create-api-sharepointonline/action-3.png)
5. Viene visualizzato il controllo **Crea file**. ![](./media/connectors-create-api-sharepointonline/action-4.png)
6. Selezionare **URL sito** e individuare il sito in cui si desidera creare il file. ![](./media/connectors-create-api-sharepointonline/action-5.png)
7. Selezionare **Percorso cartella** e individuare la cartella in cui posizionare il nuovo file. ![](./media/connectors-create-api-sharepointonline/action-6.png)
8. Selezionare il controllo **Nome file** e immettere il nome del file che si desidera creare. Per il nome del file è possibile utilizzare una delle proprietà dal trigger creato in precedenza, semplicemente selezionandolo dall'elenco visualizzato. ![](./media/connectors-create-api-sharepointonline/action-7.png)
9. Selezionare il controllo **Contenuto file** e immettere il contenuto da scrivere nel file che verrà creato. Per il contenuto del file è possibile utilizzare una delle proprietà dal trigger creato in precedenza. È sufficiente selezionare le proprietà nell'elenco visualizzato. In alternativa, è possibile immettere il testo **Contenuto file** direttamente nel controllo. In questo esempio, sono state selezionate alcune proprietà e sono stati aggiunti degli spazi e un trattino tra le diverse proprietà. ![](./media/connectors-create-api-sharepointonline/action-8.png)
10. Salvare le modifiche al flusso di lavoro

<!---HONumber=AcomDC_0727_2016-->