<properties 
	pageTitle="Configurazione di un nome di dominio personalizzato per un'app Web in Servizio app di Azure che usa Gestione traffico" 
	description="Usare un nome di dominio personalizzato per un'app Web in Servizio app di Azure che usa Gestione traffico per il bilanciamento del carico." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Configurazione di un nome di dominio personalizzato per un'app Web in Servizio app di Azure con Gestione traffico

[AZURE.INCLUDE [web-selector](../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Questo articolo fornisce istruzioni generiche sull'uso di un nome di dominio personalizzato con Servizio app di Azure in cui viene usato Gestione traffico per il bilanciamento del carico.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Contenuto dell'articolo:

-   [Informazioni sui record DNS](#understanding-records)
-   [Configurare le app Web per la modalità standard](#bkmk_configsharedmode)
-   [Aggiunta di un record DNS per il dominio personalizzato](#bkmk_configurecname)
-   [Abilitare Gestione traffico per l'app Web](#enabledomain)

<a name="understanding-records"></a>
## Informazioni sui record DNS

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## Configurare le app Web per la modalità standard

[AZURE.INCLUDE [modalità](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## Aggiungere un record DNS per il dominio personalizzato

Per associare il dominio personalizzato a un'app Web in Servizio app di Azure, è necessario aggiungere nella tabella DNS una nuova voce per il dominio personalizzato usando gli strumenti forniti dal registrar da cui è stato acquistato il nome di dominio. Per individuare e usare gli strumenti DNS, attenersi alla procedura seguente.

1. Accedere all'account presso il registrar e cercare la pagina in cui gestire i record DNS. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**. Un collegamento a questa pagina è spesso disponibile nelle informazioni dell'account cercando una voce simile a **My domains**.

4. Dopo aver trovato la pagina di gestione del nome di dominio, cercare un collegamento che consenta di modificare i record DNS. Tale collegamento può essere elencato come **File di zona**, **Record DNS** o come un collegamento di configurazione **avanzata**.

	* La pagina conterrà molto probabilmente alcuni record già creati, ad esempio una voce per associare '**@**' o '*' a una pagina  'domain parking'. Può inoltre contenere record per sottodomini comuni, ad esempio **www**.
	* Nella pagina saranno presenti voci per **record CNAME** oppure verrà visualizzato un elenco a discesa per selezionare il tipo di record. È anche possibile che siano presenti voci per altri record, ad esempio **record A** e **record MX**. In alcuni casi, i record CNAME avranno una denominazione diversa, come ad esempio nel caso dei **record Alias**.
	* La pagina conterrà inoltre campi che consentono di eseguire il **mapping** da un **nome host** o **nome di dominio** a un altro nome di dominio.

5. Sebbene le specifiche di ogni registrar varino, in generale viene eseguito il mapping  *from* nome di dominio personalizzato, ad esempio **contoso.com**,  *to* nome di dominio di Gestione traffico (**contoso.trafficmanager.net**) usato per l'app Web.

6. Dopo aver completato l'aggiunta o la modifica di record DNS presso il registrar, salvare le modifiche.

<a name="enabledomain"></a>
## Abilitare Gestione traffico

[AZURE.INCLUDE [modalità](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Servizio app di Azure e relativo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida sul passaggio dal vecchio al nuovo portale, vedere: [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--HONumber=52-->