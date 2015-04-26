<properties 
	pageTitle="Configurazione di un nome di dominio eNom per un sito Web di Azure" 
	description="Informazioni sull'uso di un nome di dominio da eNom con Siti Web di Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr, jroth"/>

#Configurazione di un nome di dominio personalizzato per un sito Web di Azure (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web che usa Gestione traffico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Questo articolo fornisce istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [eNom](https://enom.com) con siti Web di Azure.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Configurare i siti Web per la modalità di base, condivisa o standard](#bkmk_configsharedmode)
-   [Aggiungere un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitare il dominio nel sito Web](#enabledomain)

<h2><a name="understanding-records"></a>Informazioni sui record DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurare siti Web per la modalità di base, condivisa o standard</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Aggiungere un record DNS per il dominio personalizzato</h2>

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da eNom. Per individuare gli strumenti DNS per enom.com, attenersi alla procedura seguente

1. Accedere al proprio account presso eNom e selezionare **Domains**, quindi **My Domains**. Verranno visualizzati i propri nomi di dominio.

2. Nella pagina **My Domains** usare il campo **Manage Domain** per selezionare **Host Records**. Verranno visualizzati i campi relativi ai record dell'host.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. L'editor Host Records consente di selezionare il tipo di record specifico usando il campo **Record Type**. Per i siti Web di Azure usare solo la selezione **CNAME (Alias)** o **A (Address)**.

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecords.png)

	> [AZURE.NOTE] Prima di aggiungere voci al file di zona, tenere presente che eNom ha già creato i record DNS per il dominio radice ('@') e un carattere jolly per i sottodomini ('\*'). Per reindirizzare il dominio radice al proprio sito Web o usare un record A con carattere jolly, è consigliabile modificare queste voci anziché crearne di nuove.

	* Quando si aggiunge un record CNAME, è necessario impostare il campo **Host Name** sul sottodominio che si desidera usare, ad esempio **www**. È necessario impostare il campo **Address** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**.

		> [AZURE.NOTE] Se si usa un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
		> 
		> * Campo **Alias** impostato su **www** con campo **Other host** impostato su **&lt;nomesitoWeb&gt;.azurewebsites.net**.
		> 
		> OPPURE
		> 
		> * Campo **Alias** impostato su **awverify.www** con campo **Other host** impostato su **awverify.&lt;nomesitoWeb&gt;.azurewebsites.net**.
		> 
		> Questo nome di record CNAME viene usato da Azure per convalidare la proprietà del dominio descritto dal record A.

	* Quando si aggiunge un record A, è necessario impostare il campo **Host Name** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**,) su * (un carattere jolly per la corrispondenza di più sottodomini) o sul sottodominio da usare, ad esempio **www**.) È necessario impostare il campo **Address** sull'indirizzo IP del sito Web di Azure.

		> [AZURE.NOTE] Quando si aggiunge un record A è necessario anche aggiungere un record CNAME con host impostato su **awverify** e il campo **Points to** impostato su **awverify.&lt;yourwebsitename&gt;.azurewebsites.net.

5. Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Save** per salvare le modifiche.

<h2><a name="enabledomain"></a>Abilitare il nome del dominio nel sito Web</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]



<!--HONumber=42-->
