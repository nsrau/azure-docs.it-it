<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure web site that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configurazione di un nome di dominio personalizzato per un sito Web di Azure utilizzando Gestione traffico (GoDaddy)
====================================================================================================================

[Dominio personalizzato](/en-us/documentation/articles/web-sites-custom-domain-name "Dominio personalizzato")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Sito Web](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name/ "Siti Web") | [Sito Web che utilizza Gestione traffico](/en-us/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Sito Web che utilizza Gestione traffico")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

In questo articolo vengono fornite istruzioni generiche sull'utilizzo di un nome di dominio personalizzato acquistato da [Go Daddy](https://godaddy.com) con Siti Web di Azure.

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

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato utilizzando gli strumenti forniti da GoDaddy. Per individuare gli strumenti DNS per GoDaddy.com, attenersi alla procedura seguente

1.  Accedere al proprio account presso GoDaddy.com, selezionare **My Account**, quindi **Manage your domains**. Infine selezionare il nome di dominio che si desidera utilizzare con il sito Web di Azure.

    ![pagina del domino personalizzato per GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2.  Nella pagina **Domain details** selezionare la scheda **DNS Zone File**. In questa sezione è possibile aggiungere e modificare i record DNS per il nome di dominio. Fare clic sul pulsante **Edit** per visualizzare l'editor **** dei file di zona.

    ![Scheda DNS Zone File](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

3.  L'editor dei file di zona **** è suddiviso in sezioni specifiche per ogni tipo di record, iniziando dai record A, indicati come **A (Host)** nella prima sezione, seguiti dai record CNAME, indicati come **CNAME (Alias)**. Per aggiungere una nuova voce, utilizzare il pulsante **Quick Add** sotto la sezione corrispondente. Per modificare una voce esistente, selezionarla e modificare le informazioni esistenti.

    ![editor dei file di zona](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

    > [WACOM.NOTE] Prima di aggiungere voci al file di zona, tenere presente che GoDaddy ha già creato i record DNS per i sottodomini più diffusi (denominati **Host** nell'editor), ad esempio **email**, **files**, **mail** e altri ancora. Se il nome che si desidera utilizzare è già presente, modificare il record esistente anziché crearne uno nuovo.

    Quando si aggiunge un record CNAME, è necessario impostare il campo **host** sul sottodominio che si desidera utilizzare, ad esempio **www**. È necessario impostare il campo **Points to** sul nome di dominio **.trafficmanager.net** del profilo di Gestione traffico in uso con il sito Web di Azure, ad esempio **contoso.trafficmanager.net**.

    > [WACOM.NOTE] I record CNAME devono essere utilizzati solo quando il nome di dominio personalizzato viene associato a un sito Web a cui è applicato il bilanciamento del carico tramite Gestione traffico.

4.  Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Save Zone File** per salvare le modifiche.

Abilitazione di Gestione traffico per il proprio sito Web
---------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

