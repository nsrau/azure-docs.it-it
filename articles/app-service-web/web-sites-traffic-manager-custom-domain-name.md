<properties 
	pageTitle="Configurare un nome di dominio personalizzato per un'app Web nel servizio app di Azure che usa Gestione traffico" 
	description="Usare un nome di dominio personalizzato per un'app Web nel servizio app di Azure che usa Gestione traffico per il bilanciamento del carico." 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2015" 
	ms.author="mwasson"/>

#Configurazione di un nome di dominio personalizzato per un'app Web nel servizio app di Azure con Gestione traffico

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Questo articolo fornisce istruzioni generiche sull'uso di un nome di dominio personalizzato con il servizio app di Azure in cui viene usato Gestione traffico per il bilanciamento del carico.

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## Informazioni sui record DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## Configurare le app Web per la modalità standard

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## Aggiungere un record DNS per il dominio personalizzato


> [AZURE.NOTE]Se si è acquistato un dominio tramite App Web del servizio app di Azure, ignorare i passaggi seguenti e fare riferimento all'ultimo passaggio dell'articolo <a href="/documentation/articles/custom-dns-web-site-buydomains-web-app" title="Web Apps" class="current">Acquistare un dominio per app Web</a>.


Per associare il dominio personalizzato a un'app Web nel servizio app di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti dal registrar da cui è stato acquistato il nome di dominio. Per individuare e usare gli strumenti DNS, attenersi alla procedura seguente.

1. Accedere all'account presso il registrar e cercare la pagina in cui gestire i record DNS. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**. Un collegamento a questa pagina è spesso disponibile nelle informazioni dell'account, cercando una voce simile a **My domains**.

4. Dopo aver trovato la pagina di gestione del nome di dominio, cercare un collegamento che consenta di modificare i record DNS. Tale collegamento può essere denominato **Zone file** o **DNS Records** oppure figurare come collegamento di configurazione in **Advanced**.

	* La pagina conterrà molto probabilmente alcuni record già creati, ad esempio una voce per associare '**@**' o '*' a una pagina di registrazione semplice. Può inoltre contenere record per sottodomini comuni, ad esempio **www**.
	* Nella pagina saranno presenti voci per record **CNAME** oppure verrà visualizzato un elenco a discesa per selezionare il tipo di record. È anche possibile che siano presenti voci per altri record, ad esempio **A** e **MX**. In alcuni casi, i record CNAME avranno una denominazione diversa, come ad esempio nel caso dei record **Alias**.
	* La pagina conterrà inoltre campi che consentono di **eseguire il mapping** da un **nome host** o un **nome di dominio** a un altro nome di dominio.

5. Anche se le specifiche di ogni registrar possono variare, in genere viene eseguito il mapping *dal* nome di dominio personalizzato, ad esempio **contoso.com**, *al* nome di dominio di Gestione traffico (**contoso.trafficmanager.net**) usato per l'app Web.

6. Dopo aver completato l'aggiunta o la modifica di record DNS presso il registrar, salvare le modifiche.

<a name="enabledomain"></a>
## Abilitare Gestione traffico

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 

<!---HONumber=July15_HO4-->