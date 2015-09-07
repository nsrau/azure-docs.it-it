Dopo la propagazione dei record per il nome di dominio, sarà possibile usare il browser per verificare che il nome di dominio personalizzato possa essere usato per accedere all'app Web nel Servizio app di Azure.

> [AZURE.NOTE]La propagazione del record CNAME in tutto il sistema DNS può richiedere tempo. È possibile usare un servizio come <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> per verificare se il record CNAME è disponibile.

Se non si è già aggiunta l'app Web come endpoint di Gestione traffico, è necessario eseguire questa operazione per consentire il funzionamento della risoluzione dei nomi, perché il nome di dominio personalizzato viene instradato a Gestione traffico. Gestione traffico viene quindi instradato all'app Web. Usare le informazioni della sezione [Aggiungere o eliminare endpoint](../traffic-manager/traffic-manager-endpoints.md) per aggiungere l'app Web come endpoint nel profilo di Gestione traffico.

> [AZURE.NOTE]Se l'app Web non è elencata quando si aggiunge un endpoint, verificare che sia configurata per la modalità di piano di servizio app **Standard**. Per usare Gestione traffico, è necessario che l'app Web sia impostata sulla modalità **Standard**.

1. Accedere al [portale di Azure](https://portal.azure.com) dal browser.

2. Nella scheda **App Web** fare clic sul nome dell'app Web, selezionare **Impostazioni** e quindi **Domini personalizzati e SSL**.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Nel pannello **Domini personalizzati e SSL** fare clic su **Porta domini esterni**.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. Usare le caselle di testo **NOMI DI DOMINIO** per immettere il nome di dominio di Gestione traffico (contoso.trafficmanager.net) per l'associazione con questa app Web.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. Fare clic su **Salva** per salvare la configurazione del nome di dominio.

	Al termine della configurazione, il nome di dominio personalizzato sarà elencato nella sezione **nomi di dominio** dell'app Web.

A questo punto sarà possibile immettere il nome di dominio di Gestione traffico (contoso.trafficmanager.net) nel browser e verificare che il reindirizzamento all'app Web avvenga correttamente.

<!---HONumber=August15_HO9-->