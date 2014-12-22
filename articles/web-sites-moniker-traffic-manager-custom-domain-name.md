<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" pageTitle="Configurare un nome di dominio Moniker per un sito Web di Azure usando Gestione traffico" metaKeywords="Windows Azure, Windows Azure Websites, Moniker, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Configurazione di un nome di dominio personalizzato per un sito Web di Azure usando Gestione traffico (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizzato</a><a href="/it-it/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/it-it/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/it-it/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/it-it/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/it-it/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/it-it/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/it-it/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites">Sito Web</a> | <a href="/it-it/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Sito Web che usa Gestione traffico</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Questo articolo fornisce istruzioni generiche sull'uso di un nome di dominio personalizzato acquistato da [Moniker](https://moniker.com) con Siti Web di Azure.

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

Per associare il dominio personalizzato a un sito Web di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti da Moniker. Per individuare gli strumenti DNS per Moniker.com, attenersi alla procedura seguente

1. Accedere al proprio account presso Moniker.com e selezionare **My Domains**,quindi fare clic su **Manage Templates**.

    ![My Domains page for Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. Nella pagina **Zone Template Management** selezionare **Create New Template**.

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. Compilare il campo **Template Name**. 

5. Creare quindi un record DNS selezionando innanzitutto **Record Type**. Compilare il campo **Hostname** e **Address**. 

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png)

    * Quando si aggiunge un record CNAME, è necessario impostare il campo **Hostname** sul sottodominio che si vuole usare, ad esempio **www**. È necessario impostare il campo **Address** sul nome di dominio **.trafficmanager.net** del profilo di Gestione traffico usato con il sito Web di Azure, ad esempio **contoso.trafficmanager.net**.

	    > [WACOM.NOTE] I record CNAME devono essere usati solo quando il nome di dominio personalizzato viene associato a un sito Web a cui è applicato il bilanciamento del carico tramite Gestione traffico.
	 
7. Fare clic sul pulsante **Aggiungi** per aggiungere la voce.

8. Dopo aver completato l'aggiunta di tutte le voci, fare clic sul pulsante **Salva**.

5. Selezionare **Gestione dominio** per tornare all'elenco dei domini.

6. Selezionare la casella di controllo relativa al dominio di destinazione, quindi fare nuovamente clic su **Gestione modelli**.

7. Individuare il nuovo modello creato nei passaggi precedenti. Fare clic sul collegamento **place selected domains (1) into this Template**.

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>Abilitare il sito Web di Gestione traffico</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
