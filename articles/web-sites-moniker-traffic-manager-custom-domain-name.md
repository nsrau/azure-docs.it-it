<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Websites, Moniker, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Configurazione di un nome di dominio personalizzato per un sito Web di Azure usando Traffic Manager (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Sito Web mediante Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

Questo articolo fornisce istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [Moniker][1] con Siti Web di Azure.

[WACOM.INCLUDE [tmwebsitefooter][tmwebsitefooter]]

[WACOM.INCLUDE [introfooter][introfooter]]

Contenuto dell'articolo:

-   [Informazioni sui record DNS][Informazioni sui record DNS]
-   [Configurazione dei siti Web per la modalità standard][Configurazione dei siti Web per la modalità standard]
-   [Aggiunta di un record DNS per il dominio personalizzato][Aggiunta di un record DNS per il dominio personalizzato]
-   [Abilitazione di Gestione traffico per il proprio sito Web][Abilitazione di Gestione traffico per il proprio sito Web]

## <a name="understanding-records"></a>Informazioni sui record DNS

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Configurare i siti Web per la modalità standard

[WACOM.INCLUDE [modes][modes]]

<a name="bkmk_configurecname"></a>

## Aggiunta di un record DNS per il dominio personalizzato

</p>
Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da Moniker. Per individuare gli strumenti DNS per Moniker.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso Moniker.com e selezionare **My Domains**,quindi fare clic su **Manage Templates**.

    ![Pagina My Domains per Moniker][Pagina My Domains per Moniker]

2.  Nella pagina **Zone Template Management** selezionare **Create New Template**.

    ![Zone Template Management in Moniker][Zone Template Management in Moniker]

3.  Compilare il campo **Template Name**.

4.  Creare quindi un record DNS selezionando innanzitutto **Record Type**. Compilare il campo **Hostname** e **Address**.

    ![Modello Zone di Moniker][Modello Zone di Moniker]

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **Hostname** sul sottodominio che si desidera usare, ad esempio **www**. È necessario impostare il campo **Address** sul nome di dominio **.trafficmanager.net** del profilo di Gestione traffico in uso con il sito Web di Azure, ad esempio **contoso.trafficmanager.net**.

        > [WACOM.NOTE] I record CNAME devono essere usati solo quando il nome di dominio personalizzato viene associato a un sito Web a cui è applicato il bilanciamento del carico tramite Gestione traffico.

5.  Fare clic sul pulsante **Add** per aggiungere la voce.

6.  Dopo aver completato l'aggiunta di tutte le voci, fare clic sul pulsante **Save**.

7.  Selezionare **Domain Manager** per tornare all'elenco dei domini.

8.  Selezionare la casella di controllo relativa al dominio di destinazione, quindi fare nuovamente clic su **Manage Templates**.

9.  Individuare il nuovo modello creato nei passaggi precedenti. Fare clic sul collegamento **place selected domains (1) into this Template**.

    ![Modello Zone di Moniker][2]

## <a name="enabledomain"></a>Abilitare il sito Web di Gestione traffico

[WACOM.INCLUDE [modes][3]]

  [Dominio personalizzato]: /it-it/documentation/articles/web-sites-custom-domain-name "Custom Domain"
  [GoDaddy]: /it-it/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /it-it/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /it-it/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /it-it/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /it-it/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /it-it/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Sito Web]: /it-it/documentation/articles/web-sites-moniker-custom-domain-name/ "Websites"
  [Sito Web mediante Gestione traffico]: /it-it/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "Website using Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [1]: https://moniker.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Informazioni sui record DNS]: #understanding-records
  [Configurazione dei siti Web per la modalità standard]: #bkmk_configsharedmode
  [Aggiunta di un record DNS per il dominio personalizzato]: #bkmk_configurecname
  [Abilitazione di Gestione traffico per il proprio sito Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Pagina My Domains per Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png
  [Zone Template Management in Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png
  [Modello Zone di Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png
  [2]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png
  [3]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
