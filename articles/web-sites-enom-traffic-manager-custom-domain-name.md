<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configurazione di un nome di dominio personalizzato per un sito Web di Azure usando Gestione traffico" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description=""services""web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configurazione di un nome di dominio personalizzato per un sito Web di Azure usando Gestione traffico (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Sito Web che usa Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

In questo articolo vengono fornite istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [eNom](https://enom.com) con Siti Web di Azure.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Configurare i siti Web per la modalità standard](#bkmk_configsharedmode)
-   [Aggiungere un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitare Gestione traffico per il proprio sito Web](#enabledomain)

<h2><a name="understanding-records"></a>Informazioni sui record DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurare i siti Web per la modalità standard</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Aggiungere un record DNS per il dominio personalizzato</h2>

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da eNom. Per individuare gli strumenti DNS per enom.com, attenersi alla procedura seguente

1. Accedere al proprio account presso eNom e selezionare **Domains**, quindi **My Domains**. Verranno visualizzati i propri nomi di dominio.

2. Nella pagina **My Domains** usare il campo **Manage Domain** per selezionare **Host Records**. Verranno visualizzati i campi relativi ai record dell'host.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. L'editor Host Records consente di selezionare il tipo di record specifico usando il campo **Record Type**. Per i siti Web di Azure che usano Gestione traffico, usare solo la selezione **CNAME (Alias)** poiché Gestione traffico supporta solo i record CNAME.

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecordstm.png)

	* Quando si aggiunge un record CNAME, è necessario impostare il campo **Host Name** sul sottodominio che si vuole usare, ad esempio **www**. È necessario impostare il campo **Address** sul nome di dominio **.trafficmanager.net** del profilo di Gestione traffico in uso con il sito Web di Azure, ad esempio **contoso.trafficmanager.net**.

5. Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Save** per salvare le modifiche.

<h2><a name="enabledomain"></a>Abilitare il sito Web di Gestione traffico</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
