<properties
	pageTitle="Configurare un nome di dominio personalizzato nel servizio app di Azure"
	description="Ulteriori informazioni su come usare un nome di dominio personalizzato per un'app Web nel servizio app di Azure."
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="mwasson"/>

# Configurare un nome di dominio personalizzato nel servizio app di Azure

> [AZURE.SELECTOR]
- [Buy Domain for Web Apps](custom-dns-web-site-buydomains-web-app.md)
- [Web Apps with External Domains](web-sites-custom-domain-name.md)
- [Web Apps with Traffic Manager](web-sites-traffic-manager-custom-domain-name.md)
- [GoDaddy](web-sites-godaddy-custom-domain-name.md)

Quando si crea un'app Web, Azure la assegna a un sottodominio di azurewebsites.net. Ad esempio, se l'app Web è denominata **contoso**, l'URL corrisponderà a **contoso.azurewebsites.net**. Azure assegna anche un indirizzo IP virtuale.

Per un'app Web di produzione, è probabile che l'amministratore desideri che gli utenti vedano un nome di dominio personalizzato. Questo articolo spiega come acquistare e configurare un dominio personalizzato con [App Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## Panoramica

Se si dispone già di un nome di dominio o si desidera riservare un dominio da altri registrar, questa è la procedura generale da seguire per definire un nome di dominio personalizzato per l'app Web:

1. Riservare il nome di dominio. Questo processo non viene descritto in questo articolo. Sono disponibili numerosi registrar di dominio. Quando si esegue l'accesso, il sito del registrar fornisce la procedura per il processo.
1. Creare record DNS che eseguono il mapping del dominio all'app Web di Azure.
1. Aggiungere il nome di dominio all'interno del [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

All'interno del quadro di base, esistono diversi casi specifici da prendere in considerazione:

- Mapping del dominio radice. Il dominio radice è il dominio riservato con il registrar, ad esempio **contoso.com**.
- Mapping di un sottodominio, ad esempio **blogs.contoso.com**. È possibile eseguire il mapping di sottodomini a differenti app Web.
- Mapping di un carattere jolly, ad esempio ***.contoso.com**. Una voce con caratteri jolly si applica a tutti i sottodomini del dominio.

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes.md)]


## Tipi di record DNS

Domain Name System (DNS) usa record di dati per eseguire il mapping di nomi di dominio a indirizzi IP. Esistono diversi tipi di record DNS. Per le app Web, creare un record *A* o un record *CNAME*.

- Un record **A** (indirizzo) esegue il mapping di un nome di dominio a un indirizzo IP.
- Un record **CNAME** (nome canonico) esegue il mapping di un nome di dominio a un altro nome di dominio. DNS usa il secondo nome per cercare l'indirizzo. Gli utenti possono ancora vedere il primo nome di dominio nel browser in uso. Ad esempio, è possibile eseguire il mapping di contoso.com in *&lt;appWebutente&gt;*.azurewebsites.net.

Se l'indirizzo IP cambia, una voce CNAME resta valida, mentre un record A deve essere aggiornato. Alcuni registrar di dominio, tuttavia, non consentono l'uso di record CNAME per il dominio radice o un dominio con caratteri jolly. In questi casi, è necessario usare un record A.

> [AZURE.NOTE]L'indirizzo IP può cambiare se si elimina e si crea nuovamente l'app Web oppure se la sua modalità viene reimpostata su gratuita.


## Trovare l'indirizzo IP virtuale

Se si sta creando un record CNAME, ignorare questo passaggio. Per creare un record A, è necessario disporre dell'indirizzo IP virtuale dell'app Web. Per ottenere l'indirizzo IP:

