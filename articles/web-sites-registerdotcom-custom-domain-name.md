<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configurazione di un nome di dominio personalizzato per un sito Web di Azure (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web mediante Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

In questo articolo vengono fornite istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [Register.com][1] con Siti Web di Azure.

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
Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti dal registrar da cui è stato acquistato il nome di dominio. Per individuare e usare gli strumenti DNS, attenersi alla procedura seguente.

1.  Accedere al proprio account presso register.com e selezionare **Your Account** nell'angolo superiore destro per visualizzare i propri domini, quindi selezionare il nome di dominio personalizzato.

    ![pagina dell'account personale][pagina dell'account personale]

2.  Scorre la pagina verso il basso fino a **Advanced Technical Settings**. I collegamenti in questa sezione consentono di gestire i record del dominio.

    -   Per i record A, usare il collegamento **Edit IP Address Records**.
    -   Per i record A, usare il collegamento **Edit Domain Aliases Records**.

    ![Impostazioni tecniche avanzate][Impostazioni tecniche avanzate]

3.  Quando si fa clic sul pulsante **Edit**, verrà visualizzato un modulo da usare per modificare i record esistenti o per aggiungerne di nuovi. I moduli per i record CNAME e A sono simili.

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **.mydomainname.com** sul sottodominio che si desidera usare, ad esempio **www**. È necessario impostare il campo **points to** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**. Lasciare **Refers to Host Name** impostato su **Select**, poiché questo campo non è richiesto per la creazione di un record CNAME da usare con Siti Web di Azure.

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

    -   Quando si aggiunge un record A, è necessario impostare il campo **.mydomainname.com** sul sottodominio che si desidera usare, ad esempio **www**. Lasciare vuoto il campo per impostare il dominio radice oppure usare per creare un mapping con caratteri jolly. È necessario impostare il campo **points to** sull'indirizzo IP del sito Web di Azure.

        ![Modulo record A][Modulo record A]

4.  Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Continue** per rivedere le modifiche. Selezionare nuovamente **Continue** per salvare le modifiche.

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
  [Sito Web]: /it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "Websites"
  [Sito Web mediante Gestione traffico]: /it-it/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "Website using Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [1]: https://www.register.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Informazioni sui record DNS]: #understanding-records
  [Configurazione dei siti Web per la modalità di base, condivisa o standard]: #bkmk_configsharedmode
  [Aggiunta di un record DNS per il dominio personalizzato]: #bkmk_configurecname
  [Abilitazione del dominio nel sito Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [pagina dell'account personale]: ./media/web-sites-custom-domain-name/rdotcom-myaccount.png
  [Impostazioni tecniche avanzate]: ./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png
  [Modulo CNAME]: ./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png
  [Modulo record A]: ./media/web-sites-custom-domain-name/rdotcom-editarecord.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md
