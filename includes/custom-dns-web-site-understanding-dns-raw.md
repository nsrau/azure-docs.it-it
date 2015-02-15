Domain Name System (DNS) consente di individuare risorse su Internet. Se ad esempio si immette un indirizzo di sito Web nel browser o si fa clic su un collegamento in una pagina Web, viene usato DNS per convertire il dominio in un indirizzo IP. L'indirizzo IP è paragonabile a un indirizzo postale, solo che non è altrettanto intuitivo. È ad esempio molto più facile ricordare un nome DNS come **contoso.com** anziché un indirizzo IP come 192.168.1.88 o 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Il sistema DNS è basato su *records*. I record associano uno specifico *name*, come **contoso.com**, a un indirizzo IP o a un altro nome DNS. Quando un'applicazione, ad esempio un Web browser, cerca un nome in DNS, trova il record e utilizza come indirizzo il valore a cui punta. Se il valore a cui punta è un indirizzo IP, il browser utilizzerà questo valore. Se punta a un altro nome DNS, l'applicazione deve risolverlo di nuovo. In ultima analisi, la risoluzione del nome terminerà sempre in un indirizzo IP.

A ogni sito Web di Azure creato verrà automaticamente assegnato un nome DNS. Il formato di questo nome sarà **&lt;nomesito&gt;.azurewebsites.net**. È anche disponibile un indirizzo IP virtuale da usare quando si creano i record DNS, quindi è possibile creare record che puntano ad **.azurewebsites.net** oppure all'indirizzo IP.

> [AZURE.NOTE] L'indirizzo IP del sito Web verrà cambiato se si elimina il sito e poi lo si crea di nuovo oppure se la modalità del sito Web viene modificata in gratuita dopo essere stata impostata su condivisa, di base o standard.

Sono inoltre disponibili più tipi di record, ognuno con funzioni e limitazioni specifiche, ma per i siti Web sono importanti solo due tipi, ovvero *A* e *CNAME*.

###Record di indirizzo (record A)

Un record A consente di eseguire il mapping di un dominio, ad esempio **contoso.com** o **www.contoso.com**, *or a wildcard domain* ad esempio **\*.contoso.com**, a un indirizzo IP. Nel caso di un sito Web di Azure, si tratta dell'indirizzo IP virtuale del servizio o di uno specifico indirizzo IP acquistato per il sito.

I principali vantaggi di un record A rispetto a un record CNAM sono i seguenti:

* È possibile eseguire il mapping di un dominio radice, ad esempio **contoso.com**, a un indirizzo IP. Molti registrar consentono di usare solo record A in questo caso.

* È possibile usare un'unica voce con un carattere jolly, ad esempio **\*.contoso.com**, che gestirà le richieste per più sottodomini, ad esempio **mail.contoso.com**, **blogs.contoso.com** o **www.contso.com**.

> [AZURE.NOTE] Poiché il mapping di un record A viene eseguito a un indirizzo IP statico, non sarà possibile risolvere automaticamente le modifiche apportate all'indirizzo IP del sito Web. Quando si configurano le impostazioni di un nome di dominio personalizzato per il sito Web, viene fornito un indirizzo IP da usare con i record A. Questo valore può però cambiare se si elimina e si ricrea il sito Web o nel caso in cui lo si riporti in modalità gratuita.

###Record alias (record CNAME)

Un record CNAME consente di eseguire il mapping di un nome DNS *specific*, ad esempio **mail.contoso.com** o **www.contoso.com**, a un altro nome di dominio (canonico). Nel caso dei siti Web di Azure, il nome di dominio canonico è il nome di dominio **&lt;nomesito>.azurewebsites.net** del proprio sito Web. Dopo la creazione, il record CNAME crea a sua volta un alias per il nome di dominio **&lt;nomesito>.azurewebsites.net**. La voce CNAME viene automaticamente risolta nell'indirizzo IP del nome di dominio **&lt;nomesito>.azurewebsites.net**, quindi se l'indirizzo IP del sito Web cambia non sarà necessaria alcuna azione.

> [AZURE.NOTE] Alcuni registrar consentono di eseguire il mapping solo dei sottodomini se si usa un record CNAME, ad esempio **www.contoso.com**, e non dei nomi radice come **contoso.com**. Per altre informazioni sui record CNAME, vedere la documentazione fornita dal registrar, la <a href="http://en.wikipedia.org/wiki/CNAME_record">pagina di Wikipedia sui record CNAME</a> oppure il documento di <a href="http://tools.ietf.org/html/rfc1035">IETF relativo a implementazione e specifiche dei nomi di dominio</a>.

###Specifiche DNS per i siti Web di Azure

Per usare un record A con Siti Web di Azure, è prima necessario creare uno dei seguenti record CNAME:

* **Per il dominio radice o sottodomini con caratteri jolly**: un nome DNS **awverify** in **awverify.&lt;nomesitoWeb&gt;.azurewebsites.net**.

* **Per un sottodominio specifico**: un nome DNS **awverify.&lt;sottodominio>** in **awverify.&lt;nomesitoWeb&gt;.azurewebsites.net**, ad esempio **awverify.blogs** se il record A si riferisce a **blogs.contoso.com**.

Questo record CNAME viene usato per verificare che si è proprietari del dominio che si sta tentando di utilizzare. Questa operazione viene eseguita in aggiunta alla creazione di un record A che punta all'indirizzo IP virtuale del proprio sito Web.

Per individuare l'indirizzo IP, oltre al nome **awverify** e ai nomi **.azurewebsites.net** per il sito Web, eseguire le operazioni seguenti:

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com) dal browser.

2. Nella scheda **Siti Web** fare clic sul nome del sito, selezionare **Dashboard** e quindi scegliere **Gestisci domini** nella parte inferiore della pagina.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

 > [AZURE.NOTE] Se l'opzione **Gestisci domini** non è abilitata, il sito Web usato è gratuito. Dal momento che con un sito Web gratuito non è possibile usare nomi di dominio personalizzati è necessario eseguire l'aggiornamento alla modalità condivisa, di base o standard. Per altre informazioni sulle modalità di Siti Web e su come passare da una all'altra, vedere [Come applicare la scalabilità ai siti Web](http://www.windowsazure.com/it-it/documentation/articles/web-sites-scale/).

6. Nella finestra di dialogo **GESTISCI DOMINI PERSONALIZZATI** sarà possibile visualizzare le informazioni su **awverify**, il nome di dominio **.azurewebsites.net** attualmente assegnato e l'indirizzo IP virtuale. Salvare queste informazioni perché verranno usate durante la creazione dei record DNS.

	![](./media/custom-dns-web-site/managecustomdomains.png)


<!--HONumber=42-->
