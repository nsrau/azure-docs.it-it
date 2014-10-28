<properties title="Learn how to configure an Azure website to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="Learn how to configure an Azure website to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Configurazione di un nome di dominio personalizzato per un sito Web di Azure (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web mediante Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

In questo articolo vengono fornite istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [Moniker.com][Moniker.com] con Siti Web di Azure.

[WACOM.INCLUDE [introfooter][introfooter]]

Contenuto dell'articolo:

-   [Informazioni sui record DNS][Informazioni sui record DNS]
-   [Configurazione dei siti Web per la modalità di base, condivisa o standard][Configurazione dei siti Web per la modalità di base, condivisa o standard]
-   [Aggiunta di un record DNS per il dominio personalizzato][Aggiunta di un record DNS per il dominio personalizzato]
-   [Abilitazione del dominio nel sito Web][Abilitazione del dominio nel sito Web]

## <a name="understanding-records"></a>Informazioni sui record DNS

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Configurare siti Web per la modalità di base, condivisa o standard

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

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **Hostname** sul sottodominio che si desidera usare, ad esempio **www**. È necessario impostare il campo **Address** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**.

    -   Quando si aggiunge un record A, è necessario impostare il campo **Hostname** su <**@*>\* (che rappresenta il nome di dominio radice, ad esempio **contoso.com**) o sul sottodominio da usare (ad esempio **www**). È necessario impostare il campo **Address** sull'indirizzo IP del sito Web di Azure.

        > [WACOM.NOTE] Se si usa un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
        >
        > -   Campo **Hostname** impostato su **www** con campo **Address** impostato su **\<nomesitoWeb\>.azurewebsites.net**.
        >
        > OPPURE
        >
        > -   Campo **Hostname** impostato su **awverify.www** con campo **Address** impostato su **awverify.\<nomesitoWeb\>.azurewebsites.net**.
        >
        > Questo nome di record CNAME viene usato da Azure per convalidare la proprietà del dominio descritto dal record A.

5.  Fare clic sul pulsante **Add** per aggiungere la voce.

6.  Dopo aver completato l'aggiunta di tutte le voci, fare clic sul pulsante **Save**.

7.  Selezionare **Domain Manager** per tornare all'elenco dei domini.

8.  Selezionare la casella di controllo relativa al dominio di destinazione, quindi fare nuovamente clic su **Manage Templates**.

9.  Individuare il nuovo modello creato nei passaggi precedenti. Fare clic sul collegamento **place selected domains (1) into this Template**.

    ![Modello Zone di Moniker][1]

## <a name="enabledomain"></a>Abilitare il nome del dominio nel sito Web

[WACOM.INCLUDE [modes][2]]

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
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Moniker.com]: https://moniker.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Informazioni sui record DNS]: #understanding-records
  [Configurazione dei siti Web per la modalità di base, condivisa o standard]: #bkmk_configsharedmode
  [Aggiunta di un record DNS per il dominio personalizzato]: #bkmk_configurecname
  [Abilitazione del dominio nel sito Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Pagina My Domains per Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png
  [Zone Template Management in Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png
  [Modello Zone di Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png
  [1]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md
