<properties
	pageTitle="Configurazione di un nome di dominio personalizzato nel servizio app di Azure"
	description="Ulteriori informazioni su come usare un nome di dominio personalizzato per un'app Web nel servizio app di Azure."
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="mwasson"/>

# Configurazione di un nome di dominio personalizzato nel servizio app di Azure

<div class="dev-center-tutorial-selector sublanding">
  <a href="/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Dominio personalizzato</a>
  <a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a>
  <a href="/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Soluzioni di rete</a>
  <a href="/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a>
  <a href="/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a>
  <a href="/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a>
  <a href="/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a>
  <a href="/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a>
  <a href="/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a>
</div>
<div class="dev-center-tutorial-subselector">
  <a href="/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">App Web</a> | <a href="/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Web Apps using Traffic Manager">App Web tramite Gestione traffico</a>
</div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Quando si crea un'app Web, Azure la assegna a un sottodominio di azurewebsites.net. Ad esempio, se l'app Web è denominata **contoso**, l'URL corrisponderà a **contoso.azurewebsites.net**. Azure assegna anche un indirizzo IP virtuale.

<!--todo:![contoso.azurewebsites.net subdomain][subdomain]-->

Per un'app Web di produzione, è probabile che l'amministratore desideri che gli utenti vedano un nome di dominio personalizzato. In questo articolo viene spiegato come configurare un dominio personalizzato utilizzando [le app Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714). L'articolo fornisce istruzioni di carattere generale per qualsiasi registrar di dominio. Le schede riportate all'inizio dell'articolo consentono di visualizzare articoli relativi a registrar specifici.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:


