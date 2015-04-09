Domain Name System (DNS) consente di individuare risorse su Internet. Se ad esempio si immette un indirizzo di app Web nel browser o si fa clic su un collegamento in una pagina Web, viene usato DNS per convertire il dominio in un indirizzo IP. L'indirizzo IP è paragonabile a un indirizzo postale, solo che non è altrettanto intuitivo. Ad esempio, è molto più facile ricordare un nome DNS come **contoso.com** che un  indirizzo IP come 192.168.1.88 o 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Il sistema DNS è basato su  *records*. I record associano uno specifico  *name*, come **contoso.com** a un indirizzo IP o a un altro nome DNS. Quando un'applicazione, ad esempio un Web browser, cerca un nome in DNS, trova il record e utilizza come indirizzo il valore a cui punta. Se il valore a cui punta è un indirizzo IP, il browser utilizzerà questo valore. Se punta a un altro nome DNS, l'applicazione deve risolverlo di nuovo. In ultima analisi, la risoluzione del nome terminerà sempre in un indirizzo IP.

Quando di crea un'app Web in Servizio app, un nome DNS viene assegnato automaticamente all'app Web. Tale nome avrà il formato **&lt;nomeapp&gt;.azurewebsites.net**. È anche disponibile un indirizzo IP virtuale da usare quando si creano i record DNS, quindi è possibile creare record che puntano ad **.azurewebsites.net** oppure all'indirizzo IP.

> [AZURE.NOTE] L'indirizzo IP dell'app Web cambierà se si elimina e si ricrea l'app Web oppure si passa il piano Servizio app alla modalità **Gratuita** da **Base**, **Condivisa** o **Standard**.

Esistono anche altri tipi di record, ognuno dei quali con le proprie funzioni e limitazioni, ma per le app Web sono importanti solo due tipi *A* e *CNAME*.

###Record di indirizzo (record A)

Un record A esegue il mapping di un dominio, ad esempio **contoso.com** o **www.contoso.com**, *or a wildcard domain* ad esempio **\*.contoso.com**, a un indirizzo IP. Nel caso di un'app Web in Servizio app, si tratta dell'indirizzo IP virtuale del servizio o di uno specifico indirizzo IP acquistato per l'app Web.

I principali vantaggi di un record A rispetto a un record CNAM sono i seguenti:

* È possibile eseguire il mapping di un dominio radice come **contoso.com** a un indirizzo IP; molti registrar consentono di utilizzare solo record A in questo caso

* È possibile usare un'unica voce con un carattere jolly, ad esempio **\*.contoso.com**, che gestirà le richieste per più sottodomini, ad esempio **mail.contoso.com**, **blogs.contoso.com** o **www.contso.com**.

> [AZURE.NOTE] Poiché viene eseguito il mapping di un record A a un indirizzo IP statico, il record non è in grado di risolvere automaticamente le modifiche all'indirizzo IP dell'app Web. Quando si configurano le impostazioni di un nome di dominio personalizzato per l'app Web, viene fornito un indirizzo IP da utilizzare con i record A. Questo valore può tuttavia cambiare se si elimina e si ricrea l'app Web o qualora lo si riporti in modalità **Gratuita**.

###Record alias (record CNAME)

Un record CNAME consente di eseguire il mappung di un nome DNS  *specific*, ad esempio **mail.contoso.com** o **www.contoso.com** a un altro nome di dominio (canonico). Nel caso di app Web di Servizio app, il nome dominio canonico è il nome di dominio **&lt;nomeappWeb>.azurewebsites.net** dell'app Web. Una volta creato, CNAME crea un alias per il nome di dominio **&lt;nomeappweb>.azurewebsites.net**. La voce CNAME si risolverà automaticamente nell'indirizzo IP del nome di dominio **&lt;nomeappweb>.azurewebsites.net**, per cui se l'indirizzo IP dell'app Web cambia, non è necessario eseguire alcuna azione.

> [AZURE.NOTE] Alcuni registrar consentono di eseguire il mapping solo dei sottodomini se si usa un record CNAME, ad esempio **www.contoso.com**, e non dei nomi radice come **contoso.com**. Per altre informazioni sui record CNAME, vedere la documentazione fornita dal registrar, la <a href="http://en.wikipedia.org/wiki/CNAME_record">pagina di Wikipedia sui record CNAME</a> oppure il documento di <a href="http://tools.ietf.org/html/rfc1035">IETF relativo a implementazione e specifiche dei nomi di dominio</a>.

###Specifiche DNS di app Web

Per usare un record A con App Web è necessario prima creare uno dei seguenti record CNAME:

* **Per il dominio radice o il sottodominio con caratteri jolly** - Un nome DNS **awverify** in **awverify.&lt;nomeappweb&gt;.azurewebsites.net**.

* **Per un sottodominio specifico** - Un  nome DNS **awverify.&lt;sottodominio>** in **awverify.&lt;nomeappweb&gt;.azurewebsites.net**. Ad esempio, **awverify.blogs** se il record A si riferisce a **blogs.contoso.com**.

Questo record CNAME viene usato per verificare che si è proprietari del dominio che si sta tentando di utilizzare. Questa operazione viene eseguita in aggiunta alla creazione di un record A che punta all'indirizzo IP virtuale della propria app Web.

È possibile trovare l'indirizzo IP, nonché il nome **awverify** e i nomi **.azurewebsites.net** per l'app Web completando la procedura seguente:

1. Nel browser, aprire il [Portale di Azure](https://portal.azure.com).

2. Nel pannello **App Web**, fare clic sul nome dell'app Web, selezionare **Tutte le impostazioni**, quindi selezionare **Domini personalizzati e SSL** nella parte inferiore della pagina.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

	> [AZURE.NOTE] Se l'opzione **Gestisci domini** non è abilitata, si sta  usando un'app Web **gratuita**. Non è  possibile utilizzare nomi di dominio personalizzati con un'app Web **gratuita** ed è necessario eseguire l'aggiornamento del piano di Servizio app alla modalità **Condiviso**, **Base** o **Standard**. Per ulteriori informazioni sulle modalità del piano Servizio app e si come passare da una modalità all'altre, vedere [Come applicare la scalabilità alle app Web](../articles/web-sites-scale.md).

6. Nella finestra di dialogo **GESTISCI DOMINI PERSONALIZZATI** sarà possibile visualizzare le informazioni su **awverify**, il nome di dominio **.azurewebsites.net** attualmente assegnato e l'indirizzo IP virtuale. Salvare queste informazioni perché verranno usate durante la creazione dei record DNS.

	![](./media/custom-dns-web-site/managecustomdomains.png)


<!--HONumber=49-->