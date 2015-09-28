<properties 
	pageTitle="Configurazione di un Web application firewall (WAF) per l'ambiente del servizio app" 
	description="Informazioni su come configurare un Web application firewall davanti all'ambiente del servizio app." 
	services="app-service\web" 
	documentationCenter="" 
	authors="naziml" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015" 
	ms.author="naziml"/>

# Configurazione di un Web application firewall (WAF) per l'ambiente del servizio app

## Panoramica ##
[Barracuda WAF per Azure](https://www.barracuda.com/programs/azure) è un Web application firewall disponibile in [Azure Marketplace](http://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) che consente di proteggere le applicazioni Web esaminando il traffico Web in ingresso per bloccare SQL injection, attacchi tramite script da altri siti, caricamenti di malware, DDoS di applicazioni e altri attacchi. Esamina anche le risposte provenienti dai server Web back-end per la prevenzione della perdita dei dati. In associazione all'isolamento e alla scalabilità aggiuntiva fornita dagli ambienti del servizio app, costituisce un ambiente ideale per le applicazioni Web critiche per l'azienda, che devono sostenere richieste dannose e un volume elevato di traffico.

\+[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Impostazione ##
Per questo documento si configurerà l'ambiente del servizio app dietro più istanze con carico bilanciato di Barracuda WAF, in modo che solo il traffico proveniente dal firewall WAF possa raggiungere l'ambiente del servizio app che non sarà accessibile dalla rete perimetrale. Gestione traffico di Azure sarà invece davanti alle istanze di Barracuda WAF per bilanciare il carico tra i data center e le aree di Azure. Il diagramma generale della configurazione sarà simile al seguente.

![Architettura][Architecture]

## Configurazione dell'ambiente del servizio app ##
Per configurare un ambiente del servizio app, fare riferimento alla [documentazione](app-service-web-how-to-create-an-app-service-environment.md) sull'argomento. Una volta creato un ambiente del servizio app, è possibile creare in questo ambiente le [app Web](app-service-web-overview.md), le [app per le API](app-service-api-apps-why-best-platform.md) e le [app per dispositivi mobili](app-service-mobile-value-prop-preview.md) che saranno protette dietro il firewall WAF che verrà configurato nella sezione successiva.

## Configurazione del servizio Cloud Barracuda WAF ##
Sul sito Barracuda è disponibile un [articolo dettagliato](https://techlib.barracuda.com/WAF/AzureDeploy) sulla distribuzione del firewall WAF in una macchina virtuale in Azure. Poiché tuttavia quello che si vuole è la ridondanza senza introdurre un singolo punto di guasto, è opportuno distribuire almeno 2 VM con istanze WAF nello stesso servizio cloud quando si seguono queste istruzioni.

### Aggiunta di endpoint al servizio cloud ###
Una volta create 2 o più istanze VM WAF nel servizio cloud, è possibile usare il [portale di gestione di Azure](https://portal.azure.com) per aggiungere endpoint HTTP e HTTPS usati dall'applicazione come illustrato nell'immagine seguente.

![Configurare l'endpoint][ConfigureEndpoint]

Se le applicazioni usano altri endpoint, assicurarsi di aggiungerli tutti a questo elenco.

### Configurazione di Barracuda WAF tramite il relativo portale di gestione ###
Barracuda WAF usa la porta TCP 8000 per la configurazione tramite il proprio portale di gestione. Poiché ci sono più istanze di VM WAF, sarà necessario ripetere questi passaggi per ogni istanza di VM.


> Nota: una volta completata la configurazione WAF, rimuovere l'endpoint TCP/8000 da tutte le VM WAF per mantenere protetto il firewall WAF.

Aggiungere l'endpoint di gestione, come illustrato nell'immagine seguente, per configurare Barracuda WAF.

![Aggiungere l'endpoint di gestione][AddManagementEndpoint]
 
Usare un browser per passare all'endpoint di gestione nel servizio cloud. Se il servizio cloud si chiama test.cloudapp.net, si accederà a questo endpoint passando a http://test.cloudapp.net:8000. Dovrebbe essere visualizzata una pagina di accesso come quella seguente a cui è possibile accedere usando le credenziali specificate in fase di configurazione delle VM WAF.

![Pagina di accesso gestione][ManagementLoginPage]

Dopo l'accesso, dovrebbe essere visualizzato un dashboard come quello nell'immagine seguente che presenterà le statistiche di base sulla protezione WAF.

![Dashboard di gestione][ManagementDashboard]

Fare clic sulla scheda Services per configurare il firewall WAF per i servizi protetti. Per altri dettagli sulla configurazione di Barracuda WAF, è possibile consultare la [relativa documentazione](https://techlib.barracuda.com/waf/getstarted1). Nell'esempio seguente, è stata configurata un'app Web di Azure che gestisce il traffico su HTTP e HTTPS.

![Aggiunta di servizi di gestione][ManagementAddServices]

> Nota: a seconda di come sono configurate le applicazioni e di quali funzionalità sono in uso nell'ambiente del servizio app, sarà necessario inoltrare il traffico per le porte TCP diverse dalla 80 e dalla 443, ad esempio se IP SSL è configurato per un'app Web. Per un elenco di porte di rete usate negli ambienti del servizio app, fare riferimento alla sezione Porte di rete della [documentazione sul controllo del traffico in ingresso](app-service-app-service-environment-control-inbound-traffic.md).

## Configurazione di Gestione traffico di Microsoft Azure (FACOLTATIVO) ##
Se l'applicazione è disponibile in più aree, è preferibile bilanciarne il carico dietro [Gestione traffico di Azure](traffic-manager.md). A questo scopo, è possibile aggiungere un endpoint nel [portale di gestione di Azure](https://manage.azure.com) usando il nome del servizio cloud per il firewall WAF nel profilo di Gestione traffico come illustrato nell'immagine seguente.

![Endpoint di Gestione traffico][TrafficManagerEndpoint]

Se l'applicazione richiede l'autenticazione, assicurarsi di disporre di qualche risorsa che non richiede alcuna autenticazione per consentire a Gestione traffico di effettuare il ping per la disponibilità dell'applicazione. È possibile configurare l'URL nella sezione Configura del [portale di gestione di Azure](https://manage.azure.com) come illustrato di seguito.

![Configurare Gestione traffico][ConfigureTrafficManager]

Per inoltrare i ping di Gestione traffico dal firewall WAF all'applicazione, è necessario configurare Website Translations in Barracuda WAF per inoltrare il traffico all'applicazione, come illustrato nell'esempio seguente.

![Website Translations][WebsiteTranslations]

## Protezione del traffico verso l'ambiente del servizio app con i gruppi di risorse di rete##
Attenersi alla [documentazione sul controllo del traffico in ingresso](app-service-app-service-environment-control-inbound-traffic.md) per informazioni dettagliate sulla limitazione del traffico all'ambiente del servizio app dal firewall WAF usando solo l'indirizzo VIP del servizio cloud. Ecco un comando di Powershell di esempio per eseguire questa attività per la porta TCP 80.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Sostituire SourceAddressPrefix con l'indirizzo IP virtuale (VIP) del servizio cloud del firewall WAF.

> Nota: l'indirizzo VIP del servizio cloud cambia quando si elimina e si ricrea il servizio cloud. In questo caso, assicurarsi di aggiornare l'indirizzo IP nel gruppo di risorse di rete.
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png

<!---HONumber=Sept15_HO3-->