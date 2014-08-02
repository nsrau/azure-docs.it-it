<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configurazione di un nome di dominio personalizzato per un sito Web di Azure utilizzando Gestione traffico (eNom)
=================================================================================================================

[Dominio personalizzato](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizzato")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sito Web](/en-us/documentation/articles/web-sites-enom-custom-domain-name/ "Siti Web") | [Sito Web che utilizza Gestione traffico](/en-us/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "Sito Web che utilizza Gestione traffico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato acquistato da [eNom](https://enom.com) con Siti Web di Azure.

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

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato utilizzando gli strumenti forniti da eNom. Per individuare gli strumenti DNS per enom.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso eNom e selezionare **Domains**, quindi **My Domains**. Verranno visualizzati i propri nomi di dominio.

2.  Nella pagina **My Domains** utilizzare il campo **Manage Domain** per selezionare **Host Records**. Verranno visualizzati i campi relativi ai record dell'host.

    ![Scheda DNS Zone File](./media/web-sites-custom-domain-name/e-hostrecords.png)

3.  L'editor Host Records consente di selezionare il tipo di record specifico utilizzando il campo **Record Type**. Per i siti Web di Azure che utilizzano Gestione traffico, utilizzare solo la selezione **CNAME (Alias)** poiché Gestione traffico supporta solo i record CNAME.

    ![editor dei file di zona](./media/web-sites-custom-domain-name/e-editrecordstm.png)

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **Host Name** sul sottodominio che si desidera utilizzare, ad esempio **www**. È necessario impostare il campo **Address** sul nome di dominio **.trafficmanager.net** del profilo di Gestione traffico in uso con il sito Web di Azure, ad esempio **contoso.trafficmanager.net**.

4.  Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Save** per salvare le modifiche.

Abilitazione di Gestione traffico per il proprio sito Web
---------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

