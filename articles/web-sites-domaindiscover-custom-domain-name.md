<properties title="Learn how to configure an Azure web site to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, DomainDiscover, TierraNet" description="Learn how to configure an Azure web site to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configurazione di un nome di dominio personalizzato per un sito Web di Azure (DomainDiscover/TierraNetc)
========================================================================================================

[Dominio personalizzato](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizzato")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sito Web](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "Siti Web") | [Sito Web che utilizza Gestione traffico](/en-us/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "Sito Web che utilizza Gestione traffico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato acquistato da [DomainDiscover.com](https://domaindiscover.com) con Siti Web di Azure. DomainDiscover.com fa parte di TierraNet.

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

<h2\>Aggiunta di un record DNS per il dominio personalizzato</h2>

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato utilizzando gli strumenti forniti da DomainDiscover. Per individuare gli strumenti DNS per DomainDiscover.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso DomainDiscover.com (TierraNet) scegliendo **Control Panel** dal menu **Login**.

    ![Menu di accesso di DomainDiscover](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2.  Nella pagina **Domain Services** selezionare il dominio che si desidera utilizzare per il sito Web di Azure.

    ![Pagina di gestione del dominio](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3.  Nelle impostazioni del dominio fare clic sul pulsante **Edit** relativo a **DNS Service**.

    ![Pulsante per la modifica del DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4.  Nella finestra **Manage DNS** selezionare il tipo di record DNS da aggiungere all'elenco **Add Records**. Fare quindi clic su **Add**.

    ![Pulsante per la modifica del DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5.  Nella pagine successiva immettere i valori per il record DNS. Fare quindi clic su **Add**.

    ![Pulsante per la modifica del DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png)

    -   Quando si aggiunge un record CNAME, è necessario innanzitutto selezionare **CNAME (Alias)** nella pagina **Manage DNS**. Impostare quindi il campo **Host** sul sottodominio che si desidera utilizzare, ad esempio **www**. È necessario impostare il campo **Alias Hostname** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**. Specificare quindi un valore Time-to-Live (TTL), ad esempio 1800 secondi.

    -   Quando si aggiunge un record A, è necessario innanzitutto selezionare **A** nella pagina **Manage DNS**. Impostare quindi il campo **Host** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**) o sul sottodominio da utilizzare, ad esempio **www**. È necessario impostare il campo **IP Address** sull'indirizzo IP del sito Web di Azure. Specificare quindi un valore Time-to-Live (TTL), ad esempio 1800 secondi.

        > [WACOM.NOTE] Se si utilizza un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
        >
        > -   Campo **Host** impostato su **www** con campo **Alias Hostname** impostato su **&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > OPPURE
        >
        > -   Campo **Host** impostato su **awverify.www** con campo **Alias Hostname** impostato su **awverify.&lt;nomesitoWeb\>.azurewebsites.net**.
        >
        > Questo nome di record CNAME viene utilizzato da Azure per convalidare la proprietà del dominio descritto dal record A.

6.  Se è stato aggiunto un record A, è possibile che venga visualizzato un avviso che indica che il record A per il dominio non è inattivo. Viene utilizzato il record modificato più di recente e DomainDirect dispone già di un record A per il nome di dominio radice. È possibile fare affidamento su questa precedenza oppure rimuovere il record A predefinito facendo clic sul pulsante **DELETE**.

Abilitazione del nome del dominio nel sito Web
----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

