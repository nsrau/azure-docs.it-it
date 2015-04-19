#Configurazione di un nome di dominio personalizzato per un sito Web di Azure

Quando si crea un sito Web, Azure fornisce un sottodominio descrittivo nel dominio azurewebsites.net, in modo che gli utenti possano accedere al sito Web usando un URL come http://&lt;mysite>.azurewebsites.net. Se tuttavia si configurano i siti Web per la modalità condivisa o standard, è possibile eseguire il mapping del sito Web al proprio nome di dominio personalizzato.

Facoltativamente, è possibile usare Gestione traffico di Azure per bilanciare il carico del traffico in ingresso nel sito Web. Per altre informazioni sul funzionamento di Gestione traffico con i siti Web, vedere [Controllo del traffico dei siti Web di Azure con Gestione traffico di Azure][trafficmanager].

> [WACOM.NOTE] Le procedure descritte in questa attività si applicano a Siti Web di Azure. Per Servizi cloud, vedere <a href="/it-it/develop/net/common-tasks/custom-dns/">Configurazione di un nome di dominio personalizzato in Azure</a>.

> [WACOM.NOTE] .I passaggi illustrati in questa attività prevedono la configurazione dei siti Web in modalità condivisa o standard, il che potrebbe modificare la modalità di addebito della sottoscrizione. Per altre informazioni, vedere <a href="/it-it/pricing/details/web-sites/">Dettagli dei prezzi di Siti Web</a>.

Contenuto dell'articolo:

