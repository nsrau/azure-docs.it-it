<properties title="Learn how to configure an Azure web site to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configurazione di un nome di dominio personalizzato per un sito Web di Azure (Network Solutions)
================================================================================================

[Dominio personalizzato](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizzato")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sito Web](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name/ "Siti Web") | [Sito Web che utilizza Gestione traffico](/en-us/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/ "Sito Web che utilizza Gestione traffico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato acquistato da [Network Solutions](https://networksolutions.com) con Siti Web di Azure.

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

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato utilizzando gli strumenti forniti da Network Solutions. Per individuare e utilizzare gli strumenti DNS per networksolutions.com, attenersi alla procedura seguente.

1.  Accedere al proprio account presso networksolutions.com, quindi selezionare **My Account** nell'angolo superiore destro.

2.  Dalla scheda **My Products and Services**, selezionare **Edit DNS**.

    ![Modifica della pagina DNS](./media/web-sites-custom-domain-name/ns-editdns.png)

3.  Nella sezione \*\*Manage \*\* della pagina \*\*Domain Names\*\* selezionare \*\*Edit Advanced DNS Records\*\*.

    ![Pagina Domain Names con l'opzione Edit Advanced DNS Records evidenziata](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4.  La pagina **Update Advanced DNS** contiene una sezione per ogni tipo di record, con un pulsante **Edit** sotto ogni sezione.

    -   Per record A, utilizzare la sezione **IP Address (A Records)**.
    -   Per record CNAME, utilizzare la sezione **Host Alias (CNAME Records)**.

    ![Pagina Update Advanced DNS](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5.  Quando si fa clic sul pulsante **Edit**, verrà visualizzato un modulo da utilizzare per modificare i record esistenti o per aggiungerne di nuovi.

    > [WACOM.NOTE] Prima di aggiungere nuove voci, notare che Network Solutions ha già creato alcuni record DNS predefiniti per elementi come il dominio radice ('@') e un record jolly ('\*') per i sottodomini. Se il record che si desidera utilizzare è già presente, modificarlo anziché crearne uno nuovo.

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **Alias** sul sottodominio che si desidera utilizzare, ad esempio **www**. È necessario selezionare il pulsante di opzione accanto al campo **Other host**, quindi impostare il campo **Other host** sul nome di dominio del proprio sito Web seguito da **.azurewebsites.net**, ad esempio **contoso.azurwebsites.net**. Lasciare **Refers to Host Name** impostato su **Select**, poiché questo campo non è richiesto per la creazione di un record CNAME da utilizzare con Siti Web di Azure.

        ![Modulo CNAME](./media/web-sites-custom-domain-name/ns-cname.png)

        > [WACOM.NOTE] Se si utilizza un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
        >
        > -   Campo **Alias** impostato su **www** con campo **Other host** impostato su **&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > OPPURE
        >
        > -   Campo **Alias** impostato su **awverify.ww** con campo **Other host** impostato su **awverify.&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > Questo nome di record CNAME viene utilizzato da Azure per convalidare la proprietà del dominio descritto dal record A.

    -   Quando si aggiunge un record A, è necessario impostare il campo **Host** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**), su \* (un carattere jolly per la corrispondenza di più sottodomini) o sul sottodominio da utilizzare, ad esempio **www**. È necessario impostare il campo **Numeric IP** sull'indirizzo IP del sito Web di Azure.

        ![Modulo record A](./media/web-sites-custom-domain-name/ns-arecord.png)

6.  Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Continue** per rivedere le modifiche. Selezionare **Save changes only** per salvare le modifiche.

Abilitazione del nome del dominio nel sito Web
----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

