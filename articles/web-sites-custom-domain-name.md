<properties  title="Learn how to configure an Azure web site to use a custom domain name" pageTitle="Configure a custom domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# Configurazione di un nome di dominio personalizzato per un sito Web di Azure

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Dominio personalizzato" class="current">Dominio personalizzato</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

 
<div  class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-custom-domain-name/" title="Siti Web" class="current">Sito Web</a> | <a href="/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Sito Web che utilizza Gestione traffico">Sito Web che utilizza Gestione traffico</a></div>

 [WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato con Siti Web di Azure. Verificare nelle schede all'inizio dell'articolo se il registrar in uso è incluso nell'elenco. In caso affermativo, selezionare la scheda corrispondente per la procedura specifica di tale registrar.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

* [Informazioni sui record DNS](#understanding-records)
* [Configurazione dei siti Web per la modalità di base, condivisa o standard](#bkmk_configsharedmode)
* [Aggiunta di un record DNS per il dominio personalizzato](#bkmk_configurecname)
* [Abilitazione del dominio nel sito Web](#enabledomain)

<h2><a name="understanding-records"></a>Informazioni sui record DNS</h2>


[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurazione dei siti Web per la modalità di base, condivisa o standard</h2>


[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Aggiunta di un record DNS per il dominio
personalizzato</h2>

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato utilizzando gli strumenti forniti dal registrar da cui è stato acquistato il nome di dominio. Per individuare e utilizzare gli strumenti DNS, attenersi alla procedura seguente.

1.  Accedere all'account presso il registrar e cercare la pagina in cui gestire i record DNS. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**. Un collegamento a questa pagina è spesso disponibile nelle informazioni dell'account, cercando una voce simile a **My domains**.

2.  Dopo aver trovato la pagina di gestione del nome di dominio, cercare un collegamento che consenta di modificare i record DNS. Tale collegamento può essere denominato **Zone file** o **DNS Records** oppure figurare come collegamento di configurazione in **Advanced**.
    
    * La pagina conterrà molto probabilmente alcuni record già creati, ad esempio una voce per associare '**@**' o '\*' a una pagina di registrazione semplice. Può inoltre contenere record per sottodomini comuni, ad esempio **www**.
    * Nella pagina saranno presenti voci per **record A** e **record CNAME** oppure verrà visualizzato un elenco a discesa per selezionare il tipo di record. È anche possibile che siano presenti voci per altri record, ad esempio **record MX**. In alcuni casi a tali record verrà fatto riferimento con nomi diversi, ad esempio **record di indirizzi IP** anziché record A oppure **record di alias** anziché record CNAME. * La pagina conterrà inoltre campi che consentono di **eseguire il mapping** da un **nome host** o un **nome di dominio** a un **indirizzo IP** o a un altro nome di dominio.

3.  Sebbene le specifiche di ogni registrar variano, in generale viene eseguito il mapping *dal* nome di dominio personalizzato, ad esempio **contoso.com**, *al* nome di dominio del sito Web di Azure, ad esempio **contoso.azurewebsites.net**, oppure all'indirizzo IP virtuale del sito Web di Azure.
    
    * Il mapping dei record CNAME verrà sempre eseguito al dominio di Siti Web di Azure, ovvero **contoso.azurewebsites.net**. Si eseguirà pertanto il mapping *da* un dominio, ad esempio **www**, *all'* indirizzo **<nomesitoWeb>.azurewebsites.net**.


     
      > [WACOM.NOTE] Se si utilizza un record A, è inoltre necessario
      > aggiungere un record CNAME con una delle seguenti
      > configurazioni:
      > 
      > * Mapping *da* **www** *a* **<nome
      >   sitoWeb>.azurewebsites.net**
      > 
      > OPPURE
      > 
      > * Mapping *da* **awverify.www** *a*
      >   **awverify.<nomesitoWeb>.azurewebsites.net**.
      > 
      > Questo nome di record CNAME viene utilizzato da Azure per
      > convalidare la proprietà del dominio descritto dal record A.
    
    * Il mapping dei record A verrà sempre eseguito all'indirizzo IP virtuale di Siti Web di Azure. Si eseguirà pertanto il mapping *da* un dominio, come **www**, *all'* indirizzo IP virtuale del sito Web.


     
      > [WACOM.NOTE] Per eseguire il mapping di un dominio radice, ad
      > esempio **contoso.com**, a un sito Web di Azure, spesso si
      > esegue il mapping da '**@**' o da una voce vuota
      > all'indirizzo IP virtuale. Per un mapping con caratteri jolly
      > di tutti i sottodomini all'indirizzo IP virtuale, in genere si
      > esegue il mapping da '\*' all'indirizzo IP virtuale.
      > 
      > Le specifiche relative al mapping di una radice o di un
      > carattere jolly variano a seconda del registrar. Per indicazioni
      > più specifiche consultare la documentazione fornita dal
      > registrar.

4.  Dopo aver completato l'aggiunta o la modifica di record DNS presso il registrar, salvare le modifiche.

<h2><a name="enabledomain"></a>Abilitazione del nome del dominio nel sito Web</h2>


[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]