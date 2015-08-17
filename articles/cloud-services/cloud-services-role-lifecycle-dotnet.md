<properties 
pageTitle="Rispondere agli eventi del ciclo di vita di Servizi Cloud utilizzando .NET" 
description="Informazioni su come utilizzare i metodi del ciclo di vita di un ruolo del Servizio Cloud in .NET" 
services="cloud-services" 
documentationCenter=".net" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="06/11/2015" 
ms.author="adegeo"/>

# Personalizzare il ciclo di vita di un ruolo Web o di lavoro in .NET

Quando si crea un ruolo di lavoro, si estende la classe[RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx)che fornisce metodi per la sovrascrittura che consentono di rispondere agli eventi del ciclo di vita. Per i ruoli web questa classe è facoltativa, molto utilizzata per rispondere agli eventi del ciclo di vita.

## Estendere la classe RoleEntryPoint

La classe[RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx)include metodi che vengono chiamati da Azure quando si**inizia**,**esegue**o**arresta**un ruolo Web o di lavoro. Facoltativamente, è possibile ignorare questi metodi per gestire l'inizializzazione, sequenze di arresto o il thread di esecuzione del ruolo.

Quando si estende**RoleEntryPoint**è necessario tenere presente i seguenti comportamenti dei metodi:

-   I metodi[OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx)e[OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx)restituiscono un valore booleano, pertanto è possibile restituire**false**da questi metodi.

     Se il codice restituisce**false**il processo del ruolo viene interrotto improvvisamente, senza eseguire nessuna sequenza di arresto in programma. In generale, è consigliabile evitare la restituzione**false**dal metodo**OnStart**.
     
-   Qualsiasi eccezione non rilevata all'interno di un overload di un metodo**RoleEntryPoint**viene considerato come un'eccezione non gestita.

     Se si verifica un'eccezione all'interno di uno dei metodi del ciclo di vita, Azure genera l’evento[UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx)e il processo viene interrotto. Quando il ruolo viene portato offline, viene riavviato da Azure. Quando si verifica un'eccezione non gestita, l’evento[Stopping](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx)non viene generato e il metodo**OnStop**non viene chiamato.

Se il ruolo non viene avviato o passa in modo ciclico tra gli stati di arresto, di inizializzazione e di occupato, il codice potrebbe generare un'eccezione non gestita all'interno di uno degli eventi del ciclo di vita ogni volta che il ruolo viene riavviato. In questo caso, utilizzare l’evento[UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) per determinare la causa dell'eccezione e gestirla nel modo appropriato. Il ruolo potrebbe anche essere restituito dal metodo[Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) che causa il riavvio del ruolo. Per ulteriori informazioni sugli stati di distribuzione, vedere[problemi comuni che causano il riciclo dei ruoli](https://msdn.microsoft.com/library/azure/gg465402.aspx).

> [AZURE.NOTE]Se si utilizzano [gli strumenti di Azure per Microsoft Visual Studio](https://msdn.microsoft.com/library/azure/ee405484.aspx) per sviluppare l'applicazione, i modelli di progetto del ruolo estendono automaticamente la classe**RoleEntryPoint**, nei file WebRole.cs e WorkerRole.cs.

## Metodo OnStart

Il metodo**OnStart** viene chiamato quando l'istanza del ruolo viene portata online da Azure. Mentre il codice OnStart viene eseguito, l'istanza del ruolo è contrassegnata come**occupata**e non verrà indirizzato alcun traffico esterno dal bilanciamento del carico. È possibile eseguire l'override di questo metodo per eseguire operazioni di inizializzazione, ad esempio implementare i gestori eventi e avviare la [diagnostica Azure](cloud-services-how-to-monitor.md).

Se**OnStart**restituisce**true**l'istanza viene inizializzata correttamente e il metodo**RoleEntryPoint**viene chiamato da Azure. Se**OnStart**restituisce**false**il ruolo termina immediatamente, senza eseguire alcuna sequenza di arresto pianificata.

Il seguente esempio di codice illustra come eseguire l'override del metodo**OnStart**. Questo metodo configura e avvia un monitor di diagnostica all’avvio dell'istanza del ruolo e imposta il trasferimento dei dati di registrazione in un account di archiviazione:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## Metodo OnStop

Il metodo**OnStop**viene chiamato quando un'istanza del ruolo viene portata offline da Azure e prima della chiusura del processo. È possibile eseguire l'override di questo metodo per chiamare il codice necessario affinché l'istanza del ruolo effettui un arresto normale.

> [AZURE.IMPORTANT]Il codice in esecuzione nel metodo**OnStop**presenta un tempo di completamento limitato quando viene chiamato per motivi diversi da un arresto avviato dall'utente. Trascorso tale tempo, il processo viene terminato, pertanto è necessario assicurarsi che il codice del metodo**OnStop**possa essere eseguito rapidamente o tolleri il mancato completamento. Il metodo**OnStop** viene chiamato dopo che l’evento**Stopping**viene generato.


## Metodo Run

È possibile eseguire l'override del metodo**Run** per implementare un thread a esecuzione prolungata per l'istanza del ruolo.

L’esecuzione dell'override del metodo**Run**non è obbligatoria, l'implementazione predefinita avvia un thread in costante stato di sospensione. Se si esegue l'override del metodo**Run**, il codice dovrebbe bloccarsi in modo indefinito. Se il metodo**Run**restituisce un valore, il nuovo ciclo del ruolo viene avviato in modo automatico e normale; in altre parole, Azure genera l’evento **Stopping**e chiama il metodo**OnStop**in modo che le sequenze di arresto possano essere eseguite prima che il ruolo venga portato offline.


### Implementazione dei metodi del ciclo di vita ASP.NET per un ruolo web

È possibile utilizzare i metodi del ciclo di vita ASP.NET, oltre a quelli forniti dalla classe**RoleEntryPoint**, per gestire le sequenze di inizializzazione e di arresto per un ruolo web. Potrebbe essere utile per motivi di compatibilità se si trasferisce un'applicazione ASP.NET esistente in Azure. I metodi del ciclo di vita ASP.NET vengono chiamati dall'interno dei metodi**RoleEntryPoint**. Il metodo**Application\_Start**viene chiamato al termine del metodo **RoleEntryPoint.OnStart**. Il metodo**Application\_End**viene chiamato prima del metodo **RoleEntryPoint.OnStop**.

## Passaggi successivi
Informazioni su come[creare un pacchetto del servizio cloud](cloud-services-model-and-package.md).

<!---HONumber=August15_HO6-->