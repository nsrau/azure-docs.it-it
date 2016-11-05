Dopo aver aggiunto una condizione, è possibile svolgere delle operazioni interessanti con i dati generati dal trigger. Attenersi alla procedura seguente per aggiungere l'azione **Salesforce - Ottenere oggetto**. Questa azione consente di ottenere i dati ogni volta che viene creato un nuovo lead. Si aggiungerà anche una seconda azione che utilizzerà i dati dall'azione Salesforce - Ottenere oggetto per inviare un messaggio di posta elettronica tramite il connettore di Office 365.

Per configurare questa azione, è necessario fornire le informazioni indicate di seguito. Si noterà che è facile usare i dati generati dal trigger come input per alcune proprietà del nuovo file:

| Creare proprietà file | Descrizione |
| --- | --- |
| Tipo di oggetto |Questo è il tipo di oggetto Salesforce a cui si è interessati. Alcuni esempi sono Lead, Account, ecc. |
| ID oggetto |Rappresenta un identificatore dell'oggetto. |

1. Selezionare il collegamento **Aggiungi un'azione**. Viene aperta la casella di ricerca per cercare qualsiasi azione si desideri eseguire. In questo esempio, l'interesse è rivolto alle azioni di Salesforce.  
   ![Immagine di azione Salesforce 1](./media/connectors-create-api-salesforce/action-1.png)  
2. Immettere *salesforce* per cercare le azioni correlate a salesforce.
3. Selezionare **Salesforce - Ottenere oggetto** come azione da intraprendere. **Nota**: verrà richiesto di autorizzare l'app per la logica ad accedere all'account di Salesforce, se non lo si è già fatto in precedenza.  
   ![Immagine di azione Salesforce 2](./media/connectors-create-api-salesforce/action-2.png)  
4. Viene visualizzato il controllo **Ottenere oggetto**.
5. Selezionare *lead* come tipo di oggetto.
6. Selezionare il controllo **ID oggetto**.
7. Selezionare **...** per espandere l'elenco dei token utilizzabili come input per le azioni.  
   ![Immagine di azione Salesforce 3](./media/connectors-create-api-salesforce/action-3.png)  
8. Selezionare il controllo **ID lead** per visualizzarlo.  
   ![Immagine di azione Salesforce 4](./media/connectors-create-api-salesforce/action-4.png)  
9. Il token dell'ID lead si trova ora nel controllo dell'ID oggetto, indicando che l'azione Ottieni oggetto cercherà un lead con un ID identico a quello del lead che ha attivato l'app per la logica.  
   ![Immagine di azione Salesforce 5](./media/connectors-create-api-salesforce/action-5.png)  
10. Salvare il lavoro. A questo punto l'azione Ottenere oggetto è stata aggiunta all'app per la logica. Il controllo Ottenere oggetto dovrebbe assomigliare a:  
    ![Immagine di azione Salesforce 6](./media/connectors-create-api-salesforce/action-6.png)  

Dopo aver aggiunto un'azione per ottenere un lead, è possibile effettuare operazione interessanti con il lead appena creato. In un'azienda, si può voler inviare un messaggio di posta elettronica per notificare a un elenco di distribuzione che è stato creato un nuovo lead. Il connettore Office 365 viene utilizzato per inviare un messaggio di posta elettronica con alcune delle informazioni pertinenti dal nuovo oggetto lead presente in Salesforce.

1. Selezionare **Aggiungi un'azione** e digitare *messaggio di posta elettronica* nel controllo di ricerca per filtrare le azioni e visualizzare solo quelle correlate all'invio e alla ricezione di messaggi di posta elettronica.
2. Selezionare la voce di elenco **Office 365 Outlook - Inviare un messaggio di posta elettronica**. Se non è ancora stata creata una *connessione* all'account di Office 365, verrà richiesto di immettere le credenziali di Office 365 per crearla adesso. Al termine dell'operazione, viene visualizzato il controllo **Inviare un messaggio di posta elettronica**.  
   ![Immagine di azione Salesforce 7](./media/connectors-create-api-salesforce/action-7.png)  
3. Immettere l'indirizzo di posta elettronica a cui inviare il messaggio nel controllo **A**.
4. Nel controllo **Subject** (Oggetto) immettere *New Lead created* (Nuovo lead creato), quindi selezionare il token *Company* (Azienda). Verrà visualizzato il campo *Azienda* dal nuovo lead creato in Salesforce.
5. Nel controllo **Corpo messaggio** , è possibile selezionare qualsiasi token dal nuovo oggetto lead ed è inoltre possibile immettere qualunque testo che si desidera far apparire nel corpo del messaggio di posta elettronica. Ecco un esempio:  
   ![Immagine di azione Salesforce 8](./media/connectors-create-api-salesforce/action-8.png)  
6. Salvare il flusso di lavoro.

È tutto. Ora l'app per la logica è completa.

Ora è possibile testare l'app per la logica: in Salesforce, creare un nuovo lead che soddisfi la condizione creata. Se ci si è attenuti pienamente a questa procedura dettagliata, è sufficiente creare un lead con un indirizzo di posta elettronica che contenga la dicitura *amazon.com*. L'app per la logica dovrebbe attivarsi in pochi secondi e il risultato dovrebbe essere simile a:  
![Immagine di azione Salesforce 9](./media/connectors-create-api-salesforce/action-9.png)  

<!---HONumber=AcomDC_0914_2016-->