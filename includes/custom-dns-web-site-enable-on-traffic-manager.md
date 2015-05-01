Dopo la propagazione dei record per il nome di dominio, sarà possibile usare il browser per verificare che il nome di dominio personalizzato possa essere usato per accedere all'app Web nel servizio app di Azure.

> [AZURE.NOTE] La propagazione del record CNAME in tutto il sistema DNS può richiedere tempo. È possibile usare un servizio come <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> per verificare che CNAME sia disponibile.

Se non si è già aggiunta l'app Web come endpoint di Gestione traffico, è necessario eseguire questa operazione per consentire il funzionamento della risoluzione dei nomi, perché il nome di dominio personalizzato viene instradato a Gestione traffico. Gestione traffico viene quindi instradato all'app Web. Usare le informazioni della sezione [Aggiungere o eliminare endpoint](http://msdn.microsoft.com/library/windowsazure/hh744839.aspx) per aggiungere l'app Web come endpoint nel profilo di Gestione traffico.

> [AZURE.NOTE] Se l'app Web non è elencata quando si aggiunge un endpoint, verificare che sia configurata per la modalità di piano di servizio app **Standard**. Per usare Gestione traffico, è necessario che l'app Web sia impostata sulla modalità **Standard**.

<!--HONumber=52-->
