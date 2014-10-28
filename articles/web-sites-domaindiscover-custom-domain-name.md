<properties title="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, DomainDiscover, TierraNet" description="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Configurazione di un nome di dominio personalizzato per un sito Web di Azure (DomainDiscover/TierraNetc)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web mediante Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

In questo articolo vengono fornite istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [DomainDiscover.com][DomainDiscover.com] con Siti Web di Azure. DomainDiscover.com fa parte di TierraNet.

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

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da DomainDiscover. Per individuare gli strumenti DNS per DomainDiscover.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso DomainDiscover.com (TierraNet) scegliendo **Control Panel** dal menu **Login**.

    ![Menu di accesso di DomainDiscover][Menu di accesso di DomainDiscover]

2.  Nella pagina **Domain Services** selezionare il dominio da usare per il sito Web di Azure.

    ![Pagina di gestione del dominio][Pagina di gestione del dominio]

3.  Nelle impostazioni del dominio fare clic sul pulsante **Edit** relativo a **DNS Service**.

    ![Pulsante per la modifica del DNS][Pulsante per la modifica del DNS]

4.  Nella finestra **Manage DNS** selezionare il tipo di record DNS da aggiungere all'elenco **Add Records**. Fare quindi clic su **Add**.

    ![Pulsante per la modifica del DNS][1]

5.  Nella pagine successiva immettere i valori per il record DNS. Fare quindi clic su **Add**.

    ![Pulsante per la modifica del DNS][2]

    -   Quando si aggiunge un record CNAME, è necessario innanzitutto selezionare **CNAME (Alias)** nella pagina **Manage DNS**. Impostare quindi il campo **Host** sul sottodominio che si desidera usare, ad esempio **www**. È necessario impostare il campo **Alias Hostname** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**. Specificare quindi un valore Time-to-Live (TTL), ad esempio 1800 secondi.

    -   Quando si aggiunge un record A, è necessario innanzitutto selezionare **A** nella pagina **Manage DNS**. Impostare quindi il campo **Host** su <**@**> (che rappresenta il nome di dominio radice, ad esempio **contoso.com**) o sul sottodominio da usare (ad esempio **www**). È necessario impostare il campo **IP Address** sull'indirizzo IP del sito Web di Azure. Specificare quindi un valore Time-to-Live (TTL), ad esempio 1800 secondi.

        > [WACOM.NOTE] Se si usa un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
        >
        > -   Campo **Host** impostato su **www** con campo **Alias Hostname** impostato su **\<nomesitoWeb\>.azurewebsites.net**.
        >
        > OPPURE
        >
        > -   Campo **Host** impostato su **awverify.www** con campo **Alias Hostname** impostato su **awverify.\<nomesitoWeb\>.azurewebsites.net**.
        >
        > Questo nome di record CNAME viene usato da Azure per convalidare la proprietà del dominio descritto dal record A.

6.  Se è stato aggiunto un record A, è possibile che venga visualizzato un avviso che indica che il record A per il dominio non è inattivo. Viene utilizzato il record modificato più di recente e DomainDirect dispone già di un record A per il nome di dominio radice. È possibile fare affidamento su questa precedenza oppure rimuovere il record A predefinito facendo clic sul pulsante **DELETE**.

## <a name="enabledomain"></a>Abilitare il nome del dominio nel sito Web

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
  [Sito Web]: /it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "Websites"
  [Sito Web mediante Gestione traffico]: /it-it/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "Website using Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [DomainDiscover.com]: https://domaindiscover.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Informazioni sui record DNS]: #understanding-records
  [Configurazione dei siti Web per la modalità di base, condivisa o standard]: #bkmk_configsharedmode
  [Aggiunta di un record DNS per il dominio personalizzato]: #bkmk_configurecname
  [Abilitazione del dominio nel sito Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Menu di accesso di DomainDiscover]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png
  [Pagina di gestione del dominio]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png
  [Pulsante per la modifica del DNS]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png
  [1]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png
  [2]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png
  [3]: ../includes/custom-dns-web-site-enable-on-web-site.md
