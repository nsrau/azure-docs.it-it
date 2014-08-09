<properties title="Learn how to configure an Azure web site to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure web site" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configurazione di un nome di dominio personalizzato per un sito Web di Azure (eNom)
===================================================================================

[Dominio personalizzato](/it-it/documentation/articles/web-sites-custom-domain-name "Dominio personalizzato")[GoDaddy](/it-it/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/it-it/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/it-it/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/it-it/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/it-it/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sito Web](/it-it/documentation/articles/web-sites-enom-custom-domain-name/ "Siti Web") | [Sito Web che utilizza Gestione traffico](/it-it/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "Sito Web che utilizza Gestione traffico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato acquistato da [eNom](https://enom.com) con Siti Web di Azure.

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

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato utilizzando gli strumenti forniti da eNom. Per individuare gli strumenti DNS per enom.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso eNom e selezionare **Domains**, quindi **My Domains**. Verranno visualizzati i propri nomi di dominio.

2.  Nella pagina **My Domains** utilizzare il campo **Manage Domain** per selezionare **Host Records**. Verranno visualizzati i campi relativi ai record dell'host.

    ![Scheda DNS Zone File](./media/web-sites-custom-domain-name/e-hostrecords.png)

3.  L'editor Host Records consente di selezionare il tipo di record specifico utilizzando il campo **Record Type**. Per i siti Web di Azure utilizzare solo la selezione **CNAME (Alias)** o **A (Address)**.

    ![editor dei file di zona](./media/web-sites-custom-domain-name/e-editrecords.png)

    > [WACOM.NOTE] Prima di aggiungere voci al file di zona, tenere presente che eNom ha già creato i record DNS per il dominio radice ('@') e un carattere jolly per i sottodomini ('\*'). Se si desidera rindirizzare il dominio radice al proprio sito Web o utilizzare un record A con carattere jolly, è consigliabile modificare queste voci anziché crearne di nuove.

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **Host Name** sul sottodominio che si desidera utilizzare, ad esempio **www**. È necessario impostare il campo **Address** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**.

        > [WACOM.NOTE] Se si utilizza un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
        >
        > -   Campo **Alias** impostato su **www** con campo **Other host** impostato su **&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > OPPURE
        >
        > -   Campo **Alias** impostato su **awverify.ww** con campo **Other host** impostato su **awverify.&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > Questo nome di record CNAME viene utilizzato da Azure per convalidare la proprietà del dominio descritto dal record A.

    -   Quando si aggiunge un record A, è necessario impostare il campo **Host Name** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**), su \* (un carattere jolly per la corrispondenza di più sottodomini) o sul sottodominio da utilizzare, ad esempio **www**. È necessario impostare il campo **Address** sull'indirizzo IP del sito Web di Azure.

        > [WACOM.NOTE] Quando si aggiunge un record A è necessario anche aggiungere un record CNAME con host impostato su **awverify** e il campo **Points to** impostato su \*\*awverify.&lt;nomesitoWeb\>.azurewebsites.net.

4.  Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Save** per salvare le modifiche.

Abilitazione del nome del dominio nel sito Web
----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

