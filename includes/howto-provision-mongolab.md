È possibile eseguire la sottoscrizione a un database MongoDB completamente gestito e ospitato da Azure nello Store di Azure. A questo scopo, attenersi alla procedura seguente:

1. Accedere al portale di gestione di Azure.
1. Fare clic su **Nuovo**.  
![Nuovo][button-new]
1. Selezionare **Store**.  
![Store][button-store]
1. Selezionare MongoLab. Il database è disponibile nella categoria **App Services** e in **All**.  
![MongoLab][entry-mongolab]
1. Fare clic su **Avanti**.  
![Avanti][button-next]  
  Verrà visualizzata la voce dello Store relativa a MongoLab.  
![NewMongoLab][screen-newmongolab]
1. Selezionare l'opzione **Subscription** desiderata.
1. In **Name** digitare un nome per il database. Il nome può contenere solo caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. MongoLab richiede inoltre che tale nome sia univoco, pertanto potrebbe essere richiesto all'utente di inviare di nuovo la richiesta qualora il nome sia accettato.
1. Selezionare la posizione desiderata in **Region**.
1. Fare clic su **Avanti**.  
![Avanti][button-next]
1. Verificare le informazioni relative all'acquisto presso il negozio, quindi fare clic su **Purchase** per confermare.  
![Avanti][button-purchase]  
1. Sul pulsante di stato della barra degli strumenti viene visualizzato lo stato del provisioning.  
![ProgressButton][button-progress]  
Al termine del provisioning, verrà visualizzato un messaggio che indica il completamento dell'operazione.  
![SuccessMessage][message-success]

Congratulazioni. È stato eseguito il provisioning di un database MongoDB nella posizione di Azure selezionata. Sarà ora possibile accedere all'interfaccia di gestione e al supporto 24 ore su 24, 7 giorni su 7.

[button-new]: ./media/howto-provision-mongolab/button-new.png
[button-store]: ./media/howto-provision-mongolab/button-store.png
[button-next]: ./media/howto-provision-mongolab/button-next.png
[button-purchase]: ./media/howto-provision-mongolab/button-purchase.png
[button-progress]: ./media/howto-provision-mongolab/button-progress.png
[entry-mongolab]: ./media/howto-provision-mongolab/entry-mongolab.png 
[screen-newmongolab]: ./media/howto-provision-mongolab/screen-newmongolab.png 
[message-success]: ./media/howto-provision-mongolab/message-provisionsuccess.png

<!--HONumber=42-->
