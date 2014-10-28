<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure website that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configurazione di un nome di dominio personalizzato per un sito Web di Azure usando Traffic Manager (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Sito Web mediante Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

In questo articolo vengono fornite istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [Go Daddy][Go Daddy] con Siti Web di Azure.

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
Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da GoDaddy. Per individuare gli strumenti DNS per GoDaddy.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso GoDaddy.com, selezionare **My Account**, quindi **Manage your domains**. Infine selezionare il nome di dominio che si desidera usare con il sito Web di Azure.

    ![pagina del domino personalizzato per GoDaddy][pagina del domino personalizzato per GoDaddy]

2.  Nella pagina **Domain details** selezionare la scheda **DNS Zone File**. In questa sezione è possibile aggiungere e modificare i record DNS per il nome di dominio. Fare clic sul pulsante **Edit** per visualizzare **l'editor dei file di zona**.

    ![Scheda DNS Zone File][Scheda DNS Zone File]

3.  **L'editor dei file di zona** è suddiviso in sezioni specifiche per ogni tipo di record, iniziando dai record A, indicati come **A (Host)** nella prima sezione, seguiti dai record CNAME, indicati come **CNAME (Alias)**. Per aggiungere una nuova voce, usare il pulsante **Quick Add** sotto la sezione corrispondente. Per modificare una voce esistente, selezionarla e modificare le informazioni esistenti.

    ![editor dei file di zona][editor dei file di zona]

    > [WACOM.NOTE] Prima di aggiungere voci al file di zona, tenere presente che GoDaddy ha già creato i record DNS per i sottodomini più diffusi (denominati **Host** nell'editor), ad esempio **email**, **files**, **mail** e altri ancora. Se il nome che si desidera usare è già presente, modificare il record esistente anziché crearne uno nuovo.

    Quando si aggiunge un record CNAME, è necessario impostare il campo **host** sul sottodominio che si desidera usare, ad esempio **www**. È necessario impostare il campo **Points to** sul nome di dominio **.trafficmanager.net** del profilo di Gestione traffico in uso con il sito Web di Azure, ad esempio **contoso.trafficmanager.net**.

    > [WACOM.NOTE] I record CNAME devono essere usati solo quando il nome di dominio personalizzato viene associato a un sito Web a cui è applicato il bilanciamento del carico tramite Gestione traffico.

4.  Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Save Zone File** per salvare le modifiche.

## <a name="enabledomain"></a>Abilitare il sito Web di Gestione traffico

[WACOM.INCLUDE [modes][1]]

  [Dominio personalizzato]: /it-it/documentation/articles/web-sites-custom-domain-name "Custom Domain"
  [GoDaddy]: /it-it/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /it-it/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /it-it/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /it-it/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /it-it/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /it-it/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Sito Web]: /it-it/documentation/articles/web-sites-godaddy-custom-domain-name/ "Websites"
  [Sito Web mediante Gestione traffico]: /it-it/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Website using Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [Go Daddy]: https://godaddy.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Informazioni sui record DNS]: #understanding-records
  [Configurazione dei siti Web per la modalità standard]: #bkmk_configsharedmode
  [Aggiunta di un record DNS per il dominio personalizzato]: #bkmk_configurecname
  [Abilitazione di Gestione traffico per il proprio sito Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [pagina del domino personalizzato per GoDaddy]: ./media/web-sites-custom-domain-name/godaddy-customdomain.png
  [Scheda DNS Zone File]: ./media/web-sites-custom-domain-name/godaddy-zonetab.png
  [editor dei file di zona]: ./media/web-sites-custom-domain-name/godaddy-quickaddcname.png
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
