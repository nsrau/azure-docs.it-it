<properties 
	pageTitle="Configurazione di un nome di dominio Directnic per un sito Web di Azure" 
	description="Informazioni su come configurare un sito Web di Azure per l'uso di un nome di dominio registrato con Directnic" 
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
	ms.author="larryfr,jroth"/>

#Configurazione di un nome di dominio personalizzato per un sito Web di Azure (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name/" title="Websites" class="current">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sito Web che usa Gestione traffico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]


[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Questo articolo fornisce istruzioni sull'uso di un nome di dominio personalizzato acquistato da [DirectNic.com](https://directnic.com) con Siti Web di Azure.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Configurare siti Web per la modalità di base, condivisa o standard](#bkmk_configsharedmode)
-   [Aggiungere un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitare il dominio nel sito Web](#enabledomain)

<h2><a name="understanding-records"></a>Informazioni sui record DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurare i siti Web per la modalità di base, condivisa o standard</h2>

[AZURE.INCLUDE [modalità](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Aggiungere un record DNS per il dominio personalizzato</h2>

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da Directnic. Per individuare gli strumenti DNS per Directnic.com, attenersi alla procedura seguente

1. Accedere al proprio account presso Directnic.com e selezionare **My Services**, quindi **Domains**.

    ![Menu dei servizi di Directnic](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. Fare clic sul nome di dominio da usare con il sito Web di Azure.

2. Nella pagina di gestione del dominio fare clic sul pulsante **Manage** relativo a **DNS** nel riquadro **Services**.

    ![Riquadro Services](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. Aggiungere i record DNS compilando i campi **Type**, **Name** e **Data**. Al termine, fare clic sul pulsante **Add Record**.   

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    * Quando si aggiunge un record CNAME, è necessario impostare il campo **Name** sul sottodominio che si desidera usare, ad esempio **www**. È necessario impostare il campo **Data** sul nome di dominio **.azurewebsites.net** del sito Web di Azure, ad esempio **contoso.azurwebsites.net**.

    * Quando si aggiunge un record A, è necessario impostare il campo **Name** su **@** (che rappresenta il nome di dominio radice, ad esempio **contoso.com**) o sul sottodominio da usare, ad esempio **www**. È necessario impostare il campo **Data** sull'indirizzo IP del sito Web di Azure.

		> [AZURE.NOTE] Se si usa un record A, è inoltre necessario aggiungere un record CNAME con una delle seguenti configurazioni:
		> 
		> * Un valore **Name** impostato su **www** con un valore **Data** impostato su **&lt;nomesitoWeb&gt;.azurewebsites.net**.
		> 
		> OPPURE
		> 
		> * Un valore **Name** impostato su **awverify.www** con un valore **Data** impostato su **awverify.&lt;nomesitoWeb&gt;.azurewebsites.net**.
		> 
		> Questo nome di record CNAME viene usato da Azure per convalidare la proprietà del dominio descritto dal record A.


<h2><a name="enabledomain"></a>Abilitare il nome del dominio nel sito Web</h2>

[AZURE.INCLUDE [modalità](../includes/custom-dns-web-site-enable-on-web-site.md)]


<!--HONumber=42-->
