<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, DomainDiscover, TierraNet, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configurazione di un nome di dominio personalizzato per un sito Web di Azure utilizzando Gestione traffico (DomainDiscover/TierraNet)
=====================================================================================================================================

[Dominio personalizzato](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizzato")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sito Web](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "Siti Web") | [Sito Web che utilizza Gestione traffico](/en-us/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "Sito Web che utilizza Gestione traffico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato acquistato da [DomainDiscover.com](https://domaindiscover.com) con Siti Web di Azure. DomainDiscover.com fa parte di [TierraNet](https://www.tierra.net/).

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Configurazione dei siti Web per la modalità standard](#bkmk_configsharedmode)
-   [Aggiunta di un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitazione di Gestione traffico per il proprio sito Web](#enabledomain)

Informazioni sui record DNS
---------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

Configurazione dei siti Web per la modalità standard
----------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<h2>Aggiunta di un record DNS per il dominio personalizzato</h2>

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

    ![Pulsante per la modifica del DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png)

    -   Quando si aggiunge un record CNAME, è necessario innanzitutto selezionare **CNAME (Alias)** nella pagina **Manage DNS**. Impostare quindi il campo **Host** sul sottodominio che si desidera utilizzare, ad esempio **www**. È necessario impostare il campo **Alias Hostname** sul nome di dominio **.trafficmanager.net** del profilo di Gestione traffico in uso con il sito Web di Azure, ad esempio **contoso.trafficmanager.net**. Specificare quindi un valore Time-to-Live (TTL), ad esempio 1800 secondi.

        > [WACOM.NOTE] I record CNAME devono essere utilizzati solo quando il nome di dominio personalizzato viene associato a un sito Web a cui è applicato il bilanciamento del carico tramite Gestione traffico.

Abilitazione di Gestione traffico per il proprio sito Web
---------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

