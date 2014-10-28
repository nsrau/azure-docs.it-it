<properties title="Learn how to configure an Azure website to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configurazione di un nome di dominio personalizzato per un sito Web di Azure (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web mediante Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

Questo articolo fornisce istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [Network Solutions][1] con Siti Web di Azure.

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
Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da Network Solutions. Per individuare e usare gli strumenti DNS per networksolutions.com, attenersi alla procedura seguente.

1.  Accedere al proprio account presso networksolutions.com, quindi selezionare **My Account** nell'angolo superiore destro.

2.  Dalla scheda **My Products and Services**, selezionare **Edit DNS**.

    ![Modifica della pagina DNS][Modifica della pagina DNS]

3.  Nella sezione **Manage <yourdomainname>** della pagina **Domain Names** selezionare **Edit Advanced DNS Records**.

    ![Pagina Domain Names con l'opzione Edit Advanced DNS Records evidenziata][Pagina Domain Names con l'opzione Edit Advanced DNS Records evidenziata]

4.  La pagina **Update Advanced DNS** contiene una sezione per ogni tipo di record, con un pulsante **Edit** sotto ogni sezione.

    -   Per record A, usare la sezione **IP Address (A Records)**.
    -   Per record CNAME, usare la sezione **Host Alias (CNAME Records)**.

    ![Pagina Update Advanced DNS][Pagina Update Advanced DNS]

5.  Quando si fa clic sul pulsante **Edit**, verrà visualizzato un modulo da usare per modificare i record esistenti o per aggiungerne di nuovi.

    > [WACOM.NOTE] Prima di aggiungere nuove voci, notare che Network Solutions ha già creato alcuni record DNS predefiniti per elementi come il dominio radice (<'@'>) e un record jolly ('\*') per i sottodomini. Se il record che si desidera usare è già presente, modificarlo anziché crearne uno nuovo.

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **Alias** sul sottodominio che si desidera usare, ad esempio **www**. È necessario selezionare il pulsante di opzione accanto al campo **Other host**, quindi impostare il campo **Other host** sul nome di dominio del proprio sito Web seguito da **.azurewebsites.net**, ad esempio **contoso.azurwebsites.net**. Lasciare **Refers to Host Name** impostato su **Select**, poiché questo campo non è richiesto per la creazione di un record CNAME da usare con Siti Web di Azure.

        ![Modulo CNAME][Modulo CNAME]

        > [WACOM.NOTE] Se si usa un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
        >
        > -   Campo **Alias** impostato su **www** con campo **Other host** impostato su **\<nomesitoWeb\>.azurewebsites.net**.
        >
        > OPPURE
        >
        > -   Campo **Alias** impostato su **awverify.ww** con campo **Other host** impostato su **awverify.\<nomesitoWeb\>.azurewebsites.net**.
        >
        > Questo nome di record CNAME viene usato da Azure per convalidare la proprietà del dominio descritto dal record A.

    -   Quando si aggiunge un record A, è necessario impostare il campo **Host** su <**@*>\* (che rappresenta il nome di dominio radice, ad esempio **contoso.com**), su \* (un carattere jolly per la corrispondenza di più sottodomini) o sul sottodominio da usare, ad esempio **www**. È necessario impostare il campo **Numeric IP** sull'indirizzo IP del sito Web di Azure.

        ![Modulo record A][Modulo record A]

6.  Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Continue** per rivedere le modifiche. Selezionare **Save changes only** per salvare le modifiche.

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
  [Sito Web]: /it-it/documentation/articles/web-sites-network-solutions-custom-domain-name/ "Websites"
  [Sito Web mediante Gestione traffico]: /it-it/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/ "Website using Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [1]: https://www.networksolutions.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Informazioni sui record DNS]: #understanding-records
  [Configurazione dei siti Web per la modalità di base, condivisa o standard]: #bkmk_configsharedmode
  [Aggiunta di un record DNS per il dominio personalizzato]: #bkmk_configurecname
  [Abilitazione del dominio nel sito Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Modifica della pagina DNS]: ./media/web-sites-custom-domain-name/ns-editdns.png
  [Pagina Domain Names con l'opzione Edit Advanced DNS Records evidenziata]: ./media/web-sites-custom-domain-name/ns-editadvanced.png
  [Pagina Update Advanced DNS]: ./media/web-sites-custom-domain-name/ns-updateadvanced.png
  [Modulo CNAME]: ./media/web-sites-custom-domain-name/ns-cname.png
  [Modulo record A]: ./media/web-sites-custom-domain-name/ns-arecord.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md
