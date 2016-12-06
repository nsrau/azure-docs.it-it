## <a name="connect-to-outlookcom"></a>Connessione ad Outlook.com
### <a name="prerequisites"></a>Prerequisiti
* Un account Outlook.com

Prima di poter usare l'account Outlook.com in un'app per la logica, è necessario autorizzare l'app per la logica a connettersi all'account Outlook.com. Fortunatamente, è possibile eseguire questa operazione in modo facile dalla propria app per la logica nel portale di Azure. 

Ecco i passaggi per autorizzare l'app per la logica a connettersi all'account Outlook.com:

1. Tutte le app per la logica dovranno essere avviate da un trigger. Quindi dopo aver creato l'app per la logica, la finestra di progettazione viene aperta e visualizza un elenco di trigger che è possibile usare per avviare l'app per la logica:
   
   ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. Immettere "outlook" nella casella di ricerca. Si noti che l'elenco è filtrato per elencare tutti i trigger contenenti "Outlook" nel nome: ![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Selezionare **Office 365 Outlook - All'arrivo di un nuovo messaggio di posta elettronica**.   
   Se non sono mai state create connessioni ad Outlook prima d'ora, verrà chiesto di fornire le credenziali di Outlook.com. Queste credenziali verranno usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati dell'account Outlook.com: ![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Fornire le credenziali per Outlook ed eseguire l'accesso: ![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
   È tutto. A questo punto è stata creata una connessione a Outlook. Questa connessione potrà essere usata in qualsiasi altra app per la logica creata.



<!--HONumber=Nov16_HO3-->


