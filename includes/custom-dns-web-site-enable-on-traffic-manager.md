Dopo la propagazione dei record per il nome di dominio, sarà possibile usare il browser per verificare che il nome di dominio personalizzato possa essere usato per accedere al sito Web.

> [WACOM.NOTE] La propagazione del record CNAME in tutto il sistema DNS può richiedere tempo. È possibile utilizzare un servizio come <http://www.digwebinterface.com/> per verificare se il record CNAME è disponibile.

Se non si è già aggiunto il sito Web come endpoint di Gestione traffico, è necessario eseguire questa operazione per consentire il funzionamento della risoluzione dei nomi, perché il nome di dominio personalizzato viene instradato a Gestione traffico. Gestione traffico viene quindi instradato al sito Web. Usare le informazioni della sezione [Aggiungere o eliminare endpoint][Aggiungere o eliminare endpoint] per aggiungere il sito Web come endpoint nel profilo di Gestione traffico.

> [WACOM.NOTE] Se il sito Web non è elencato quando si aggiunge un endpoint, verificare che sia configurato per la modalità standard. Per usare Gestione traffico, è necessario che il sito Web sia impostato sulla modalità standard.

  [Aggiungere o eliminare endpoint]: http://msdn.microsoft.com/it-it/library/windowsazure/hh744839.aspx
