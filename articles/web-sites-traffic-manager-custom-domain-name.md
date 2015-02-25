<properties 
	pageTitle="Configurazione di un nome di dominio personalizzato per un sito Web di Azure che usa Gestione traffico" 
	description="" 
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

#Configurazione di un nome di dominio personalizzato per un sito Web di Azure che usa Gestione traffico

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-custom-domain-name/" title="Websites">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Sito Web che usa Gestione traffico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

In questo articolo vengono fornite istruzioni generiche sull'uso di un nome di dominio personalizzato con Siti Web di Azure in cui viene usato Gestione traffico per il bilanciamento del carico. Verificare nelle schede all'inizio dell'articolo se il registrar in uso è incluso nell'elenco. In caso affermativo, selezionare la scheda corrispondente per la procedura specifica di tale registrar.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Configurare i siti Web per la modalità standard](#bkmk_configsharedmode)
-   [Aggiungere un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitare Gestione traffico per il proprio sito Web](#enabledomain)

<h2><a name="understanding-records"></a>Informazioni sui record DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurare i siti Web per la modalità standard</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Aggiungere un record DNS per il dominio personalizzato</h2>

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti dal registrar da cui è stato acquistato il nome di dominio. Per individuare e usare gli strumenti DNS, seguire questa procedura.

1. Accedere all'account presso il registrar e cercare la pagina in cui gestire i record DNS. Individuare collegamenti o aree del sito denominate **Nome di dominio**, **DNS** o **Gestione server dei nomi**. Un collegamento a questa pagina è spesso disponibile nelle informazioni dell'account, cercando una voce simile a **My domains**.

4. Dopo aver trovato la pagina di gestione del nome di dominio, cercare un collegamento che consenta di modificare i record DNS. Tale collegamento può essere denominato **Zone file** o **DNS Records** oppure figurare come collegamento di configurazione in **Advanced**.

	* La pagina conterrà molto probabilmente alcuni record già creati, ad esempio una voce per associare '**@**' o '\*' a una pagina 'domain parking'. Può inoltre contenere record per sottodomini comuni, ad esempio **www**.
	* Nella pagina saranno presenti voci per **record CNAME** oppure verrà visualizzato un elenco a discesa per selezionare il tipo di record. È anche possibile che siano presenti voci per altri record, ad esempio **record A** e **record MX**. In alcuni casi, i record CNAME avranno una denominazione diversa, come ad esempio nel caso dei **record Alias**.
	* La pagina conterrà inoltre campi che consentono di eseguire il **mapping** da un **nome host** o **nome di dominio** a un altro nome di dominio.

5. Sebbene le specifiche di ogni registrar varino, in generale viene eseguito il mapping *from* nome di dominio personalizzato, ad esempio **contoso.com**, *to* nome di dominio di Gestione traffico (**contoso.trafficmanager.net**) usato per il sito Web di Azure.

6. Dopo aver completato l'aggiunta o la modifica di record DNS presso il registrar, salvare le modifiche.

<h2><a name="enabledomain"></a>Abilitare Gestione traffico per il proprio sito Web</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


<!--HONumber=42-->
