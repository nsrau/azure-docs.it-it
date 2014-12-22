<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with GoDaddy" pageTitle="Configurazione di un nome di dominio personalizzato per un sito Web di Azure usando Gestione traffico" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configurazione di un nome di dominio personalizzato per un sito Web di Azure usando Gestione traffico (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Sito Web che usa Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

In questo articolo vengono fornite istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [Go Daddy](https://godaddy.com) con Siti Web di Azure.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Configurare i siti Web per la modalità standard](#bkmk_configsharedmode)
-   [Aggiungere un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitare Gestione traffico per il proprio sito Web](#enabledomain)

<h2><a name="understanding-records"></a>Informazioni sui record DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurare i siti Web per la modalità standard</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Aggiungere un record DNS per il dominio personalizzato</h2>

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da GoDaddy. Per individuare gli strumenti DNS per GoDaddy.com, attenersi alla procedura seguente

1. Accedere al proprio account presso GoDaddy.com e selezionare **Mio account**, quindi **Gestisci i miei domini**. Infine selezionare il nome di dominio che si desidera usare con il sito Web di Azure.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. Nella pagina **Domain details**selezionare la scheda **DNS Zone File**. In questa sezione è possibile aggiungere e modificare i record DNS per il nome di dominio. Fare clic sul pulsante **Edit** per visualizzare l'**editor dei file di zona**.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

4. L'**editor dei file di zona** è suddiviso in sezioni specifiche per ogni tipo di record, iniziando dai record A, indicati come **A (Host)** nella prima sezione, seguiti dai record CNAME, indicati come **CNAME (Alias)**. Per aggiungere una nuova voce, usare il pulsante **Quick Add** sotto la sezione corrispondente. Per modificare una voce esistente, selezionarla e modificare le informazioni esistenti.

	![zone file editor](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

	> [WACOM.NOTE] Prima di aggiungere nuovi record, tenere presente che GoDaddy ha già creato i record DNS per i sottodomini più diffusi (denominati **Host** nell'editor,) ad esempio **email**, **files**, **mail** e altri ancora. Se il nome che si desidera usare è già presente, modificare il record esistente anziché crearne uno nuovo.

	Quando si aggiunge un record CNAME, è necessario impostare il campo **Host** sul sottodominio che si vuole usare, ad esempio **www**. È necessario impostare il campo **Points to** sul nome di dominio **.trafficmanager.net** del profilo di Gestione traffico in uso con il sito Web di Azure, ad esempio **contoso.trafficmanager.net**.

	> [WACOM.NOTE] I record CNAME devono essere usati solo quando il nome di dominio personalizzato viene associato a un sito Web a cui è applicato il bilanciamento del carico tramite Gestione traffico.

5. Dopo avere completato l'aggiunta o la modifica dei record, fare clic su **Salva file di zona** per salvare le modifiche.

<h2><a name="enabledomain"></a>Abilitare il sito Web di Gestione traffico</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