-   [Informazioni sui record CNAME e A](#understanding-records)
-   [Configurare i siti Web per la modalità condivisa o standard](#bkmk_configsharedmode)
-   [Aggiungere siti Web a Gestione traffico](#trafficmanager)
-   [Aggiungere un record CNAME per il dominio personalizzato](#bkmk_configurecname)
-   [Aggiungere un record A per il dominio personalizzato](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Informazioni sui record CNAME e A</h2>

I record CNAME (o record Alias) e i record A consentono entrambi di associare un nome di dominio a un sito Web, tuttavia, le modalità di funzionamento sono diverse.

###Record CNAME o Alias

 Un record CNAME consente di eseguire il mapping di un dominio *specifico*, ad esempio **contoso.com** o **www.contoso.com**, a un nome di dominio canonico. In questo caso il nome di dominio canonico è **&lt;myapp>.azurewebsites.net** nel sito Web di Azure o **&lt;myapp>.trafficmgr.com** del profilo di Gestione traffico. Dopo la creazione, il record CNAME crea un alias per il nome di dominio **&lt;myapp>.azurewebsites.net** o **&lt;myapp>.trafficmgr.com**. La voce CNAME viene risolta automaticamente nell'indirizzo IP del nome di dominio **&lt;myapp>.azurewebsites.net** o **&lt;myapp>.trafficmgr.com**, quindi se l'indirizzo IP del sito Web cambia non sarà necessaria alcuna azione.

> [WACOM.NOTE] ,Alcuni registrar consentono il mapping di sottodomini unicamente se si usa un record CNAME, ad esempio www.contoso.com, e non nomi radice, come contoso.com. Per altre informazioni sui record CNAME, vedere la documentazione fornita dal registrar <a href="http://en.wikipedia.org/wiki/CNAME_record">la voce di Wikipedia "CNAME record"</a> oppure il documento <a href="http://tools.ietf.org/html/rfc1035">IETF sull'implementazione e le specifiche dei nomi di dominio</a>.

###Record A

Un record A consente di eseguire il mapping di un dominio, ad esempio **contoso.com** o **www.contoso.com** *o di un dominio con caratteri jolly*, ad esempio **\*.contoso.com**, a un indirizzo IP. Nel caso di un sito Web di Azure, si tratta dell'indirizzo IP virtuale del servizio o di uno specifico indirizzo IP acquistato per il sito. Quindi il principale vantaggio di un record A rispetto a un record CNAME consiste nel fatto che un'unica voce con un carattere jolly, ad esempio ***.contoso.com**, gestirà le richieste per più sottodomini, ad esempio **mail.contoso.com**, **login.contoso.com** o **www.contso.com**.

> [WACOM.NOTE] Poiché il mapping di un record A viene eseguito a un indirizzo IP statico, non sarà possibile risolvere automaticamente le modifiche apportate all'indirizzo IP del sito Web. Quando si configurano le impostazioni di un nome di dominio personalizzato per il sito Web, viene fornito un indirizzo IP da usare con i record A. Questo valore può però cambiare se si elimina e si ricrea il sito Web o nel caso in cui lo si riporti in modalità gratuita.

> [WACOM.NOTE] .[WACOM.NOTE] Non è possibile usare i record A per il bilanciamento del carico con Gestione traffico. Per altre informazioni, vedere [Controllo del traffico dei siti Web di Azure con Gestione traffico di Azure][trafficmanager].
 
<a name="bkmk_configsharedmode"></a><h2>Configurare i siti Web per la modalità condivisa o standard</h2>

L'impostazione di un nome di dominio personalizzato in un sito Web è disponibile solo per le modalità condivisa e standard per Siti Web di Azure. Prima di impostare un sito Web dalla modalità gratuita alla modalità condivisa o standard, è necessario rimuovere i limiti di spesa applicati alla sottoscrizione per Siti Web. Per altre informazioni sui prezzi per le modalità condivisa e standard, vedere [Dettagli prezzi][PricingDetails].

1. Nel browser, aprire il [portale di gestione][].
2. Nella scheda **Siti Web** fare clic sul nome del sito Web.

	![][standardmode1]

3. Fare clic sulla scheda **SCALA**.

	![][standardmode2]

	
4. Nella sezione **generale** impostare la modalità del sito Web facendo clic su **CONDIVISO**.

	![][standardmode3]

	> [WACOM.NOTE] Se si intende usare Gestione traffico con questo sito Web, è necessario selezionare la modalità standard anziché quella condivisa.

5. Fare clic su **Salva**.
6.  Quando viene proposto l'aumento di costo per la modalità condivisa, o standard se si sceglie quella standard, fare clic su **Sì** per confermare.

	<!--![][standardmode4]-->

	**Nota**<br /> 
	Se viene visualizzato un messaggio di errore di tipo "Configurazione della scalabilità per il sito Web 'nome sito Web' non riuscita", è possibile usare il pulsante Dettagli per ottenere altre informazioni. 

<a name="trafficmanager"></a><h2>(Facoltativo) Aggiungere i siti Web a Gestione traffico</h2>

Se si desidera usare il sito Web con Gestione traffico, attenersi alla procedura seguente.

1. Se non si dispone già di un profilo di Gestione traffico, usare le informazioni riportate in [Creare un profilo di Gestione traffico mediante Creazione rapida][createprofile] per crearne uno. Prendere nota del nome di dominio **.trafficmgr.com** associato al proprio profilo di Gestione traffico. Questo nome verrà usato in un passaggio successivo.

2. Usare le informazioni nella sezione [Aggiungere o eliminare endpoint][addendpoint] per aggiungere il sito Web come endpoint nel profilo di Gestione traffico.

	> [WACOM.NOTE] Se il sito Web non è elencato quando si aggiunge un endpoint, verificare che sia configurato per la modalità standard. Per usare Gestione traffico, è necessario che il sito Web sia impostato sulla modalità standard.

3. Accedere al sito Web del registrar DNS e passare alla pagina di gestione dei DNS. Cercare collegamenti o aree del sito denominate **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

4. Trovare la sezione in cui è possibile selezionare o immettere i record CNAME. Può essere necessario selezionare un tipo di record in un elenco a discesa oppure passare a una pagina di impostazioni avanzate. Cercare le parole **CNAME**, **Alias** o **Subdomains**.

5. .È necessario fornire anche l'alias di dominio o sottodominio per il record CNAME, ad esempio **www** se si vuole creare un alias per **www.customdomain.com**.

5. È necessario fornire anche un nome host corrispondente al nome di dominio canonico per l'alias CNAME. Si tratta del nome **.trafficmgr.com** del sito Web.

Il record CNAME seguente, ad esempio, inoltre tutto il traffico da **www.contoso.com** a **contoso.trafficmgr.com**, che è il nome di dominio di un sito Web:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nome host/Sottodominio</strong></td>
<td><strong>Dominio canonico</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

A un visitatore di **www.contoso.com** non verrà mai visualizzato il nome dell'host reale
(contoso.azurewebsite.net), pertanto il processo di inoltro risulta totalmente invisibile all'utente finale.

> [WACOM.NOTE]Se si usa Gestione traffico con un sito Web, non è necessario eseguire i passaggi delle sezioni seguenti, ossia '**Aggiunta di un record CNAME per il dominio personalizzato**' e '**Aggiunta di un record A per il dominio personalizzato**'. Il record CNAME creato nei passaggi precedenti indirizzerà il traffico a Gestione traffico, che a sua volta lo indirizzerà agli endpoint del sito Web.

<a name="bkmk_configurecname"></a><h2>Aggiungere un record CNAME per il dominio personalizzato</h2>

Per creare un record CNAME è necessario aggiungere una nuova voce nella tabella DNS del dominio personalizzato usando gli strumenti forniti dal registrar. Sebbene i registrar usino metodi simili per specificare un record CNAME, vi sono alcune differenze nel modo in cui ognuno consente di effettuare questa operazione. Il concetto di base è tuttavia identico per tutti.

1. Usare uno dei metodi seguenti per trovare il nome di dominio **.azurewebsite.net** assegnato al sito Web in questione.

	* Accedere al [portale di gestione di Azure][portale], selezionare il sito Web, scegliere **Dashboard**, quindi individuare la voce **URL sito** nella sezione**Riepilogo rapido**.

	* Installare e configurare [Azure Powershell](/it-it/manage/install-and-configure-windows-powershell/), quindi eseguire il comando seguente:

			get-azurewebsite yoursitename | select hostnames

	* Installare e configurare l'[interfaccia della riga di comando multipiattaforma di Azure](/it-it/manage/install-and-configure-cli/), quindi eseguire il comando seguente:

			azure site domain list yoursitename

	Salvare questo nome **.azurewebsite.net** in quanto verrà usato nei passaggi seguenti.

3. Accedere al sito Web del registrar DNS e passare alla pagina di gestione dei DNS. Cercare collegamenti o aree del sito denominate **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

4. Trovare la sezione in cui è possibile selezionare o immettere i record CNAME. Può essere necessario selezionare un tipo di record in un elenco a discesa oppure passare a una pagina di impostazioni avanzate. Cercare le parole **CNAME**, **Alias** o **Subdomains**.

5. .È necessario fornire anche l'alias di dominio o sottodominio per il record CNAME, ad esempio **www** se si vuole creare un alias per **www.customdomain.com**. Se si desidera creare un alias per il dominio radice, è possibile che sia elencato con il simbolo '**@**' negli strumenti DNS del registrar.

5. È necessario fornire anche un nome host corrispondente al nome di dominio canonico per l'alias CNAME. Questo sarà il nome **.azurewebsite.net** per il sito Web.

Il record CNAME seguente, ad esempio, inoltra tutto il traffico da **www.contoso.com** a **contoso.azurewebsite.net**, che è il nome di dominio di un sito Web:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nome host/Sottodominio</strong></td>
<td><strong>Dominio canonico</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

A un visitatore di **www.contoso.com** non verrà mai visualizzato il nome dell'host reale
(contoso.azurewebsite.net), pertanto il processo di inoltro risulta totalmente invisibile
all'utente finale.

> [WACOM.NOTE] L'esempio precedente si applica solo al sottodominio __www__. Poiché non è possibile usare caratteri jolly con i record CNAME, è necessario crearne uno per ogni dominio/sottodominio. Se si vuole indirizzare traffico da sottodomini, ad esempio *.contoso.com, all'indirizzo azurewebsite.net, è possibile configurare una voce di __reindirizzamento URL__ o __Inoltro URL__ nelle impostazioni DNS oppure creare un record A.

> [WACOM.NOTE] La propagazione del record CNAME in tutto il sistema DNS può richiedere tempo. Per impostare il record CNAME per il sito Web è necessario attendere il completamento della propagazione del record CNAME. È possibile usare un servizio come <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> per verificare se il record CNAME è disponibile.

###Aggiungere il nome di dominio al sito Web

Dopo il completamento della propagazione del record CNAME per il nome di dominio, è necessario associarlo al sito Web desiderato. È possibile aggiungere il nome di dominio personalizzato definito dal record CNAME al sito Web usando l'interfaccia della riga di comando multipiattaforma di Azure oppure il portale di gestione di Azure.

**Per aggiungere un nome di dominio con gli strumenti da riga di comando**

Installare e configurare l'[interfaccia della riga di comando multipiattaforma di Azure](/it-it/manage/install-and-configure-cli/), quindi eseguire il comando seguente:

	azure site domain add customdomain yoursitename

Il comando seguente, ad esempio, aggiungerà un nome di dominio personalizzato di **www.contoso.com** al sito Web **contoso.azurewebsite.net**:

	azure site domain add www.contoso.com contoso

Per verificare che il nome di dominio personalizzato sia stato aggiunto al sito Web, eseguire il comando seguente:

	azure site domain list yoursitename

L'elenco restituito da questo comando dovrebbe contenere il nome di dominio personalizzato, oltre alla voce **.azurewebsite.net** predefinita.

**Per aggiungere un nome di dominio tramite il portale di gestione di Azure**

1. Nel browser aprire il [portale di gestione di Azure][].

2. Nella scheda **Siti Web** fare clic sul nome del sito, selezionare **Dashboard** e quindi scegliere **Gestisci domini** nella parte inferiore della pagina.

	![][setcname2]

6. Nella casella di testo **DOMAIN NAMES** immettere il nome del dominio configurato. 

	![][setcname3]

6. Fare clic sul segno di spunta per accettare il nome di dominio.

Al termine della configurazione, il nome di dominio personalizzato sarà elencato nella sezione **nomi di dominio** della pagina **Configura** del sito Web. 

<a name="bkmk_configurearecord"></a><h2>Aggiungere un record A per il dominio personalizzato</h2>

.Per creare un record A, è necessario innanzitutto trovare l'indirizzo IP del sito Web. Aggiungere quindi una voce nella tabella DNS del dominio personalizzato usando gli strumenti forniti dal registrar. Sebbene i registrar usino metodi simili per specificare un record A, vi sono alcune differenze nel modo in cui ognuno consente di effettuare questa operazione. Il concetto di base è tuttavia identico per tutti. Oltre alla creazione di un record A, è necessario procedere alla creazione di un record CNAME che verrà usato da Azure per verificare il record A.

1. Nel browser aprire il [portale di gestione di Azure][].

2. Nella scheda **Siti Web** fare clic sul nome del sito, selezionare **Dashboard**, **Gestisci domini** nella parte inferiore della schermata.

	![][setcname2]

5. Nella finestra di dialogo **Manage custom domains** individuare **l'indirizzo IP da usare quando si configurano record A**. Copiare l'indirizzo IP, che verrà usato durante la creazione del record A.

5. Nella finestra di dialogo **Manage custom domains** prendere nota del nome di dominio awverify alla fine del testo presente nella parte superiore della finestra di dialogo. Il nome dovrebbe essere **awverify.mysite.azurewebsites.net** dove **mysite** è il nome del sito Web in questione. Copiare questo nome, poiché è il nome che verrà usato per la creazione del record CNAME di verifica.

6. Accedere al sito Web del registrar DNS e passare alla pagina di gestione dei DNS. Cercare collegamenti o aree del sito denominate **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

6. Trovare la sezione in cui è possibile selezionare o immettere i record A e CNAME. Può essere necessario selezionare un tipo di record in un elenco a discesa oppure passare a una pagina di impostazioni avanzate.

7. Procedere come descritto di seguito per creare il record A:

	1. Selezionare o immettere il dominio o sottodominio che userà il record A. Selezionare ad esempio **www** se si desidera creare un alias per **www.customdomain.com**. Se si desidera creare una voce con caratteri jolly per tutti i sottodomini, immettere '__*__'. In questo modo verranno inclusi tutti i sottodomini, ad esempio **mail.customdomain.com**, **login.customdomain.com** e **www.customdomain.com**.

		Se si vuole creare un record A per il dominio radice, è possibile che sia elencato con il simbolo '**@**' negli strumenti DNS del registrar.

	2. Immettere l'indirizzo IP del servizio cloud nell'apposito campo. La voce del dominio usata nel record A verrà associata all'indirizzo IP della distribuzione del servizio cloud.

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

		Questo esempio illustra la creazione di un record A per il dominio radice. Per creare una voce con caratteri jolly per tutti i sottodomini, immettere '__*__' come sottodominio.

7. Creare quindi un record CNAME con un alias di **awverify** e un dominio canonico di **awverify.mysite.azurewebsites.net** ottenuto in precedenza.

	> [WACOM.NOTE] Sebbene un alias di awverify possa funzionare per alcuni registrar, altri potrebbero richiedere il nome di dominio alias completo di awverify.www.customdomainname.com o awverify.customdomainname.com.

	Il comando seguente, ad esempio, crea un record CNAME che potrà essere usato da Azure per verificare la configurazione del record A.

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tr>
	<td><strong>Alias/Nome host/Sottodominio</strong></td>
	<td><strong>Dominio canonico</strong></td>
	</tr>
	<tr>
	<td>awverify</td>
	<td>awverify.contoso.azurewebsites.net</td>
	</tr>
	</table>

> [WACOM.NOTE] La propagazione del record CNAME awverify in tutto il sistema DNS può richiedere tempo. Per impostare il nome di dominio personalizzato definito dal record A per il sito Web è necessario attendere il completamento della propagazione del record CNAME awverify. È possibile usare un servizio come <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> per verificare se il record CNAME è disponibile.

###Aggiungere il nome di dominio al sito Web

Dopo il completamento della propagazione del record CNAME **awverify**, è possibile associare il dominio personalizzato definito dal record A al sito Web. È possibile aggiungere il nome di dominio personalizzato definito dal record A al sito Web usando l'interfaccia della riga di comando multipiattaforma di Azure oppure il portale di gestione di Azure.

**Per aggiungere un nome di dominio con gli strumenti da riga di comando**

Installare e configurare l'[interfaccia della riga di comando multipiattaforma di Azure](/it-it/manage/install-and-configure-cli/), quindi eseguire il comando seguente:

	azure site domain add customdomain yoursitename

Il comando seguente, ad esempio, aggiungerà un nome di dominio personalizzato di **contoso.com** al sito Web **contoso.azurewebsite.net**:

	azure site domain add contoso.com contoso

Per verificare che il nome di dominio personalizzato sia stato aggiunto al sito Web, eseguire il comando seguente:

	azure site domain list yoursitename

L'elenco restituito da questo comando dovrebbe contenere il nome di dominio personalizzato, oltre alla voce **.azurewebsite.net** predefinita.

**Per aggiungere un nome di dominio tramite il portale di gestione di Azure**

1. Nel browser aprire il [portale di gestione di Azure][].

2. Nella scheda **Siti Web** fare clic sul nome del sito, selezionare **Dashboard** e quindi scegliere **Gestisci domini** nella parte inferiore della pagina.

	![][setcname2]

6. Nella casella di testo **DOMAIN NAMES** immettere il nome del dominio configurato. 

	![][setcname3]

6. Fare clic sul segno di spunta per accettare il nome di dominio.

Al termine della configurazione, il nome di dominio personalizzato sarà elencato nella sezione **nomi di dominio** della pagina **Configura** del sito Web.

> [WACOM.NOTE] Dopo aver aggiunto il nome di dominio personalizzato definito dal record A record al sito Web, è possibile rimuovere il record CNAME awverify usando gli strumenti forniti dal registrar. Se tuttavia si desiderasse aggiungere altri record A in futuro, sarà necessario ricreare il record awverify prima di poter associare il nuovo nome di dominio definito dal nuovo record A al sito Web.

## Passaggi successivi

-   [Come gestire i siti Web](/it-it/manage/services/web-sites/how-to-manage-websites/)

-   [Configurare un certificato SSL per i siti Web](/it-it/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configurazione dei siti Web per la modalità condivisa]: #bkmk_configsharedmode
[Configurare il record di verifica CNAME nel registrar di dominio]: #bkmk_configurecname
[Configurare un record CNAME di verifica nel registrar di dominio]: #bkmk_configurecname
[Configurare un record A per il nome di dominio]:#bkmk_configurearecord
[Impostare il nome di dominio nel portale di gestione]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /it-it/pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../custom-dns/
[trafficmanager]: /it-it/documentation/articles/web-sites-traffic-manager/
[addendpoint]: http://msdn.microsoft.com/library/windowsazure/hh744839.aspx
[createprofile]: http://msdn.microsoft.com/library/windowsazure/dn339012.aspx

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png 


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png
