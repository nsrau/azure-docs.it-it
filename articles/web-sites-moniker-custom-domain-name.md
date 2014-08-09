<properties title="Learn how to configure an Azure web site to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="Learn how to configure an Azure web site to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configurazione di un nome di dominio personalizzato per un sito Web di Azure (Moniker)
======================================================================================

[Dominio personalizzato](/it-it/documentation/articles/web-sites-custom-domain-name "Dominio personalizzato")[GoDaddy](/it-it/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/it-it/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/it-it/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/it-it/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/it-it/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sito Web](/it-it/documentation/articles/web-sites-moniker-custom-domain-name/ "Siti Web") | [Sito Web che utilizza Gestione traffico](/it-it/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "Sito Web che utilizza Gestione traffico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato acquistato da [Moniker.com](https://moniker.com) con Siti Web di Azure.

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

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato utilizzando gli strumenti forniti da Moniker. Per individuare gli strumenti DNS per Moniker.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso Moniker.com e selezionare **My Domains**,quindi fare clic su **Manage Templates**.

    ![Pagina My Domains per Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2.  Nella pagina **Zone Template Management** selezionare **Create New Template**.

    ![Zone Template Management in Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3.  Compilare il campo **Template Name**.

4.  Creare quindi un record DNS selezionando innanzitutto **Record Type**. Compilare il campo **Hostname** e **Address**.

    ![Modello Zone di Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png)

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **Hostname** sul sottodominio che si desidera utilizzare, ad esempio **www**. È necessario impostare il campo **Address** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**.

    -   Quando si aggiunge un record A, è necessario impostare il campo **Hostname** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**) o sul sottodominio da utilizzare, ad esempio **www**. È necessario impostare il campo **Address** sull'indirizzo IP del sito Web di Azure.

        > [WACOM.NOTE] Se si utilizza un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
        >
        > -   Campo **Hostname** impostato su **www** con campo **Address** impostato su **&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > OPPURE
        >
        > -   Campo **Hostname** impostato su **awverify.www** con campo **Address** impostato su **awverify.&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > Questo nome di record CNAME viene utilizzato da Azure per convalidare la proprietà del dominio descritto dal record A.

5.  Fare clic sul pulsante **Add** per aggiungere la voce.

6.  Dopo aver completato l'aggiunta di tutte le voci, fare clic sul pulsante **Save**.

7.  Selezionare **Domain Manager** per tornare all'elenco dei domini.

8.  Selezionare la casella di controllo relativa al dominio di destinazione, quindi fare nuovamente clic su **Manage Templates**.

9.  Individuare il nuovo modello creato nei passaggi precedenti. Fare clic sul collegamento **place selected domains (1) into this Template**.

    ![Modello Zone di Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

Abilitazione del nome del dominio nel sito Web
----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

