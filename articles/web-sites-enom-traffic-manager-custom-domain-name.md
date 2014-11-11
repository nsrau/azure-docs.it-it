<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure website that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Configurazione di un nome di dominio personalizzato per un sito Web di Azure usando Gestione traffico (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Sito Web mediante Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

In questo articolo vengono fornite istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [eNom][eNom] con Siti Web di Azure.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS][Informazioni sui record DNS]
-   [Configurazione dei siti Web per la modalità standard][Configurazione dei siti Web per la modalità standard]
-   [Aggiunta di un record DNS per il dominio personalizzato][Aggiunta di un record DNS per il dominio personalizzato]
-   [Abilitazione di Gestione traffico per il proprio sito Web][Abilitazione di Gestione traffico per il proprio sito Web]

## <a name="understanding-records"></a>Informazioni sui record DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>Configurare i siti Web per la modalità standard

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## Aggiunta di un record DNS per il dominio personalizzato

</p>
Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da eNom. Per individuare gli strumenti DNS per enom.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso eNom e selezionare **Domains**, quindi **My Domains**. Verranno visualizzati i propri nomi di dominio.

2.  Nella pagina **My Domains** usare il campo **Manage Domain** per selezionare **Host Records**. Verranno visualizzati i campi relativi ai record dell'host.

    ![Scheda DNS Zone File][Scheda DNS Zone File]

3.  L'editor Host Records consente di selezionare il tipo di record specifico usando il campo **Record Type**. Per i siti Web di Azure che usano Gestione traffico, usare solo la selezione **CNAME (Alias)** poiché Gestione traffico supporta solo i record CNAME.

    ![editor dei file di zona][editor dei file di zona]

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **Host Name** sul sottodominio che si desidera usare, ad esempio **www**. È necessario impostare il campo **Indirizzo** sul nome di dominio **.trafficmanager.net** del profilo di Gestione traffico in uso con il sito Web di Azure, ad esempio **contoso.trafficmanager.net**.

4.  Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Save** per salvare le modifiche.

## <a name="enabledomain"></a>Abilitare il sito Web di Gestione traffico

[WACOM.INCLUDE [modes][1]]

  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [eNom]: https://enom.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Informazioni sui record DNS]: #understanding-records
  [Configurazione dei siti Web per la modalità standard]: #bkmk_configsharedmode
  [Aggiunta di un record DNS per il dominio personalizzato]: #bkmk_configurecname
  [Abilitazione di Gestione traffico per il proprio sito Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Scheda DNS Zone File]: ./media/web-sites-custom-domain-name/e-hostrecords.png
  [editor dei file di zona]: ./media/web-sites-custom-domain-name/e-editrecordstm.png
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
