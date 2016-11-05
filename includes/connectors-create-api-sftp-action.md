Una volta aggiunto un trigger, è possibile svolgere delle operazioni interessanti con i dati generati dal trigger. Attenersi alla procedura seguente per aggiungere un'azione **SFTP - Estrai cartella**. Questa azione consente di estrarre i contenuti di un file se le condizioni impostate vengono soddisfatte.

Per configurare questa azione, è necessario fornire le informazioni indicate di seguito. Si noterà che è facile usare i dati generati dal trigger come input per alcune proprietà del nuovo file:

| SFTP - Proprietà della cartella di estrazione | Descrizione |
| --- | --- |
| Percorso file di archiviazione di origine |Si tratta del percorso del file estratto. È possibile selezionare uno dei token da un'azione precedente o esplorare il server SFTP per trovare il percorso del file. |
| Percorso cartella di destinazione |Questo è il percorso in cui verranno posizionati i file estratti. È possibile selezionare uno dei token da un'azione precedente come percorso di destinazione oppure esplorare il server SFTP e selezionare un percorso. |
| Sovrascrivere? |Se nel percorso della cartella di destinazione è presente un file con lo stesso nome del file estratto, questo messaggio indica che è possibile sovrascrivere il file esistente. |

Iniziamo aggiungendo l'azione per estrarre i file se la condizione definita in precedenza restituisce il valore *True*.

1. Selezionare **Aggiungi un'azione**.  
   ![Immagine di condizione dell'azione SFTP 6](./media/connectors-create-api-sftp/condition-6.png)  
2. Selezionare l'azione **SFTP - Estrai cartella**  
   ![Immagine di condizione dell'azione SFTP 7](./media/connectors-create-api-sftp/condition-7.png)  
3. Selezionare **Percorso file di archiviazione di origine**  
   ![Immagine di condizione dell'azione SFTP 9](./media/connectors-create-api-sftp/condition-9.png)  
4. Selezionare il token **Percorso file**. Ciò indica che si utilizzerà il percorso del file che il trigger ha rilevato come percorso file di archiviazione di origine.  
   ![Immagine di condizione dell'azione SFTP 10](./media/connectors-create-api-sftp/condition-10.png)  
5. Selezionare **Percorso cartella di destinazione**  
   ![Immagine di condizione dell'azione SFTP 11](./media/connectors-create-api-sftp/condition-11.png)  
6. Selezionare il token **Percorso file**. Ciò indica che si utilizza il percorso del file che il trigger ha rilevato come percorso di destinazione dei file estratti.
7. Immettere *\\ExtractedFile* nel controllo **Percorso cartella di destinazione**. Eseguire questa operazione dopo il token del percorso file nel controllo Percorso cartella di destinazione.  
   ![Immagine di condizione dell'azione SFTP 12](./media/connectors-create-api-sftp/condition-12.png)  
8. Immettere *True* nel controllo **Sovrascrivere?* per indicare che i file esistenti devono essere sovrascritti se hanno lo stesso nome dei file estratti.  
   ![Immagine di condizione dell'azione SFTP 13](./media/connectors-create-api-sftp/condition-13.png)  
9. Salvare le modifiche al flusso di lavoro

<!---HONumber=AcomDC_0727_2016-->
