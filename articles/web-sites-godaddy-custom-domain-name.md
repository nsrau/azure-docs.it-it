<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Configurare un nome di dominio GoDaddy per un sito Web di Azure" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configurazione di un nome di dominio personalizzato per un sito Web di Azure (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web che usa Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

In questo articolo vengono fornite istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [Go Daddy](https://godaddy.com) con Siti Web di Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Aggiungere un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitare il dominio nel sito Web](#enabledomain)

<h2><a name="understanding-records"></a>Informazioni sui record DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>Aggiungere un record DNS per il dominio personalizzato</h2>

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da GoDaddy. Per individuare gli strumenti DNS per GoDaddy.com, attenersi alla procedura seguente

1. Accedere al proprio account presso GoDaddy.com e selezionare **Mio account**, quindi **Gestisci i miei domini**. Infine selezionare il menu a discesa relativo al nome di dominio da usare con il sito Web di Azure e selezionare **Gestisci DNS**.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. Nella pagina **Dettagli dominio** selezionare la scheda **DNS Zone File**. In questa sezione è possibile aggiungere e modificare i record DNS per il nome di dominio. 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	Selezionare **Aggiungi record** a un record esistente.

	Per **modificare** un record esistente, selezionare l'icona della penna e del foglio accanto al record.

	> [WACOM.NOTE] Prima di aggiungere nuovi record, tenere presente che GoDaddy ha già creato i record DNS per i sottodomini più diffusi (denominati **Host** nell'editor), ad esempio **email**, **files**, **mail** e altri ancora. Se il nome che si desidera usare è già presente, modificare il record esistente anziché crearne uno nuovo.

4. Per aggiungere un record, è necessario innanzitutto selezionare un tipo di record.

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	Quindi, è necessario specificare l'**Host** (il dominio o il sottodominio personalizzato) e l'opzione **Points to**.

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* Quando si aggiunge un **record A (host)**, è necessario impostare il campo **Host** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**), su * (un carattere jolly per la corrispondenza di più sottodomini) o sul sottodominio da usare (ad esempio **www**). È necessario impostare il campo **Points to** sull'indirizzo IP del sito Web di Azure.
	
		> [WACOM.NOTE] Quando si usa un record A (host), è anche necessario aggiungere un record CNAME con la configurazione seguente:
		> 
		> * Valore **Host** impostato su **awverify** con valore **Points to** impostato su **awverify.&lt;nomesitoWeb&gt;.azurewebsites.net**.
		> 
		> Questo nome di record CNAME viene usato da Azure per convalidare la proprietà del dominio descritto dal record A.

	* Quando si aggiunge un **record CNAME (alias)**, è necessario impostare il campo **Host** sul sottodominio da usare, ad esempio **www**. È necessario impostare il campo **Points to** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**.


5. Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Fine** per salvare le modifiche.

<h2><a name="enabledomain"></a>Abilitare il nome del dominio nel sito Web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

