Domain Name System (DNS) consente di individuare risorse su Internet. Se ad esempio si immette un indirizzo di app Web nel browser o si fa clic su un collegamento in una pagina Web, viene usato DNS per convertire il dominio in un indirizzo IP. L'indirizzo IP è paragonabile a un indirizzo postale, solo che non è altrettanto intuitivo. È ad esempio molto più facile ricordare un nome DNS come **contoso.com** anziché un indirizzo IP come 192.168.1.88 o 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Il sistema DNS è basato su *record*. I record associano uno specifico *nome*, come **contoso.com**, a un indirizzo IP o a un altro nome DNS. Quando un'applicazione, ad esempio un Web browser, cerca un nome in DNS, trova il record e usa come indirizzo il valore a cui punta. Se il valore a cui punta è un indirizzo IP, il browser userà questo valore. Se punta a un altro nome DNS, l'applicazione deve risolverlo di nuovo. In ultima analisi, la risoluzione del nome terminerà sempre in un indirizzo IP.

Quando di crea un'app Web in Servizio app, un nome DNS viene assegnato automaticamente all'app Web. Tale nome avrà il formato **&lt;nomeappweb&gt;.azurewebsites.net**. È anche disponibile un indirizzo IP virtuale da usare quando si creano i record DNS, quindi è possibile creare record che puntano ad **.azurewebsites.net**oppure all'indirizzo IP.

> [!NOTE]
> L'indirizzo IP dell'app Web cambierà se si elimina e si ricrea l'app Web oppure si cambia la modalità del piano di servizio app impostandola sulla modalità **Gratuita** da **Basic**, **Condivisa** o **Standard**.
> 
> 

Sono inoltre disponibili più tipi di record, ognuno con funzioni e limitazioni specifiche, ma per le app Web sono importanti solo due tipi, *A* e *CNAME*.

### <a name="address-record-a-record"></a>Record di indirizzo (record A)
Un record A consente di eseguire il mapping di un dominio, ad esempio **contoso.com** o **www.contoso.com**, *o di un dominio con caratteri jolly* ad esempio **\*.contoso.com**, a un indirizzo IP. Nel caso di un'app Web in Servizio app, si tratta dell'indirizzo IP virtuale del servizio o di uno specifico indirizzo IP acquistato per l'app Web.

I principali vantaggi di un record A rispetto a un record CNAM sono i seguenti:

* È possibile eseguire il mapping di un dominio radice, ad esempio **contoso.com**, a un indirizzo IP. Molti registrar consentono di usare solo record A in questo caso.
* È possibile usare un'unica voce con un carattere jolly, ad esempio **\*.contoso.com**, che gestirà le richieste per più sottodomini, ad esempio **mail.contoso.com**, **blogs.contoso.com** o **www.contso.com**.

> [!NOTE]
> Poiché viene eseguito il mapping di un record A a un indirizzo IP statico, il record non è in grado di risolvere automaticamente le modifiche all'indirizzo IP dell'app Web. Quando si configurano le impostazioni di un nome di dominio personalizzato per l'app Web, viene fornito un indirizzo IP da usare con i record A. Questo valore può tuttavia cambiare se si elimina e si ricrea l'app Web o qualora si riporti il piano di servizio app in modalità **Gratuita**.
> 
> 

### <a name="alias-record-cname-record"></a>Record alias (record CNAME)
Un record CNAME consente di eseguire il mapping di un nome DNS *specifico*, ad esempio **mail.contoso.com** o **www.contoso.com**, a un altro nome di dominio (canonico). Nel caso di app Web del servizio app, il nome dominio canonico è il nome di dominio **&lt;nomeappWeb>.azurewebsites.net** dell'app Web. Dopo la creazione, CNAME crea un alias per il nome di dominio **&lt;nomeappWeb>.azurewebsites.net**. La voce CNAME si risolverà automaticamente nell'indirizzo IP del nome di dominio **&lt;nomeappWeb>.azurewebsites.net**, quindi se l'indirizzo IP dell'app Web cambia, non è necessario eseguire alcuna azione.

> [!NOTE]
> Alcuni registrar consentono di eseguire il mapping solo dei sottodomini se si usa un record CNAME, ad esempio **www.contoso.com**, e non dei nomi radice come **contoso.com**. Per altre informazioni sui record CNAME, vedere la documentazione fornita dal registrar, la <a href="http://en.wikipedia.org/wiki/CNAME_record">voce di Wikipedia sui record CNAME</a> oppure il documento di IETF relativo a <a href="http://tools.ietf.org/html/rfc1035">implementazione e specifiche dei nomi di dominio</a>.
> 
> 

### <a name="web-app-dns-specifics"></a>Specifiche DNS di app Web
Per usare un record A con App Web è necessario prima creare uno dei seguenti record TXT:

* **Per il dominio radice**: un record TXT DNS di **@** in **&lt;nomeappWeb&gt;.azurewebsites.net**.
* **Per un sottodominio specifico**: un nome DNS di **&lt;sottodominio>** in **&lt;nomeappWeb&gt;.azurewebsites.net**. Ad esempio, **blogs** se il record A si riferisce a **blogs.contoso.com**.
* **Per i sottodomini con carattere jolly**: un record TXT DNS di **&lt;nomeappWeb&gt;.azurewebsites.net**.

Questo record TXT viene usato per verificare la proprietà del dominio che si sta tentando di usare. Questa operazione viene eseguita in aggiunta alla creazione di un record A che punta all'indirizzo IP virtuale dell'app Web.

È possibile trovare l'indirizzo IP, nonché i nomi **.azurewebsites.net** per l'app Web completando la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com)dal browser.
2. Nel pannello **App Web** fare clic sul nome dell'app Web e selezionare **Domini personalizzati** nella parte inferiore della pagina.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Nel pannello **Domini personalizzati** verrà visualizzato l'indirizzo IP virtuale. Salvare queste informazioni perché verranno usate durante la creazione dei record DNS
   
    ![](./media/custom-dns-web-site/virtual-ip-address.png)
   
   > [!NOTE]
   > Non è possibile usare nomi di dominio personalizzati con un'app Web in modalità **Gratuita** ed è necessario eseguire l'aggiornamento del piano di servizio app alla modalità di livello **Condiviso**, **Basic**, **Standard** o **Premium**. Per altre informazioni sui piani tariffari dei piani di servizio app e su come modificare il piano tariffario dell'app Web, vedere [Come ridimensionare un'app Web](../articles/app-service-web/web-sites-scale.md).
   > 
   > 



<!--HONumber=Nov16_HO3-->


