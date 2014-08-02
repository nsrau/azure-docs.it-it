<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a custom domain name" pageTitle="Configure a custom domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configurazione di un nome di dominio personalizzato per un sito Web di Azure utilizzando Gestione traffico
==========================================================================================================

[Dominio personalizzato](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizzato")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sito Web](/en-us/documentation/articles/web-sites-custom-domain-name/ "Siti Web") | [Sito Web che utilizza Gestione traffico](/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/ "Sito Web che utilizza Gestione traffico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato con Siti Web di Azure in cui viene utilizzato Gestione traffico per il bilanciamento del carico. Verificare nelle schede all'inizio dell'articolo se il registrar in uso è incluso nell'elenco. In caso affermativo, selezionare la scheda corrispondente per la procedura specifica di tale registrar.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Configurazione dei siti Web per la modalità standard](#bkmk_configsharedmode)
-   [Aggiunta di un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitazione di Gestione traffico per il proprio sito Web](#enabledomain)

Informazioni sui record DNS
---------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

Configurazione dei siti Web per la modalità standard
----------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<h2>Aggiunta di un record DNS per il dominio personalizzato</h2>

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato utilizzando gli strumenti forniti dal registrar da cui è stato acquistato il nome di dominio. Per individuare e utilizzare gli strumenti DNS, attenersi alla procedura seguente.

1.  Accedere all'account presso il registrar e cercare la pagina in cui gestire i record DNS. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**. Un collegamento a questa pagina è spesso disponibile nelle informazioni dell'account, cercando una voce simile a **My domains**.

2.  Dopo aver trovato la pagina di gestione del nome di dominio, cercare un collegamento che consenta di modificare i record DNS. Tale collegamento può essere denominato **Zone file** o **DNS Records** oppure figurare come collegamento di configurazione in **Advanced**.

    -   La pagina conterrà molto probabilmente alcuni record già creati, ad esempio una voce per associare '**@**' o '\*' a una pagina di registrazione semplice. Può inoltre contenere record per sottodomini comuni, ad esempio **www**.
    -   Nella pagina saranno presenti voci per record **CNAME** oppure verrà visualizzato un elenco a discesa per selezionare il tipo di record. È anche possibile che siano presenti voci per altri record, ad esempio **A** e **MX**. In alcuni casi, i record CNAME avranno una denominazione diversa, come ad esempio nel caso dei record **Alias**.
    -   La pagina conterrà inoltre campi che consentono di **eseguire il mapping** da un **nome host** o un **nome di dominio** a un altro nome di dominio.

3.  Sebbene le specifiche di ogni registrar variano, in generale viene eseguito il mapping *dal* nome di dominio personalizzato, ad esempio **contoso.com**, *al* nome di dominio di Gestione traffico (**contoso.trafficmanager.net**) utilizzato per il sito Web di Azure.

4.  Dopo aver completato l'aggiunta o la modifica di record DNS presso il registrar, salvare le modifiche.

Abilitazione di Gestione traffico per il proprio sito Web
---------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

