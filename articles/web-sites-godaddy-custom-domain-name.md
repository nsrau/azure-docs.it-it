<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configurazione di un nome di dominio personalizzato per un sito Web di Azure (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web mediante Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

In questo articolo vengono fornite istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [Go Daddy][Go Daddy] con Siti Web di Azure.

[WACOM.INCLUDE [introfooter][introfooter]]

Contenuto dell'articolo:

-   [Informazioni sui record DNS][Informazioni sui record DNS]
-   [Aggiunta di un record DNS per il dominio personalizzato][Aggiunta di un record DNS per il dominio personalizzato]
-   [Abilitazione del dominio nel sito Web][Abilitazione del dominio nel sito Web]

## <a name="understanding-records"></a>Informazioni sui record DNS

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configurecname"></a>Aggiunta di un record DNS per il dominio personalizzato

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da GoDaddy. Per individuare gli strumenti DNS per GoDaddy.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso GoDaddy.com e selezionare **Mio account**, quindi **Gestisci i miei domini**. Infine selezionare il menu a discesa relativo al nome di dominio da usare con il sito Web di Azure e selezionare **Gestisci DNS**.

    ![pagina del domino personalizzato per GoDaddy][pagina del domino personalizzato per GoDaddy]

2.  Nella pagina **Dettagli dominio** selezionare la scheda **DNS Zone File**. In questa sezione è possibile aggiungere e modificare i record DNS per il nome di dominio.

    ![Scheda DNS Zone File][Scheda DNS Zone File]

    Selezionare **Aggiungi record** a un record esistente.

    Per **modificare** un record esistente, selezionare l'icona della penna e del foglio accanto al record.

    > [WACOM.NOTE] Prima di aggiungere nuovi record, tenere presente che GoDaddy ha già creato i record DNS per i sottodomini più diffusi (denominati **Host** nell'editor), ad esempio **email**, **files**, **mail** e altri ancora. Se il nome che si desidera usare è già presente, modificare il record esistente anziché crearne uno nuovo.

3.  Per aggiungere un record, è necessario innanzitutto selezionare un tipo di record.

    ![select record type][select record type]

    Quindi, è necessario specificare l'**Host** (il dominio o il sottodominio personalizzato) e l'opzione **Punta a**.

    ![add zone record][add zone record]

    -   Quando si aggiunge un **record A (host)**, è necessario impostare il campo **Host** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**), su * (un carattere jolly per la corrispondenza di più sottodomini) o sul sottodominio da usare (ad esempio **www**). È necessario impostare il campo **Punta a** sull'indirizzo IP del sito Web di Azure.

        > [WACOM.NOTE] Quando si usa un record A (host), è inoltre necessario aggiungere un record CNAME con la seguente configurazione:
        >
        > -   Un valore **Host** di **awverify** con campo **Punta a** impostato su un valore di **awverify.\<nomesitoWeb\>.azurewebsites.net**.
        >
        > Questo nome di record CNAME viene usato da Azure per convalidare la proprietà del dominio descritto dal record A.

    -   Quando si aggiunge un **record CNAME (alias)**, è necessario impostare il campo **Host** sul sottodominio da usare, ad esempio **www**. È necessario impostare il campo **Punta a** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**.

4.  Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Fine** per salvare le modifiche.

## <a name="enabledomain"></a>Abilitare il nome del dominio nel sito Web

[WACOM.INCLUDE [modes][modes]]

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
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Go Daddy]: https://godaddy.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Informazioni sui record DNS]: #understanding-records
  [Aggiunta di un record DNS per il dominio personalizzato]: #bkmk_configurecname
  [Abilitazione del dominio nel sito Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [pagina del domino personalizzato per GoDaddy]: ./media/web-sites-custom-domain-name/godaddy-customdomain.png
  [Scheda DNS Zone File]: ./media/web-sites-custom-domain-name/godaddy-zonetab.png
  [select record type]: ./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png
  [add zone record]: ./media/web-sites-custom-domain-name/godaddy-addzonerecord.png
  [modes]: ../includes/custom-dns-web-site-enable-on-web-site.md
