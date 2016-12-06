Una volta aggiunto un trigger, è possibile svolgere delle operazioni interessanti con i dati generati dal trigger. Attenersi alla procedura seguente per aggiungere l'azione **SharePoint Online - Creare file** . Questa azione consente di creare un file in SharePoint Online ogni volta che il trigger del nuovo elemento si attiva. 

Per configurare questa azione, è necessario fornire le informazioni indicate di seguito. Dopo aver fornito queste informazioni, si noterà che è facile usare i dati generati dal trigger come input per alcune proprietà del nuovo file:

| Creare proprietà file | Descrizione |
| --- | --- |
| Site URL |Si tratta dell'URL del sito di SharePoint Online in cui si desidera creare il nuovo file. Selezionare il sito dall'elenco visualizzato. |
| Percorso della cartella |Questa è la cartella (all'URL del sito selezionato nel passaggio precedente) in cui verrà posizionato il nuovo file. Cercare e selezionare la cartella. |
| Nome file |Questo è il nome del file creato. |
| Contenuto del file |Il contenuto che verrà scritto nel file. |

1. Selezionare **+ nuovo passaggio** per aggiungere l'azione.  
   ![Immagine di azione SharePoint Online 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. Selezionare il collegamento **Aggiungi un'azione** . Viene aperta la casella di ricerca per cercare qualsiasi azione si desideri eseguire. In questo esempio, l'interesse è rivolto alle azioni di SharePoint.    
   ![Immagine di azione SharePoint Online 2](./media/connectors-create-api-sharepointonline/action-2.png)    
3. Immettere *sharepoint* per cercare le azioni correlate a SharePoint.
4. Selezionare **SharePoint Online - Creare file** come azione da eseguire.   **Nota**: verrà richiesto di autorizzare l'app per la logica ad accedere all'account di SharePoint, se in precedenza non è stata creata una connessione a SharePoint Online.    
   ![Immagine di azione SharePoint Online 3](./media/connectors-create-api-sharepointonline/action-3.png)    
5. Viene visualizzato il controllo **Crea file** .   
   ![Immagine di azione SharePoint Online 4](./media/connectors-create-api-sharepointonline/action-4.png)     
6. Selezionare **URL sito** e individuare il sito in cui si desidera creare il file.     
   ![Immagine di azione SharePoint Online 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. Selezionare **Percorso cartella** e individuare la cartella in cui posizionare il nuovo file.  
   ![Immagine di azione SharePoint Online 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. Selezionare il controllo **Nome file** e immettere il nome del file che si desidera creare. Qui è possibile immettere direttamente il nome del file oppure utilizzare una delle proprietà dal trigger creato in precedenza. Per farlo, selezionare le proprietà dall'elenco di **output da Quando viene creato un nuovo elemento**. Questo elenco viene visualizzato solo dopo aver selezionato il controllo **Nome file** . In questa procedura dettagliata, è stato selezionato l'ID (ID del nuovo elemento in elenco) come nome del file creato attraverso l'azione **SharePoint Online - Creare file** .    
   ![Immagine di azione SharePoint Online 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. Selezionare il controllo **Contenuto file** e immettere il contenuto da scrivere nel file che verrà creato. Per il contenuto del file è possibile utilizzare una delle proprietà dal trigger creato in precedenza. È sufficiente selezionare le proprietà nell'elenco visualizzato. In alternativa, è possibile immettere il testo **Contenuto file** direttamente nel controllo. In questo esempio, sono state selezionate alcune proprietà e sono stati aggiunti degli spazi e un trattino tra le diverse proprietà.        
   ![Immagine di azione SharePoint Online 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. Salvare le modifiche al flusso di lavoro  
11. Congratulazioni, ora si dispone di un'app per la logica pienamente operativa che si avvia quando un nuovo elemento viene aggiunto a un elenco SharePoint Online. L'app quindi creerà un file, utilizzando alcune delle proprietà del nuovo elemento in elenco.  È ora possibile mettere alla prova l'app creando un nuovo elemento nell'elenco di SharePoint. 



<!--HONumber=Nov16_HO3-->


