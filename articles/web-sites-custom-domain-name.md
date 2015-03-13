<properties 
	pageTitle="Configurazione di un nome di dominio personalizzato per un sito Web di Azure" 
	description="Informazioni su come usare un nome di dominio personalizzato con siti Web di Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr, jroth, mwasson"/>

#Configurazione di un nome di dominio personalizzato per un sito Web di Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web con Gestione traffico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Quando si crea un sito Web, Azure lo assegna a un sottodominio di azurewebsites.net. Se ad esempio il nome del sito Web è **contoso**, l'URL sarà **contoso.azurewebsites.net**. Azure assegna anche un indirizzo IP virtuale.

![sottodominio contoso.azurewebsites.net][sottodominio]

Per un sito Web di produzione, è probabile che l'amministratore desideri che gli utenti vedano un nome di dominio personalizzato. Questo articolo spiega come configurare un dominio personalizzato con Siti Web di Azure. L'articolo fornisce istruzioni di carattere generale per qualsiasi registrar di dominio. Le schede riportate all'inizio dell'articolo consentono di visualizzare articoli relativi a registrar specifici.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:


-   [Informazioni generali]
-   [Tipi di record DNS]
-   [Individuare l'indirizzo IP virtuale]
-   [Creare i record DNS]
-   [Creare un record "awverify" (solo record A)](#awverify)
-   [Abilitare il nome del dominio nel sito Web]


## Informazioni generali

Di seguito sono riportati i passaggi generali per la configurazione di un nome di dominio personalizzato:

1. Riservare il nome di dominio. Questo processo non viene descritto in questo articolo. Sono disponibili numerosi registrar di dominio. Quando si esegue l'accesso, il sito del registrar fornisce la procedura per il processo.
1. Creare record DNS che eseguono il mapping del dominio al sito Web di Azure. 
1. Aggiungere un nome di dominio nel portale di gestione di Azure. 

All'interno del quadro di base, esistono diversi casi specifici da prendere in considerazione:

- Mapping del dominio radice. Il dominio radice è il dominio riservato con il registrar, ad esempio **contoso.com**.
- Mapping di un sottodominio, ad esempio **blogs.contoso.com**.  È possibile eseguire il mapping di sottodomini differenti a siti Web differenti.
- Mapping di un carattere jolly, ad esempio ***.contoso.com**. Una voce con caratteri jolly si applica a tutti i sottodomini del dominio. 
 
[AZURE.INCLUDE [modalità](../includes/custom-dns-web-site-modes.md)]


## Tipi di record DNS

Domain Name System (DNS) usa record di dati per eseguire il mapping di nomi di dominio a indirizzi IP. Esistono diversi tipi di record DNS. Per i siti Web, creare un record *A* o un record *CNAME*.

- Un record A **(indirizzo)** esegue il mapping di un nome di dominio a un indirizzo IP. 
- Un record **CNAME (nome canonico)** esegue il mapping di un nome di dominio a un altro nome di dominio. DNS usa il secondo nome per cercare l'indirizzo. Gli utenti possono ancora vedere il primo nome di dominio nel browser in uso. È possibile, ad esempio, eseguire il mapping di contoso.com a *&lt;sitoutente&gt;*.azurewebsites.net. 

Se l'indirizzo IP cambia, una voce CNAME resta valida, mentre un record A deve essere aggiornato. Alcuni registrar di dominio, tuttavia, non consentono l'uso di record CNAME per il dominio radice o un dominio con caratteri jolly. In questi casi, è necessario usare un record A. 

> [AZURE.NOTE] L'indirizzo IP può cambiare se si elimina e si crea nuovamente il sito Web oppure se la modalità del sito Web viene reimpostata su gratuita.


## Individuare l'indirizzo IP virtuale 

Se si sta creando un record CNAME, ignorare questo passaggio. Per creare un record A, è necessario disporre dell'indirizzo IP virtuale del sito Web. Per ottenere l'indirizzo IP:

1.	Accedere al [portale di gestione di Azure](https://manage.windowsazure.com) dal browser.
2.	Nella scheda **Siti Web** fare clic sul nome del sito e selezionare **Dashboard**.
3.	Selezionare **Gestisci domini** nella parte inferiore della pagina. Se questa opzione è disabilitata, assicurarsi di usare la modalità condivisa, base o standard. Per altre informazioni, vedere [Come scalare siti Web](http://azure.microsoft.com/documentation/articles/web-sites-scale/). 

	![](media/web-sites-custom-domain-name/dncmntask-cname-6.png)

4.	L'indirizzo IP è riportato nella parte inferiore della finestra di dialogo.

	![](media/web-sites-custom-domain-name/ipaddress.png)

## Creare i record DNS

Accedere al registrar di dominio e usare i relativi strumenti per aggiungere un record A o CNAME. Il sito Web di ogni registrar è leggermente diverso, ma esistono delle linee guida generali. 

1.	Individuare la pagina relativa alla gestione dei record DNS. Individuare collegamenti o aree del sito denominate **Domain name**, **DNS** o **Name Server Management**. Spesso è possibile individuare il collegamento nelle informazioni dell'account cercando una voce simile a **My domains**.
2.	Una volta individuata la pagina di gestione, cercare un collegamento che consenta di aggiungere o modificare i record DNS. Tale collegamento può essere denominato **Zone file** o **DNS Records** oppure figurare come collegamento di configurazione in **Advanced**.

È possibile che nella pagina i record A e CNAME siano elencati separatamente oppure che venga fornito un elenco a discesa per la selezione del tipo di record. È possibile anche che ai tipi di record venga fatto riferimento con nomi diversi, ad esempio **record di indirizzo IP** anziché record A o **record di alias** anziché record CNAME.  Normalmente il registrar crea automaticamente alcuni record. È quindi possibile che siano già presenti dei record per il dominio radice o per sottodomini comuni, ad esempio **www**.

Quando si crea o modifica un record, i campi consentono di eseguire il mapping del nome di dominio a un indirizzo IP (per i record A) o a un altro dominio (per i record CNAME). Per quanto riguarda un record CNAME, verrà eseguito il mapping *from* dominio personalizzato *to* sottodominio azurewebsites.net. 

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

Si supponga che il nome di dominio personalizzato sia 'contoso.com'. In questo caso, verranno creati i seguenti record:

- **contoso.com** associato a 127.0.0.1.
- **www.contoso.com** associato a **contoso.azurewebsites.net**


<h2 id="awverify">Creare un record "awverify" (solo record A)</h2>

Se si crea un record A, Siti Web di Azure richiede anche uno speciale record CNAME usato per verificare che l'utente sia proprietario del dominio che sta tentando di usare. Tale record CNAME deve avere il formato seguente. 

- *Se il record A esegue il mapping al domino radice o al dominio con caratteri jolly:* creare un record CNAME che esegua il mapping da **awverify.&lt;dominioutente&gt;** a **awverify.&lt;nomesitoWebutente&gt;.azurewebsites.net**. Ad esempio, se il record A è associato a **contoso.com**, creare un record CNAME per **awverify.contoso.com**.
- *Se il record A esegue il mapping a un sottodominio specifico:* creare un record CNAME che esegua il mapping da **awverify.&lt;sottodominio&gt;** a **awverify.&lt;nomesitoWebutente&gt;.azurewebsites.net**. Ad esempio, se il record A è associato a **blogs.contoso.com**, creare un record CNAME per **awverify.blogs.contoso.com**.

I visitatori che accedono al sito dell'utente non vedono il sottodominio awverify, usato soltanto da Azure per la verifica del dominio.

## Abilitare il nome del dominio nel sito Web

[AZURE.INCLUDE [modalità](../includes/custom-dns-web-site-enable-on-web-site.md)]


<!-- Anchors. -->
[Informazioni generali]: #overview
[Tipi di record DNS]: #dns-record-types
[Individuare l'indirizzo IP virtuale]: #find-the-virtual-ip-address
[Creare i record DNS]: #create-the-dns-records
[Abilitare il nome del dominio nel sito Web]: #enable-the-domain-name-on-your-website

<!-- Images -->
[sottodominio]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!--HONumber=42-->