-   [Informazioni generali]
-   [Tipi di record DNS]
-   [Individuare l'indirizzo IP virtuale]
-   [Creare i record DNS]
-   [Creare un record "awverify" (solo record A)](#awverify)
-   [Abilitare il nome del dominio nell'app Web]


## Informazioni generali

Di seguito sono riportati i passaggi generali per la configurazione di un nome di dominio personalizzato:

1. Riservare il nome di dominio. Questo processo non viene descritto in questo articolo. Sono disponibili numerosi registrar di dominio. Quando si esegue l'accesso, il sito del registrar fornisce la procedura per il processo.
1. Creare record DNS che eseguono il mapping del dominio all'app Web di Azure.
1. Aggiungere il nome di dominio all'interno del [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

All'interno del quadro di base, esistono diversi casi specifici da prendere in considerazione:

- Mapping del dominio radice. Il dominio radice è il dominio riservato con il registrar, ad esempio, **contoso.com**.
- Mapping di un sottodominio, ad esempio, **blogs.contoso.com**.  È possibile eseguire il mapping di sottodomini a differenti app Web.
- Mapping di un carattere jolly, ad esempio, ***.contoso.com**. Una voce con caratteri jolly si applica a tutti i sottodomini del dominio.

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]


## Tipi di record DNS

Domain Name System (DNS) usa record di dati per eseguire il mapping di nomi di dominio a indirizzi IP. Esistono diversi tipi di record DNS. Nel caso delle app Web, creare un record *A* o un record *CNAME*.

- Un record A **(indirizzo)** esegue il mapping di un nome di dominio a un indirizzo IP.
- Un record **CNAME (nome canonico)** esegue il mapping di un nome di dominio a un altro nome di dominio. DNS usa il secondo nome per cercare l'indirizzo. Gli utenti possono ancora vedere il primo nome di dominio nel browser in uso. Ad esempio, è possibile eseguire il mapping di contoso.com in *&lt;appWebutente&gt;*.azurewebsites.net.

Se l'indirizzo IP cambia, una voce CNAME resta valida, mentre un record A deve essere aggiornato. Alcuni registrar di dominio, tuttavia, non consentono l'uso di record CNAME per il dominio radice o un dominio con caratteri jolly. In questi casi, è necessario usare un record A.

> [AZURE.NOTE] L'indirizzo IP può cambiare se si elimina e si crea nuovamente l'app Web oppure se la sua modalità viene reimpostata su gratuita.


## Individuare l'indirizzo IP virtuale

Se si sta creando un record CNAME, ignorare questo passaggio. Per creare un record A, è necessario disporre dell'indirizzo IP virtuale dell'app Web. Per ottenere l'indirizzo IP:

1.	Accedere al [portale di gestione di Azure](https://portal.azure.com) dal browser.
2.	Fare clic sull'opzione **Sfoglia** a sinistra nella pagina.
3.	Fare clic sul pannello **App Web**.
4.	Fare clic sul nome dell'app Web.
5.	Nella pagina **Elementi essenziali**, fare clic su **Tutte le impostazioni**.
6.	Fare clic su **Domini e SSL personalizzati**. L'indirizzo IP si trova nella parte inferiore della pagina (proprio al di sopra della sezione **Binding SSL**).

## Creare i record DNS

Accedere al registrar di dominio e usare i relativi strumenti per aggiungere un record A o CNAME. L'app Web di ogni registrar è leggermente differente. Di seguito, sono riportate le linee guida comuni.

1.	Individuare la pagina relativa alla gestione dei record DNS. Cercare i collegamenti o le aree del sito denominate **Nome dominio**, **DNS** o **Gestione nome server**. Spesso, è possibile trovare i collegamenti controllando le informazioni dell'account e cercando un link analogo a **Domini personali**.
2.	Una volta individuata la pagina di gestione, cercare un collegamento che consenta di aggiungere o modificare i record DNS. Tale collegamento può essere elencato come **File di zona**, **Record DNS** o come un collegamento di configurazione **avanzata**.

È possibile che nella pagina i record A e CNAME siano elencati separatamente oppure che venga fornito un elenco a discesa per la selezione del tipo di record. È possibile anche che ai tipi di record venga fatto riferimento con nomi diversi, ad esempio **Record indirizzo IP** anziché record A oppure **Record alias** anziché record CNAME.  Di solito, il registrar crea automaticamente alcuni record. È quindi possibile che siano già presenti dei record per il dominio radice o per sottodomini comuni, ad esempio **www**.

Quando si crea o modifica un record, i campi consentono di eseguire il mapping del nome di dominio a un indirizzo IP (per i record A) o a un altro dominio (per i record CNAME). Per quanto riguarda un record CNAME, verrà eseguito il mapping  *from* dominio personalizzato  *to* sottodominio azurewebsites.net.

In diversi strumenti del registrar è sufficiente digitare parte del nome del dominio, non l'intero nome. Molti strumenti, inoltre, usano il carattere '@' per indicare il dominio radice. Ad esempio:

<table cellspacing="0" border="1">
  <tr>
    <th>Host</th>
    <th>Tipo di record</th>
    <th>Indirizzo IP o URL</th>
  </tr>
  <tr>
    <td>@</td>
    <td>A (indirizzo)</td>
    <td>127.0.0.1</td>
  </tr>
  <tr>
    <td>www</td>
    <td>CNAME (alias)</td>
    <td>contoso.azurewebsites.net</td>
  </tr>
</table>

Si supponga che il nome di dominio personalizzato sia 'contoso.com'. In questo caso, verranno creati i record seguenti:

- **contoso.com** associato a 127.0.0.1.
- **www.contoso.com** associato a **contoso.azurewebsites.net**.


<h2 id="awverify">Creare un record "awverify" (soltanto record A)</h2>

Se si crea un record A, l'app web richiede anche uno speciale record CNAME usato per verificare che l'utente sia proprietario del dominio che sta tentando di usare. Tale record CNAME deve avere il formato seguente.

- *Se il record A esegue il mapping al domino radice o a un dominio con caratteri jolly:* Creare un record CNAME che esegua il mapping da **awverify.&lt;dominioutente&gt;** a **awverify.&lt;appwebutente&gt;.azurewebsites.net**.  Ad esempio, se il record A è associato a **contoso.com**, creare un record CNAME per **awverify.contoso.com**.
- *Se il record A esegue il mapping a un sottodominio specifico:* Creare un record CNAME che esegua il mapping da **awverify.&lt;sottodominio&gt;** a **awverify.&lt;appwebutente&gt;.azurewebsites.net**. Ad esempio, se un record A è associato a **blogs.contoso.com**, creare un record CNAME per **awverify.blogs.contoso.com**.

I visitatori che accedono all'app Web dell'utente non vedono il sottodominio awverify, usato soltanto da Azure per la verifica del dominio.

## Abilitare il nome del dominio nell'app Web

[AZURE.INCLUDE [modalità](../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrare un account di Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751)  dove è possibile creare immediatamente un'app Web di base e temporanea nel servizio app. Non é necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Il servizio app di Azure e il suo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal vecchio al nuovo portale, vedere: [Riferimenti per esplorare il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Anchors. -->
[Informazioni generali]: #overview
[Tipi di record DNS]: #dns-record-types
[Individuare l'indirizzo IP virtuale]: #find-the-virtual-ip-address
[Creare i record DNS]: #create-the-dns-records
[Abilitare il nome del dominio nell'app Web]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!--HONumber=49-->