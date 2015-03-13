# Configurazione di un nome di dominio personalizzato per un servizio cloud di Azure

> [AZURE.NOTE]
> Grazie al nuovo [percorso guidato](http://support.microsoft.com/kb/2990804) di Azure,  è facilissimo associare un nome di dominio personalizzato E proteggere le comunicazioni (SSL) con i Servizi cloud di Azure o Siti Web di Azure.

Quando si crea un'applicazione, Azure fornisce un sottodominio nel dominio cloudapp.net in modo che gli utenti possano accedere all'applicazione tramite un URL come http://&lt;*myapp*.cloudapp.net. È tuttavia possibile esporre l'applicazione in un nome di dominio personalizzato, ad esempio contoso.com.

> [AZURE.NOTE] 
> Le procedure in questa attività si applicano ai servizi cloud di Azure. Per gli account di archiviazione, vedere [Configurazione di un nome di dominio personalizzato per un account di archiviazione di Azure](../storage-custom-domain-name/). Per i siti Web, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](../web-sites-custom-domain-name/).

Contenuto dell'articolo:

-   [Informazioni sui record CNAME e A](#access-app)
-   [Aggiungere un record CNAME per il dominio personalizzato](#add-cname)
-   [Aggiungere un record A per il dominio personalizzato](#add-aname)

<h2><a name="access-app"></a>Informazioni sui record CNAME e A</h2>

I record CNAME (o record Alias) e i record A consentono entrambi di associare un nome di dominio a un server specifico (o in questo caso a un servizio) tuttavia le modalità di funzionamento sono diverse. Vi sono inoltre alcune considerazioni specifiche sull'utilizzo di record A con i servizi cloud di Azure, di cui è necessario tenere conto prima di decidere il tipo di record da usare.

###Record CNAME o Alias

Un record CNAME consente di eseguire il mapping di un dominio  *specific*, ad esempio **contoso.com** o **www.contoso.com**, a un nome di dominio canonico. In questo caso, il nome di dominio canonico è il nome di dominio **&lt;myapp>.cloudapp.net** dell'applicazione ospitata in Azure. Dopo la creazione, il record CNAME crea a sua volta un alias per **&lt;myapp>.cloudapp.net**. La voce CNAME viene risolta nell'indirizzo IP del servizio **&lt;myapp>.cloudapp.net** in modo automatico, pertanto se l'indirizzo IP del servizio cloud cambia non sarà necessaria alcuna azione.

> [AZURE.NOTE] 
> Alcuni registrar consentono di eseguire il mapping solo dei sottodomini se si usa un record CNAME, ad esempio www.contoso.com, e non dei nomi radice come contoso.com. Per altre informazioni sui record CNAME, vedere la documentazione fornita dal registrar, <a href="http://en.wikipedia.org/wiki/CNAME_record">la voce di Wikipedia sui record CNAME</a>o il documento <a href="http://tools.ietf.org/html/rfc1035">su implementazione e specifiche dei nomi di dominio IETF</a> .

###Record A

Un record A consente di eseguire il mapping di un dominio, ad esempio **contoso.com** o **www.contoso.com**, *or a wildcard domain* ad esempio **\*.contoso.com**, a un indirizzo IP. Nel caso di un servizio cloud di Azure, si tratta dell'IP virtuale del servizio. Quindi il principale vantaggio di un record A rispetto a un record CNAME consiste nel fatto che un'unica voce con un carattere jolly, ad esempio ***.contoso.com**, gestirà le richieste per più sottodomini, ad esempio **mail.contoso.com**, **login.contoso.com** o **www.contoso.com**.

> [AZURE.NOTE]
> Poiché il mapping di un record A viene eseguito a un indirizzo IP statico, il record non è in grado di risolvere automaticamente le modifiche all'indirizzo IP del servizio cloud. L'indirizzo IP usato dal servizio cloud viene allocato la prima volta che si effettua una distribuzione in uno slot vuoto di produzione o di gestione temporanea. Se si elimina la distribuzione per lo slot, l'indirizzo IP viene rilasciato da Azure e le distribuzioni future in quello slot potrebbero ricevere un nuovo indirizzo IP.
> 
> L'indirizzo IP di un determinato slot di distribuzione (di produzione o di gestione temporanea) viene reso permanente durante i passaggi tra distribuzioni di produzione e di gestione temporanea o durante l'esecuzione di aggiornamenti sul posto di distribuzioni esistenti. Per altre informazioni sull'esecuzione di queste azioni, vedere [Come gestire i servizi cloud](../cloud-services-how-to-manage/).


<h2><a name="add-cname"></a>Aggiungere un record CNAME per il dominio personalizzato</h2>

Per creare un record CNAME è necessario aggiungere una nuova voce nella tabella DNS del dominio personalizzato usando gli strumenti forniti dal registrar. Sebbene i registrar usino metodi simili per specificare un record CNAME, vi sono alcune differenze nel modo in cui ognuno consente di effettuare questa operazione. Il concetto di base è tuttavia identico per tutti.

1. Usare uno dei metodi seguenti per trovare il nome di dominio **.cloudapp.net** assegnato al servizio cloud in questione.

  * Accedere al [portale di gestione di Azure], selezionare il servizio cloud, scegliere **Dashboard**, quindi individuare la voce **Site URL** nella sezione **Riepilogo rapido**.

  		  ![quick glance section showing the site URL][csurl]

  * Installare e configurare [Azure PowerShell](../install-configure-powershell/), quindi eseguire il comando seguente:

    Get-AzureDeployment -ServiceName yourservicename | Select Url

  Salvare il nome di dominio usato nell'URL restituito da uno dei metodi, poiché sarà necessario per la creazione di un record CNAME.

1.  Accedere al sito Web del registrar DNS e passare alla pagina di gestione dei DNS. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**.

2.  Trovare la sezione in cui è possibile selezionare o immettere CNAME. Può essere necessario selezionare un tipo di record in un elenco a discesa oppure passare a una pagina di impostazioni avanzate. Cercare le parole **CNAME**, **Alias** o **Subdomains**.

3.  Se si desidera creare un alias per **www.customdomain.com** è necessario fornire anche l'alias di dominio o sottodominio per il record CNAME, ad esempio **www**. Se si desidera creare un alias per il dominio radice, è possibile che sia elencato con il simbolo '**@**' negli strumenti DNS del registrar.

4. A questo punto, occorre fornire un nome host canonico, che in questo caso corrisponde al dominio **cloudapp.net**.

Il record CNAME seguente, ad esempio, inoltra tutto il traffico da **www.contoso.com** a **contoso.cloudapp.net**, che è il nome di dominio personalizzato dell'applicazione distribuita:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nome host/Sottodominio</strong></td>
<td><strong>Dominio canonico</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.cloudapp.net</td>
</tr>
</table>

A un visitatore di **www.contoso.com** non verrà mai visualizzato il nome dell'host reale
(contoso.cloudapp.net), pertanto il processo di inoltro risulta totalmente invisibile
all'utente finale.

> [AZURE.NOTE]
> L'esempio precedente si applica solo al sottodominio <strong>www</strong> . Poiché non è possibile usare caratteri jolly con i record CNAME, è necessario crearne uno per ogni dominio/sottodominio. Se si vuole indirizzare  traffico da sottodomini, ad esempio .contoso.com, all'indirizzo cloudapp.net in uso, è possibile configurare una <strong>voce di reindirizzamento URL</strong> oppure <strong>di inoltro URL</strong> nelle impostazioni DNS o creare un record A.


<h2><a name="add-aname"></a>Aggiungere un record A per il dominio personalizzato</h2>

Per creare un record A, è necessario innanzitutto trovare l'indirizzo IP virtuale del servizio cloud. Si aggiunge quindi una voce nella tabella DNS del dominio personalizzato usando gli strumenti forniti dal registrar. Sebbene i registrar usino metodi simili per specificare un record A, vi sono alcune differenze nel modo in cui ognuno consente di effettuare questa operazione. Il concetto di base è tuttavia identico per tutti.

1. Usare uno dei metodi seguenti per ottenere l'indirizzo IP del servizio cloud.

  * Accedere al [portale di gestione di Azure], selezionare il servizio cloud, scegliere **Dashboard**, quindi individuare la voce **Indirizzo IP virtuale pubblico (VIP)** nella sezione **riepilogo rapido**.

   		 ![quick glance section showing the VIP][vip]

  * Installare e configurare [Azure PowerShell](../install-configure-powershell/), quindi eseguire il comando seguente:

      get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip

    Nel caso in cui vi siano più endpoint associati al servizio cloud, si riceveranno più righe contenenti l'indirizzo IP, tuttavia tali righe dovrebbero visualizzare tutte lo stesso indirizzo.

  Salvare l'indirizzo IP, poiché sarà necessario per la creazione di un record A.

1.  Accedere al sito Web del registrar DNS e passare alla pagina di gestione dei DNS. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**.

2.  Trovare la sezione in cui è possibile selezionare o immettere i record A. Può essere necessario selezionare un tipo di record in un elenco a discesa oppure passare a una pagina di impostazioni avanzate.

3. Selezionare o immettere il dominio o sottodominio che utilizzerà il record A. Selezionare ad esempio **www**se si desidera creare un alias per **www.customdomain.com**. Se si desidera creare una voce con caratteri jolly per tutti i sottodomini, immettere '__*__'. In questo modo verranno inclusi tutti i sottodomini, ad esempio **mail.customdomain.com**, **login.customdomain.com** e **www.customdomain.com**.

  Se si vuole creare un record A per il dominio radice, è possibile che sia elencato con il simbolo '**@**' negli strumenti DNS del registrar.

4. Immettere l'indirizzo IP del servizio cloud nell'apposito campo. La voce del dominio usata nel record A verrà associata all'indirizzo IP della distribuzione del servizio cloud.

Il record A seguente, ad esempio, inoltra tutto il traffico da **contoso.com** a **137.135.70.239**, che è l'indirizzo IP dell'applicazione distribuita:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nome host/Sottodominio</strong></td>
<td><strong>Indirizzo IP</strong></td>
</tr>
<tr>
<td>@</td>
<td>137.135.70.239</td>
</tr>
</table>

Questo esempio illustra la creazione di un record A per il dominio radice. Se si desidera creare una voce con caratteri jolly per tutti i sottodomini, immettere '__*__' come sottodominio.

## Passaggi successivi

-   [Come gestire i servizi cloud](../cloud-services-how-to-manage/)
-   [Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato][]

  [Esporre l'applicazione in un dominio personalizzato]: #access-app
  [Aggiungere un record CNAME per il dominio personalizzato]: #add-cname
  [Esporre i dati in un dominio personalizzato]: #access-data
  [Scambi indirizzi VIP]: http://msdn.microsoft.com/library/ee517253.aspx
  [Creare un record CNAME che associa il sottodominio all'account di archiviazione]: #create-cname
  [Portale di gestione di Azure]: https://manage.windowsazure.com
  [Finestra di dialogo Convalida dominio personalizzato]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
  [Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx
  [vip]: ./media/custom-dns/csvip.png
  [csurl]: ./media/custom-dns/csurl.png
<!--HONumber=45--> 
