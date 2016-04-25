<properties
	pageTitle="Distribuzione di applicazioni nel servizio app di Azure"
	description="Informazioni su come distribuire le applicazioni per l'uso nel servizio app"
	keywords="servizio app, servizio app di azure, distribuire, distribuzione"
	services="app-service"
	documentationCenter=""
	authors="dariagrigoriu"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="dariagrigoriu"/>

# Panoramica della distribuzione nel servizio app di Azure

Il servizio app di Azure offre una funzionalità integrata e avanzata concepita per supportare la creazione di flussi di lavoro di distribuzione flessibili e potenti. La distribuzione di app può sfruttare alcune opzioni, ad esempio l'integrazione continua o la pubblicazione locale del controllo del codice sorgente, WebDeploy e FTP. Il metodo consigliato per la distribuzione di app di produzione è lo scambio degli slot di distribuzione. Gli slot di distribuzione rappresentano gli ambienti di staging e integrazione associati alle app di produzione. Gli slot di distribuzione possono essere configurati e impostati per ricevere traffico Web per la convalida. Il traffico può essere scambiato su richiesta per la distribuzione nell'ambiente di produzione senza tempi di inattività e con avvio automatico. I passaggi di un flusso di lavoro di distribuzione possono essere facilmente automatizzati con prodotti di gestione del rilascio, ad esempio Release Management per Visual Studio. Questo approccio è utile per il coordinamento con altre risorse della soluzione, ad esempio l'archivio dati, la ricorrenza e la replica tra più unità di distribuzione.

[AZURE.INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]

<!---HONumber=AcomDC_0413_2016-->