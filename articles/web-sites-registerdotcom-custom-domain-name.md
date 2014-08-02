<properties title="Learn how to configure an Azure web site to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configurazione di un nome di dominio personalizzato per un sito Web di Azure (Register.com)
===========================================================================================

[Dominio personalizzato](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizzato")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sito Web](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "Siti Web") | [Sito Web che utilizza Gestione traffico](/en-us/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "Sito Web che utilizza Gestione traffico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato acquistato da [Register.com](https://register.com) con Siti Web di Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Configurazione dei siti Web per la modalità di base, condivisa o standard](#bkmk_configsharedmode)
-   [Aggiunta di un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitazione del dominio nel sito Web](#enabledomain)

Informazioni sui record DNS
---------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

Configurazione dei siti Web per la modalità di base, condivisa o standard
-------------------------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<h2>Aggiunta di un record DNS per il dominio personalizzato</h2>

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato utilizzando gli strumenti forniti dal registrar da cui è stato acquistato il nome di dominio. Per individuare e utilizzare gli strumenti DNS, attenersi alla procedura seguente.

1.  Accedere al proprio account presso register.com e selezionare **Your Account** nell'angolo superiore destro per visualizzare i propri domini, quindi selezionare il nome di dominio personalizzato.

    ![pagina dell'account personale](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

2.  Scorre la pagina verso il basso fino a **Advanced Technical Settings**. I collegamenti in questa sezione consentono di gestire i record del dominio.

    -   Per i record A, utilizzare il collegamento **Edit IP Address Records**.
    -   Per i record A, utilizzare il collegamento **Edit Domain Aliases Records**.

    ![Impostazioni tecniche avanzate](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

3.  Quando si fa clic sul pulsante **Edit**, verrà visualizzato un modulo da utilizzare per modificare i record esistenti o per aggiungerne di nuovi. I moduli per i record CNAME e A sono simili.

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **.mydomainname.com** sul sottodominio che si desidera utilizzare, ad esempio **www**. È necessario impostare il campo **points to** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**. Lasciare **Refers to Host Name** impostato su **Select**, poiché questo campo non è richiesto per la creazione di un record CNAME da utilizzare con Siti Web di Azure.

        ![Modulo CNAME](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

        > [WACOM.NOTE] Se si utilizza un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
        >
        > -   Campo **Alias** impostato su **www** con campo **Other host** impostato su **&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > OPPURE
        >
        > -   Campo **Alias** impostato su **awverify.ww** con campo **Other host** impostato su **awverify.&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > Questo nome di record CNAME viene utilizzato da Azure per convalidare la proprietà del dominio descritto dal record A.

    -   Quando si aggiunge un record A, è necessario impostare il campo **.mydomainname.com** sul sottodominio che si desidera utilizzare, ad esempio **www**. Lasciare vuoto il campo per impostare il dominio radice oppure utilizzare **\*\* per creare un mapping con caratteri jolly. È necessario impostare il campo \*\*points to** sull'indirizzo IP del sito Web di Azure.

        ![Modulo record A](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

4.  Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Continue** per rivedere le modifiche. Selezionare nuovamente **Continue** per salvare le modifiche.

Abilitazione del nome del dominio nel sito Web
----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

