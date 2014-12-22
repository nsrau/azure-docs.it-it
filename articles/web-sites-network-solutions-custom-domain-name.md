<properties title="Learn how to configure an Azure website to use a domain name registered with Network Solutions" pageTitle="Configurare un nome di dominio Network Solutions per un sito Web di Azure" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configurazione di un nome di dominio personalizzato per un sito Web di Azure (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web che usa Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Questo articolo fornisce istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [Network Solutions](https://www.networksolutions.com) con Siti Web di Azure.

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

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da Network Solutions. Per individuare e usare gli strumenti DNS per networksolutions.com, attenersi alla procedura seguente.

1. Accedere al proprio account presso networksolutions.com, quindi selezionare **My Account** nell'angolo superiore destro.

3. Dalla scheda **My Products and Services**, selezionare **Edit DNS**.

	![edit dns page](./media/web-sites-custom-domain-name/ns-editdns.png)

2. Nella sezione **Manage <nomedominio>** della pagina **Domain Names** selezionare **Edit Advanced DNS Records**.

	![domain names page with edit advanced dns records highlighted](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. La pagina **Update Advanced DNS** contiene una sezione per ogni tipo di record, con un pulsante **Edit** sotto ogni sezione.
	
	* Per record A, usare la sezione I**IP Address (A Records)**.
	* Per record CNAME, usare la sezione **Host Alias (CNAME Records)**.

	![update advanced dns page](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5. Quando si fa clic sul pulsante **Edit**, verrà visualizzato un modulo da usare per modificare i record esistenti o per aggiungerne di nuovi. 

	> [WACOM.NOTE] Prima di aggiungere nuove voci, notare che Network Solutions ha già creato alcuni record DNS predefiniti per elementi come il dominio radice ('@') e un record jolly ('*') per i sottodomini. Se il record che si desidera usare è già presente, modificarlo anziché crearne uno nuovo.

	* Quando si aggiunge un record CNAME, è necessario impostare il campo **Hostname** sul sottodominio che si vuole usare, ad esempio **www**. È necessario selezionare il pulsante di opzione accanto al campo **Other host**, quindi impostare il campo **Other host** sul nome di dominio del proprio sito Web seguito da **.azurewebsites.net**, ad esempio **contoso.azurwebsites.net**. Lasciare **Refers to Host Name** impostato su **Select**, poiché questo campo non è richiesto per la creazione di un record CNAME da usare con Siti Web di Azure.
	
		![cname form](./media/web-sites-custom-domain-name/ns-cname.png)

		> [WACOM.NOTE] Se si usa un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
		> 
		> * Valore **Alias** impostato su **www** con valore **Other host** impostato su **&lt;nomesitoWeb&gt;.azurewebsites.net**.
		> 
		OPPURE
		> 
		> * Valore **Alias** impostato su **awverify.www** con valore **Other host** impostato su **awverify.&lt;nomesitoWeb&gt;.azurewebsites.net**.
		> 
		Questo nome di record CNAME viene usato da Azure per convalidare la proprietà del dominio descritto dal record A.

	* Quando si aggiunge un record A, è necessario impostare il campo **Host** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**), su * (un carattere jolly per la corrispondenza di più sottodomini) o sul sottodominio da usare, ad esempio **www**. È necessario impostare il campo **Numeric IP** sull'indirizzo IP del sito Web di Azure.

		![a record form](./media/web-sites-custom-domain-name/ns-arecord.png)

5. Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Continue** per rivedere le modifiche. Selezionare **Save changes only** per salvare le modifiche.

<h2><a name="enabledomain"></a>Abilitare il nome del dominio nel sito Web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