1.	Accedere al [Portale di Azure](https://portal.azure.com) dal browser.
2.	Fare clic sull'opzione **Sfoglia** a sinistra nella pagina.
3.	Fare clic sul pannello **App Web**.
4.	Fare clic sul nome dell'app Web.
5.	Nella pagina **Elementi essenziali**, fare clic su **Tutte le impostazioni**.
6.	Fare clic su **Domini e SSL personalizzati**. 
7.	Nel pannello **Domini personalizzati e SSL** fare clic su **Porta domini esterni**. L'indirizzo IP si trova in basso in questa parte.

## Creare i record DNS

Accedere al registrar di dominio e usare i relativi strumenti per aggiungere un record A o CNAME. L'app Web di ogni registrar è leggermente differente. Di seguito, sono riportate le linee guida comuni.

1.	Individuare la pagina relativa alla gestione dei record DNS. Individuare collegamenti o aree del sito denominate **Nome dominio**, **DNS** o **Name Server Management**. Spesso è possibile individuare un collegamento nelle informazioni dell'account, cercando una voce simile a **My domains**.
2.	Una volta individuata la pagina di gestione, cercare un collegamento che consenta di aggiungere o modificare i record DNS. Tale collegamento può essere denominato **Zone file** o **DNS Records** oppure figurare come collegamento di configurazione in **Advanced**.

È possibile che nella pagina i record A e CNAME siano elencati separatamente oppure che venga fornito un elenco a discesa per la selezione del tipo di record. È possibile anche che ai tipi di record venga fatto riferimento con nomi diversi, ad esempio **IP Address record** anziché record A o **Alias Record** anziché record CNAME. Normalmente il registrar crea automaticamente alcuni record. È quindi possibile che siano già presenti dei record per il dominio radice o per sottodomini comuni, ad esempio **www**.

Quando si crea o modifica un record, i campi consentono di eseguire il mapping del nome di dominio a un indirizzo IP (per i record A) o a un altro dominio (per i record CNAME). Per quanto riguarda un record CNAME, verrà eseguito il mapping *dal* dominio personalizzato *al* sottodominio azurewebsites.net.

In diversi strumenti del registrar è sufficiente digitare parte del nome del dominio, non l'intero nome. Molti strumenti, inoltre, usano il carattere ‘@’ per indicare il dominio radice. Ad esempio:

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

Si supponga che il nome di dominio personalizzato sia contoso.com. In questo caso, verranno creati i record seguenti:

- **contoso.com** associato a 127.0.0.1.
- **www.contoso.com** associato a **contoso.azurewebsites.net**.

>[AZURE.NOTE]È possibile usare il DNS di Azure per ospitare i record di dominio necessari per l'app Web. Per configurare il dominio personalizzato e creare i record in DNS di Azure, vedere [Creare record DNS personalizzati per un'app Web](../dns-web-sites-custom-domain).

<a name="awverify" />
## Creare un record awverify (solo record A)

Se si crea un record A, l'app web richiede anche uno speciale record CNAME usato per verificare che l'utente sia proprietario del dominio che sta tentando di usare. Tale record CNAME deve avere il formato seguente.

- *Se il record A esegue il mapping al domino radice o al dominio con caratteri jolly:* creare un record CNAME che esegue il mapping da **awverify.&lt;dominioutente&gt;** a **awverify.&lt;nomeappWebutente&gt;.azurewebsites.net**. Ad esempio, se il record A è associato a **contoso.com**, creare un record CNAME per **awverify.contoso.com**.
- *Se il record A esegue il mapping a uno specifico sottodominio:* creare un record CNAME che esegue il mapping da **awverify.&lt;sottodominio&gt;** a **awverify.&lt;nomeappWebutente&gt;.azurewebsites.net**. Ad esempio, se il record A è associato a **blogs.contoso.com**, creare un record CNAME per **awverify.blogs.contoso.com**.

I visitatori che accedono all'app Web dell'utente non vedono il sottodominio awverify, usato soltanto da Azure per la verifica del dominio.

## Abilitare il nome del dominio nell'app Web

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.


## Passaggi successivi

Per altre informazioni, vedere: [Introduzione a DNS di Azure](../dns/dns-getstarted-create-dnszone) e [Delegare un dominio a DNS di Azure](../dns/dns-domain-delegation)

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Anchors. -->
[Overview]: #overview
[DNS record types]: #dns-record-types
[Find the virtual IP address]: #find-the-virtual-ip-address
[Create the DNS records]: #create-the-dns-records
[Enable the domain name on your web app]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
 

<!---HONumber=Sept15_HO3-->