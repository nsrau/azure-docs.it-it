<properties
	pageTitle="Panoramica di Diagnostica di Azure"
	description="Usare Diagnostica di Azure per debug, valutazione delle prestazioni, monitoraggio e analisi del traffico dei servizi cloud, delle macchine virtuali e di Service Fabric."
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>


# Che cos'è Diagnostica di Microsoft Azure?


Diagnostica di Azure è la funzionalità all'interno di Azure che consente la raccolta di dati di diagnostica in un'applicazione distribuita. È possibile usare l'estensione di diagnostica da numerose origini diverse. Sono attualmente supportati i ruoli Web e di lavoro dei servizi cloud di Azure e le macchine virtuali di Azure che eseguono Microsoft Windows e Service Fabric. Altri servizi di Azure dispongono di propri strumenti di diagnostica separati.

## Dati che è possibile raccogliere

Diagnostica di Azure consente di raccogliere i tipi di dati seguenti:

Origine dati|Descrizione
---|---
Contatori delle prestazioni | Contatori delle prestazioni del sistema operativo e personalizzati
Log applicazioni | Messaggi di traccia scritti dall'applicazione
Registri eventi di Windows | Informazioni inviate al sistema di registrazione eventi di Windows.
Origine dell'evento .NET | Eventi di scrittura di codice con la classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) di .NET
Log di IIS | Informazioni sui siti Web di IIS
ETW basato su manifesto | Traccia di eventi per eventi Windows generati da qualsiasi processo
Dump di arresto anomalo del sistema | Informazioni sullo stato del processo in caso di arresto anomalo dell'applicazione.
Log degli errori personalizzati | Log creati dall'applicazione o dal servizio.
Log dell'infrastruttura diagnostica di Azure|Informazioni su Diagnostica

L'estensione Diagnostica di Azure consente di trasferire i dati in un account di archiviazione di Azure o di inviarli a servizi quali [Application Insights](./application-insights/app-insights-cloudservices.md). È possibile usare i dati per il debug, la risoluzione dei problemi, la valutazione delle prestazioni, il monitoraggio dell'utilizzo delle risorse, l'analisi del traffico, la pianificazione della capacità e il controllo.


## Controllo delle versioni
Vedere [Cronologia delle versioni di Diagnostica di Azure](azure-diagnostics-versioning-history.md).

## Passaggi successivi
Scegliere il servizio su cui si desidera raccogliere dati di diagnostica e usare i seguenti articoli per iniziare. Usare i collegamenti di diagnostica di Azure generali per riferimento per attività specifiche.

## App Web
Si noti che le app Web non usano Diagnostica di Azure. Trovare le informazioni equivalenti in [App Web](./app-service-web/web-sites-enable-diagnostic-log.md)

## Servizi cloud con Diagnostica di Azure
- Se si usa Visual Studio, vedere [Debug di un servizio cloud o di una macchina virtuale di Azure in Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md) per iniziare. In alternativa, vedere
- [Come monitorare i servizi cloud usando Diagnostica di Azure](./cloud-services/cloud-services-how-to-monitor.md)
- [Configurare Diagnostica di Azure in un'applicazione di servizi cloud](./cloud-services/cloud-services-dotnet-diagnostics.md)

Per argomenti più avanzati, vedere
- [Uso di Diagnostica di Azure con Application Insights per i servizi cloud](./application-insights/app-insights-cloudservices.md)
- [Tracciare il flusso in un'applicazione di Servizi cloud con Diagnostica di Azure](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Usare PowerShell per configurare la diagnostica nei servizi cloud](./virtual-machines/virtual-machines-extensions-diagnostics-windows-powershell.md)


## Macchine virtuali con Diagnostica di Azure
- Se si usa Visual Studio, vedere [Debug di un servizio cloud o di una macchina virtuale di Azure in Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md) per iniziare. In alternativa, vedere
- [Configurare Diagnostica di Azure in una macchina virtuale di Azure](./virtual-machines-dotnet-diagnostics.md)

Per argomenti più avanzati, vedere
- [Usare PowerShell per abilitare la Diagnostica di Azure in una macchina virtuale di Azure](./virtual-machines/virtual-machines-extensions-diagnostics-windows-powershell.md)
- [Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante i modelli di Gestione risorse di Azure](./virtual-machines/virtual-machines-extensions-diagnostics-windows-template.md)

## Service Fabric con Diagnostica di Azure
Per un'introduzione, vedere [Monitorare un'applicazione di Service Fabric](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Numerosi altri articoli sulla diagnostica di Service Fabric sono disponibili nella struttura di navigazione a sinistra.

## Articoli su Diagnostica di Azure
- [Schemi di configurazione di diagnostica di Azure](https://msdn.microsoft.com/library/azure/mt634524.aspx) -Informazioni su come modificare il file di schema per raccogliere e inviare dati di diagnostica. Si noti che è anche possibile usare Visual Studio per modificare il file di schema.
- [Archiviare e visualizzare i dati di diagnostica nell'account di archiviazione Azure](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - Nomi delle tabelle e dei BLOB in cui vengono scritti i dati di diagnostica.
- Informazioni su come [Creare e usare contatori di prestazioni in un'applicazione Azure](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
- Informazioni su come [Configurare Diagnostica di Azure per inviare dati ad Application Insights](./azure-diagnostics-configure-applicationinsights.md)
- Se si riscontrano problemi di avvio della diagnostica o individuazione dei dati nelle tabelle di archiviazione di Azure, vedere [Risoluzione dei problemi di Diagnostica di Azure](./azure-diagnostics-troubleshooting.md)

<!---HONumber=AcomDC_0302_2016-->