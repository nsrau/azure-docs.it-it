<properties title="Learn how to configure an Azure web site to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, Directnic" description="Learn how to configure an Azure web site to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configurazione di un nome di dominio personalizzato per un sito Web di Azure (Directnic)
========================================================================================

[Dominio personalizzato](/it-it/documentation/articles/web-sites-custom-domain-name "Dominio personalizzato")[GoDaddy](/it-it/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/it-it/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/it-it/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/it-it/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/it-it/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sito Web](/it-it/documentation/articles/web-sites-directnic-custom-domain-name/ "Siti Web") | [Sito Web che utilizza Gestione traffico](/it-it/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/ "Sito Web che utilizza Gestione traffico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato acquistato da [DirectNic.com](https://directnic.com) con Siti Web di Azure.

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

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato utilizzando gli strumenti forniti da Directnic. Per individuare gli strumenti DNS per Directnic.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso Directnic.com e selezionare **My Services**, quindi **Domains**.

    ![Menu dei servizi di Directnic](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2.  Fare clic sul nome di dominio che si desidera utilizzare con il sito Web di Azure.

3.  Nella pagina di gestione del dominio fare clic sul pulsante **Manage** relativo a **DNS** nel riquadro **Services**.

    ![Riquadro dei servizi](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4.  Aggiungere i record DNS compilando i campi **Type**, **Name** e **Data**. Al termine, fare clic sul pulsante **Add Record**.

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **Name** sul sottodominio che si desidera utilizzare, ad esempio **www**. È necessario impostare il campo **Data** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**.

    -   Quando si aggiunge un record A, è necessario impostare il campo **Name** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**) o sul sottodominio da utilizzare, ad esempio **www**. È necessario impostare il campo **Data** sull'indirizzo IP del sito Web di Azure.

        > [WACOM.NOTE] Se si utilizza un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
        >
        > -   Valore **Name** impostato su **www** con campo **Data** impostato su **&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > OPPURE
        >
        > -   Valore **Name** impostato su **awverify.www** con campo **Data** impostato su **awverify.&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > Questo nome di record CNAME viene utilizzato da Azure per convalidare la proprietà del dominio descritto dal record A.

Abilitazione del nome del dominio nel sito Web
----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

