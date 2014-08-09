<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Dotster, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configurazione di un nome di dominio personalizzato per un sito Web di Azure utilizzando Gestione traffico (Dotster)
====================================================================================================================

[Dominio personalizzato](/it-it/documentation/articles/web-sites-custom-domain-name "Dominio personalizzato")[GoDaddy](/it-it/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/it-it/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/it-it/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/it-it/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/it-it/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")
[Sito Web](/it-it/documentation/articles/web-sites-dotster-custom-domain-name/ "Siti Web") | [Sito Web che utilizza Gestione traffico](/it-it/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/ "Sito Web che utilizza Gestione traffico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato acquistato da [Dotster.com](https://dotster.com) con Siti Web di Azure.

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

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato utilizzando gli strumenti forniti da Dotster. Per individuare gli strumenti DNS per Dotster.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso Dotster.com. Scegliere **DomainCentral** dal menu **Domain**.

    ![Menu Domain Central di Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2.  Selezionare il proprio dominio per visualizzare un elenco di impostazioni. Selezionare il collegamento **Nameservers**.

    ![Opzioni di configurazione del dominio di Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3.  Selezionare **Use different name servers**. Per poter utilizzare i servizi DNS su Dotster, è necessario specificare i server dei nomi seguenti: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com e ns4.nameresolve.com.

    ![Opzioni di configurazione del dominio di Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [WACOM.NOTE] Possono essere necessarie dalle 24 alle 48 ore perché la modifica ai server dei nomi venga applicata. La parte restante della procedura descritta in questo articolo deve essere eseguita dopo l'applicazione della modifica.

4.  In DomainCentral selezionare il proprio dominio e quindi fare clic su **DNS**. Nell'elenco **Modify** selezionare il tipo di record DNS da aggiungere (**CNAME Alias** o **A Record**).

    ![Opzioni di configurazione del dominio di Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5.  Specificare quindi i valori dei campi **Host** e **Points To** per il record. Al termine, fare clic su **Add**.

    ![Opzioni di configurazione del dominio di Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png)

    -   Quando si aggiunge un record CNAME, è necessario impostare il campo **Host** sul sottodominio che si desidera utilizzare, ad esempio **www**. È necessario impostare il campo **Points To** sul nome di dominio **.trafficmanager.net** del profilo di Gestione traffico in uso con il sito Web di Azure, ad esempio **contoso.trafficmanager.net**.

        > [WACOM.NOTE] I record CNAME devono essere utilizzati solo quando il nome di dominio personalizzato viene associato a un sito Web a cui è applicato il bilanciamento del carico tramite Gestione traffico.

Abilitazione di Gestione traffico per il proprio sito Web
---------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

