In questo passaggio si testa il listener del gruppo di disponibilità utilizzando un'applicazione client in esecuzione nella stessa rete.

Per la connettività client, tenere presente i seguenti requisiti:

- Le connessioni client al listener devono provenire da computer che si trovano in un servizio cloud diverso da quello che ospita le repliche di disponibilità AlwaysOn.

- Se le repliche AlwaysOn si trovano in subnet diverse, i client devono specificare “MultisubnetFailover=True” nella stringa di connessione. Di conseguenza, vengono eseguiti tentativi di connessione paralleli alle repliche nelle diverse subnet. Notare che questo scenario include la distribuzione di un gruppo di disponibilità AlwaysOn tra più aree.

Un esempio sarebbe connettersi al listener da una delle macchine virtuali nella stessa rete virtuale Azure (ma non quello che ospita una replica). Un modo semplice per completare questo test consiste nel provare a connettere SSMS al listener del gruppo di disponibilità. Un altro metodo semplice consiste nell'eseguire [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx) come indicato di seguito:

	sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [AZURE.NOTE]Se il valore EndpointPort è 1433, non è necessario specificarlo nella chiamata. La chiamata precedente presuppone inoltre che il computer client viene unito allo stesso dominio e che il chiamante dispone delle autorizzazioni necessarie nel database utilizzando l'autenticazione di windows.

Durante il test del listener, assicurarsi di eseguire il failover del gruppo di disponibilità per assicurarsi che i client possano connettersi al listener attraverso i failover.

<!---HONumber=Oct15_HO3-->