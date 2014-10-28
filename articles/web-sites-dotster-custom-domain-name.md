<properties title="Learn how to configure an Azure website to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, Dotster" description="Learn how to configure an Azure website to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Configurazione di un nome di dominio personalizzato per un sito Web di Azure (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web mediante Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

In questo articolo vengono fornite istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [Dotster.com][Dotster.com] con Siti Web di Azure.

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
Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da Dotster. Per individuare gli strumenti DNS per Dotster.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso Dotster.com. Scegliere **DomainCentral** dal menu **Domain**.

    ![Menu Domain Central di Dotster][Menu Domain Central di Dotster]

2.  Selezionare il proprio dominio per visualizzare un elenco di impostazioni. Selezionare il collegamento **Nameservers**.

    ![Opzioni di configurazione del dominio di Dotster][Opzioni di configurazione del dominio di Dotster]

3.  Selezionare **Use different name servers**. Per poter usare i servizi DNS su Dotster, è necessario specificare i server dei nomi seguenti: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com e ns4.nameresolve.com.

    ![Opzioni di configurazione del dominio di Dotster][1]

    > [WACOM.NOTE] Possono essere necessarie dalle 24 alle 48 ore perché la modifica ai server dei nomi venga applicata. La parte restante della procedura descritta in questo articolo deve essere eseguita dopo l'applicazione della modifica.

4.  In DomainCentral selezionare il proprio dominio e quindi fare clic su **DNS**. Nell'elenco **Modify** selezionare il tipo di record DNS da aggiungere (**CNAME Alias** o **A Record**).

    ![Opzioni di configurazione del dominio di Dotster][2]

5.  Specificare quindi i valori dei campi **Host** e **Points To** per il record. Al termine, fare clic su **Add**.

    ![Opzioni di configurazione del dominio di Dotster][3]

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **Host** sul sottodominio che si desidera usare, ad esempio **www**. È necessario impostare il campo **Punta a** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**.

    -   Quando si aggiunge un record A, è necessario impostare il campo **Host** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**) o sul sottodominio da usare (ad esempio **www**.) È necessario impostare il campo **Punta a** sull'indirizzo IP del sito Web di Azure.

        > [WACOM.NOTE] Se si usa un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
        >
        > -   Campo **Host** impostato su **www** con campo **Points To** impostato su **\<nomesitoWeb\>.azurewebsites.net**.
        >
        > OPPURE
        >
        > -   Campo **Host** impostato su **awverify.www** con campo **Points To** impostato su **awverify.\<nomesitoWeb\>.azurewebsites.net**.
        >
        > Questo nome di record CNAME viene usato da Azure per convalidare la proprietà del dominio descritto dal record A.

## <a name="enabledomain"></a>Abilitare il nome del dominio nel sito Web

[WACOM.INCLUDE [modes][4]]

  [Dominio personalizzato]: /it-it/documentation/articles/web-sites-custom-domain-name "Custom Domain"
  [GoDaddy]: /it-it/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /it-it/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /it-it/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /it-it/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /it-it/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /it-it/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Sito Web]: /it-it/documentation/articles/web-sites-dotster-custom-domain-name/ "Websites"
  [Sito Web mediante Gestione traffico]: /it-it/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/ "Website using Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Dotster.com]: https://dotster.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Informazioni sui record DNS]: #understanding-records
  [Configurazione dei siti Web per la modalità di base, condivisa o standard]: #bkmk_configsharedmode
  [Aggiunta di un record DNS per il dominio personalizzato]: #bkmk_configurecname
  [Abilitazione del dominio nel sito Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Menu Domain Central di Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png
  [Opzioni di configurazione del dominio di Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png
  [1]: .\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png
  [2]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png
  [3]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png
  [4]: ../includes/custom-dns-web-site-enable-on-web-site.md
