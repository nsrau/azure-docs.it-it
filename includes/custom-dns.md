
# Configurazione di un nome di dominio personalizzato per un servizio cloud di Azure

Quando si crea un'applicazione in Azure, viene fornito un sottodominio nel dominio cloudapp.net in modo che gli utenti possano accedere all'applicazione tramite un URL come http://<*myapp*>.cloudapp.net. È tuttavia possibile esporre l'applicazione in un nome di dominio personalizzato, ad esempio contoso.com.

> [WACOM.NOTE] Le procedure in questa attività si applicano ai servizi
> cloud di Azure. Per gli account di archiviazione, vedere
> [Configurazione di un nome di dominio personalizzato per un account di
> archiviazione di Azure](../storage-custom-domain-name/). Per i siti
> Web, vedere [Configurazione di un nome di dominio personalizzato per
> un sito Web di Azure](../web-sites-custom-domain-name/).

Contenuto dell'articolo:

* [Informazioni sui record CNAME e A](#access-app)
* [Aggiunta di un record CNAME per il dominio
  personalizzato](#add-cname)
* [Aggiunta di un record A per il dominio personalizzato](#add-aname)

<h2><a name="access-app"></a>Informazioni sui record CNAME e A</h2>


I record CNAME (o record Alias) e i record A consentono entrambi di associare un nome di dominio a un server specifico (o in questo caso a un servizio) tuttavia le modalità di funzionamento sono diverse. Vi sono inoltre alcune considerazioni specifiche sull'utilizzo di record A con i servizi cloud di Azure, di cui è necessario tenere conto prima di decidere il tipo di record da utilizzare.

### Record CNAME o Alias

Un record CNAME consente di eseguire il mapping di un dominio *specifico*, ad esempio **contoso.com** o **www.contoso.com**, a un nome di dominio canonico. In questo caso, il nome di dominio canonico è il nome di dominio **<myapp>.cloudapp.net** dell'applicazione ospitata in Azure. Dopo la creazione, il record CNAME crea a sua volta un alias per **<myapp>.cloudapp.net**. La voce CNAME viene risolta nell'indirizzo IP del servizio **<myapp>.cloudapp.net** in modo automatico, pertanto se l'indirizzo IP del servizio cloud cambia non sarà necessaria alcuna azione.

> [WACOM.NOTE] Alcuni registrar consentono il mapping di sottodomini
> unicamente se si utilizza un record CNAME, ad esempio www.contoso.com,
> e non nomi radice, come contoso.com. Per ulteriori informazioni sui
> record CNAME, vedere la documentazione fornita dal registrar, [la voce
> di Wikipedia "CNAME record"][1] oppure il
> documento [IETF sull'implementazione e le specifiche dei nomi di
> dominio][2].

### Record A

Un record A consente di eseguire il mapping di un dominio, ad esempio **contoso.com** o **www.contoso.com**, *o di un dominio con caratteri jolly,* ad esempio **\*.contoso.com**, a un indirizzo IP. Nel caso di un servizio cloud di Azure, si tratta dell'IP virtuale del servizio. Quindi il principale vantaggio di un record A rispetto a un record CNAME consiste nel fatto che un'unica voce con un carattere jolly, ad esempio **\*.contoso.com**, gestirà le richieste per più sottodomini, ad esempio **mail.contoso.com**, **login.contoso.com** o **www.contso.com**.

> [WACOM.NOTE] Poiché il mapping di un record A viene eseguito a un
> indirizzo IP statico, il record non è in grado di risolvere
> automaticamente le modifiche all'indirizzo IP del servizio cloud.
> L'indirizzo IP utilizzato dal servizio cloud viene allocato la prima
> volta che si effettua una distribuzione in uno slot vuoto di
> produzione o di gestione temporanea. Se si elimina la distribuzione
> per lo slot, l'indirizzo IP viene rilasciato da Azure e le
> distribuzioni future in quello slot potrebbero ricevere un nuovo
> indirizzo IP.
> 
> L'indirizzo IP di un determinato slot di distribuzione (di produzione
> o di gestione temporanea) viene reso permanente durante i passaggi tra
> distribuzioni di produzione e di gestione temporanea o durante
> l'esecuzione di aggiornamenti sul posto di distribuzioni esistenti.
> Per ulteriori informazioni sull'esecuzione di queste azioni, vedere
> [Come gestire i servizi cloud](../cloud-services-how-to-manage/).

<h2><a name="add-cname"></a>Aggiunta di un record CNAME per il dominio personalizzato</h2>


Per creare un record CNAME è necessario aggiungere una nuova voce nella tabella DNS del dominio personalizzato utilizzando gli strumenti forniti dal registrar. Sebbene i registrar utilizzino metodi simili per specificare un record CNAME, vi sono alcune differenze nel modo in cui ognuno consente di effettuare questa operazione. Il concetto di base è tuttavia identico per tutti.

1.  Utilizzare uno dei metodi seguenti per trovare il nome di dominio **.cloudapp.net** assegnato al servizio cloud in questione.

* Accedere al [portale di gestione di Azure][3], selezionare il servizio cloud, scegliere **Dashboard**, quindi individuare la voce **Site URL** nella sezione **quick glance**.

 ![Sezione quick glance in cui è visualizzato l'URL del sito](./media/custom-dns/csurl.png)

* Installare e configurare [Azure Powershell](../install-configure-powershell/), quindi eseguire il comando seguente:
  
  Get-AzureDeployment -ServiceName nome\_servizio \| Select Url

Salvare il nome di dominio utilizzato nell'URL restituito da uno dei metodi, poiché sarà necessario per la creazione di un record CNAME.

1.  Accedere al sito Web del registrar DNS e passare alla pagina di gestione dei DNS. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**.

2.  Trovare la sezione in cui è possibile selezionare o immettere i record CNAME. Può essere necessario selezionare un tipo di record in un elenco a discesa oppure passare a una pagina di impostazioni avanzate. Individuare i termini **CNAME**, **Alias** o **Subdomains**.

3.  Se si desidera creare un alias per **www.customdomain.com** è necessario fornire anche l'alias di dominio o sottodominio per il record CNAME, ad esempio **www**. Se si desidera creare un alias per il dominio radice, è possibile che sia elencato con il simbolo '**@**' negli strumenti DNS del registrar.

4.  A questo punto, occorre fornire un nome host canonico, che in questo caso corrisponde al dominio **cloudapp.net**.

Il record CNAME seguente, ad esempio, inoltra tutto il traffico da **www.contoso.com** a **contoso.cloudapp.net**, che è il nome di dominio personalizzato dell'applicazione distribuita:

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nome host/Sottodominio</strong>
</td>

<td><strong>Dominio canonico</strong>
</td>

</tr>

<tr>
<td>www</td>

<td>contoso.cloudapp.net</td>

</tr>

</table>

A un visitatore di **www.contoso.com** non verrà mai visualizzato il nome dell'host reale (contoso.cloudapp.net), pertanto il processo di inoltro risulta totalmente invisibile all'utente finale.

> [WACOM.NOTE] L'esempio sopra riportato si applica solo al
> sottodominio **www**. Poiché non è possibile
> utilizzare caratteri jolly con i record CNAME, è necessario crearne
> uno per ogni dominio/sottodominio. Se si desidera indirizzare traffico
> da sottodomini, ad esempio \*.contoso.com, all'indirizzo cloudapp.net
> in uso, è possibile configurare una voce di **reindirizzamento URL**{:
> } o **inoltro URL** nelle
> impostazioni DNS oppure creare un record A.

<h2><a name="add-aname"></a>Aggiunta di un record A per il dominio personalizzato</h2>


Per creare un record A, è necessario innanzitutto trovare l'indirizzo IP virtuale del servizio cloud. Si aggiunge quindi una voce nella tabella DNS del dominio personalizzato utilizzando gli strumenti forniti dal registrar. Sebbene i registrar utilizzino metodi simili per specificare un record A, vi sono alcune differenze nel modo in cui ognuno consente di effettuare questa operazione. Il concetto di base è tuttavia identico per tutti.

1.  Utilizzare uno dei metodi seguenti per ottenere l'indirizzo IP del servizio cloud.

	* Accedere al [portale di gestione di Azure][3], selezionare il servizio cloud, scegliere **Dashboard**, quindi individuare la voce **Public Virtual IP (VIP) address** nella sezione **quick glance**.

	![Sezione quick glance in cui è visualizzato l'indirizzo VIP](./media/custom-dns/csvip.png)

	* Installare e configurare [Azure Powershell](../install-configure-powershell/), quindi eseguire il comando seguente:
  
  get-azurevm -servicename nome\_servizio \| get-azureendpoint -VM
  \{$\_.VM} \| select Vip
  
  Nel caso in cui vi siano più endpoint associati al servizio cloud, si riceveranno più righe contenenti l'indirizzo IP, tuttavia tali righe dovrebbero visualizzare tutte lo stesso indirizzo.

Salvare l'indirizzo IP, poiché sarà necessario per la creazione di un record A.

1.  Accedere al sito Web del registrar DNS e passare alla pagina di gestione dei DNS. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**.

2.  Trovare la sezione in cui è possibile selezionare o immettere i record A. Può essere necessario selezionare un tipo di record in un elenco a discesa oppure passare a una pagina di impostazioni avanzate.

3.  Selezionare o immettere il dominio o sottodominio che utilizzerà il record A. Selezionare ad esempio **www** se si desidera creare un alias per **www.customdomain.com**. Se si desidera creare una voce con caratteri jolly per tutti i sottodomini, immettere '**\***'. In questo modo verranno inclusi tutti i sottodomini, ad esempio **mail.customdomain.com**, **login.customdomain.com** e **www.customdomain.com**.

Se si desidera creare un record A per il dominio radice, è possibile che sia elencato con il simbolo '**@**' negli strumenti DNS del registrar.

1.  Immettere l'indirizzo IP del servizio cloud nell'apposito campo. La voce del dominio utilizzata nel record A verrà associata all'indirizzo IP della distribuzione del servizio cloud.

Il record A seguente, ad esempio, inoltra tutto il traffico da **contoso.com** a **137.135.70.239**, che è l'indirizzo IP dell'applicazione distribuita:

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nome host/Sottodominio</strong>
</td>

<td><strong>Indirizzo IP</strong>
</td>

</tr>

<tr>
<td>@</td>

<td>137.135.70.239</td>

</tr>

</table>

In questo esempio viene illustrata la creazione di un record A per il dominio radice. Se si desidera creare una voce con caratteri jolly per tutti i sottodomini, immettere '**\***' come sottodominio.

## Passaggi successivi

* [Come gestire i servizi cloud](../cloud-services-how-to-manage/)
* [Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato][4]



[1]: http://en.wikipedia.org/wiki/CNAME_record
[2]: http://tools.ietf.org/html/rfc1035
[3]: https://manage.windowsazure.com
[4]: http://msdn.microsoft.com/it-it/library/windowsazure/gg680307.aspx