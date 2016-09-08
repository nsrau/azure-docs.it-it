Dopo la propagazione dei record per il nome di dominio, sarà possibile usare il browser per verificare che il nome di dominio personalizzato possa essere usato per accedere all'app Web nel Servizio app di Azure.

> [AZURE.NOTE] La propagazione del record CNAME in tutto il sistema DNS può richiedere tempo. È possibile usare un servizio come <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> per verificare se il record CNAME è disponibile.

Se non si è già aggiunta l'app Web come endpoint di Gestione traffico, è necessario eseguire questa operazione per consentire il funzionamento della risoluzione dei nomi, perché il nome di dominio personalizzato viene instradato a Gestione traffico. Gestione traffico viene quindi instradato all'app Web. Usare le informazioni della sezione [Aggiungere o eliminare endpoint](../articles/traffic-manager/traffic-manager-endpoints.md) per aggiungere l'app Web come endpoint nel profilo di Gestione traffico.

> [AZURE.NOTE] Se l'app Web non è elencata quando si aggiunge un endpoint, verificare che sia configurata per la modalità di piano di servizio app **Standard**. Per usare Gestione traffico, è necessario che l'app Web sia impostata sulla modalità **Standard**.

1. Accedere al [portale di Azure](https://portal.azure.com) dal browser.

1. Nella scheda **App Web** fare clic sul nome dell'app Web, selezionare **Impostazioni** e quindi **Domini personalizzati**

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

1. Nel pannello **Domini personalizzati** fare clic su **Aggiungi il nome host**.
	
1. Usare le caselle di testo **Nome host** per immettere il nome di dominio di Gestione traffico per l'associazione con questa app Web.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

1. Fare clic su **Convalida** per salvare la configurazione del nome di dominio.

7.  Dopo aver fatto clic su **Convalida**, Azure avvierà il flusso di lavoro Verifica del dominio, che controllerà la proprietà del dominio oltre alla disponibilità del nome host e segnalerà la riuscita o un errore dettagliato con le linee guida consigliate per correggere l'errore.

8.  Se la convalida avrà esito positivo, il pulsante **Aggiungi il nome host** diventerà attivo e sarà possibile assegnare il nome host. Ora passare al nome di dominio personalizzato in un browser. Sarà visibile l'app in esecuzione con il nome di dominio personalizzato.

	Al termine della configurazione, il nome di dominio personalizzato sarà elencato nella sezione **nomi di dominio** dell'app Web.

A questo punto sarà possibile immettere il nome di dominio di Gestione traffico nel browser e verificare che il reindirizzamento all'app Web avvenga correttamente.

<!---HONumber=AcomDC_0824_2016-->