<properties 
	pageTitle="Servizio app di Azure: scalabilità delle applicazioni nel servizio app" 
	description="Informazioni su vantaggi e svantaggi della scalabilità dell'applicazione nel servizio app." 
	keywords="servizio app, servizio app di azure, scala, scalabile, piano di servizio app, costo del servizio app"
	services="app-service" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="byvinyal"/>
	
# Servizio app di Azure: scalabilità delle applicazioni nel servizio app

Le applicazioni ospitate nel servizio app di Azure possono [raggiungere un livello avanzato di scalabilità](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage), tuttavia la scalabilità di un'applicazione è un problema complesso che non ha una soluzione "universale". Per ridimensionare correttamente l'applicazione occorre considerare 3 aree chiave che contribuiranno al corretto funzionamento delle applicazioni:

1. Comprendere l'architettura dell'applicazione e i relativi punti deboli.
	* Si tratta di un'applicazione con stato o senza stato?
	* Quali sono i componenti dell'applicazione?
		* Dove sono i colli di bottiglia nell'applicazione? 
	* Quando si applica il carico all'app, quali elementi vengono interrotti per primi?
2. Comprendere i requisiti di carico e prestazioni previsti.
	* L'applicazione deve servire 1000 utenti o un milione?
	* Il traffico proviene da un'unica area geografica o a livello globale?
	* Sono presenti variazioni stagionali o picchi di traffico? 
	* Con quale velocità deve rispondere l'applicazione? Un secondo? Un millisecondo?
3. Comprendere e sfruttare correttamente la piattaforma di hosting dell'app.
	* Quali funzionalità è necessario sfruttare per raggiungere gli obiettivi di scalabilità?
	
Questa sezione aiuta a comprendere tutti i fattori e a escogitare una strategia che sfrutti le funzionalità del servizio app necessarie per raggiungere gli obiettivi di scalabilità.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]

<!---HONumber=AcomDC_0121_2016-->