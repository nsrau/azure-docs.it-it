<properties title="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configurare un nome di dominio DomainDiscover per un sito Web di Azure" metaKeywords="Azure, Azure Web Sites, DomainDiscover, TierraNet" description="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Configurazione di un nome di dominio personalizzato per un sito Web di Azure (DomainDiscover/TierraNetc)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web che usa Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Questo articolo fornisce istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [eNom](https://domaindiscover.com) con Siti Web di Azure. DomainDiscover.com fa parte di TierraNet.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Configurare siti Web per la modalità di base, condivisa o standard](#bkmk_configsharedmode)
-   [Aggiungere un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitare il dominio nel sito Web](#enabledomain)

<h2><a name="understanding-records"></a>Informazioni sui record DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurare siti Web per la modalità di base, condivisa o standard</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Aggiungere un record DNS per il dominio personalizzato</h2>

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da DomainDiscover. Per individuare gli strumenti DNS per DomainDiscover.com, attenersi alla procedura seguente

1. Accedere al proprio account presso DomainDiscover.com (TierraNet) scegliendo **Control Panel** dal menu **Login**.

    ![DomainDiscover Login Menu](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. Nella pagina **Domain Services** selezionare il dominio da usare per il sito Web di Azure.

    ![Domain management page](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. Nelle impostazioni del dominio fare clic sul pulsante **Edit** relativo a **DNS Service**.

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. Nella finestra **Manage DNS** selezionare il tipo di record DNS da aggiungere nell'elenco **Add Records**. Fare quindi clic su **Add**.

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. Nella pagine successiva immettere i valori per il record DNS. Fare quindi clic su **Add**.

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png)

    * Quando si aggiunge un record CNAME, è necessario innanzitutto selezionare **CNAME (Alias)** nella pagina **Manage DNS**. Impostare quindi il campo **Host** sul sottodominio che si desidera usare, ad esempio **www**. È necessario impostare il campo **Alias Hostname** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**. Specificare quindi un valore Time-to-Live (TTL), ad esempio 1800 secondi.

    * Quando si aggiunge un record A, è necessario innanzitutto selezionare **A** nella pagina **Manage DNS**. Impostare quindi il campo **Host** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**) o sul sottodominio da usare, ad esempio **www**. È necessario impostare il campo **IP Address** sull'indirizzo IP del sito Web di Azure. Specificare quindi un valore Time-to-Live (TTL), ad esempio 1800 secondi.

		> [WACOM.NOTE] Se si usa un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
		> 
		> * Valore **Host** impostato su **www** con valore **Alias Hostname** impostato su **&lt;nomesitoWeb&gt;.azurewebsites.net**.
		> 
		> OPPURE
		> 
		> * Valore **Host** impostato su **awverify.www** con valore **Alias Hostname** impostato su **awverify.&lt;nomesitoWeb&gt;.azurewebsites.net**.
		> 
		> Questo nome di record CNAME viene usato da Azure per convalidare la proprietà del dominio descritto dal record A.


5. Se è stato aggiunto un record A, è possibile che venga visualizzato un avviso che indica che il record A per il dominio non è inattivo. Viene usato il record modificato più di recente e DomainDirect dispone già di un record A per il nome di dominio radice. È possibile fare affidamento su questa precedenza oppure rimuovere il record A predefinito facendo clic sul pulsante **DELETE**.

<h2><a name="enabledomain"></a>Abilitare il nome del dominio nel sito Web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
