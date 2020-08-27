---
title: Gestire eventi del ciclo di vita dei servizi cloud | Documentazione Microsoft
description: Informazioni su come usare i metodi del ciclo di vita di un ruolo del servizio cloud in .NET, incluso RoleEntryPoint, che fornisce i metodi per rispondere agli eventi del ciclo di vita.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: d64414abfbc62e52b172a2c42796ec8d89d1719f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930061"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalizzare il ciclo di vita di un ruolo Web o di lavoro in .NET
Quando si crea un ruolo di lavoro, si estende la classe [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) che fornisce metodi per la sovrascrittura che consentono di rispondere agli eventi del ciclo di vita. Per i ruoli Web questa classe è facoltativa, molto utilizzata per rispondere agli eventi del ciclo di vita.

## <a name="extend-the-roleentrypoint-class"></a>Estendere la classe RoleEntryPoint
La classe [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) include metodi che vengono chiamati da Azure quando **avvia**, **esegue** o **arresta** un ruolo Web o di lavoro. Facoltativamente, è possibile ignorare questi metodi per gestire l'inizializzazione, sequenze di arresto o il thread di esecuzione del ruolo. 

Quando si estende **RoleEntryPoint**, è necessario tenere presenti i seguenti comportamenti dei metodi:

* Il metodo [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) restituisce un valore booleano, pertanto è possibile restituire **false** da questo metodo.
  
   Se il codice restituisce **false**il processo del ruolo viene interrotto improvvisamente, senza eseguire nessuna sequenza di arresto in programma. In generale, è consigliabile evitare la restituzione di **false** dal metodo **OnStart**.
* Le eccezioni non rilevate all'interno di un overload di un metodo **RoleEntryPoint** vengono trattate come eccezioni non gestite.
  
   Se si verifica un'eccezione all'interno di uno dei metodi del ciclo di vita, Azure genera l’evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) e il processo viene interrotto. Quando il ruolo viene portato offline, viene riavviato da Azure. Quando si verifica un'eccezione non gestita, l’evento [Stopping](/previous-versions/azure/reference/ee758136(v=azure.100)) non viene generato e il metodo **OnStop** non viene chiamato.

Se il ruolo non viene avviato o passa in modo ciclico tra gli stati di arresto, di inizializzazione e di occupato, il codice potrebbe generare un'eccezione non gestita all'interno di uno degli eventi del ciclo di vita ogni volta che il ruolo viene riavviato. In questo caso utilizzare l'evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) per determinare la causa dell'eccezione e gestirla opportunamente. Il ruolo potrebbe anche essere restituito dal metodo [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) che causa il riavvio del ruolo. Per ulteriori informazioni sugli stati di distribuzione, vedere [problemi comuni che causano il riciclo dei ruoli](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Se si usano gli **strumenti di Azure per Microsoft Visual Studio** per sviluppare l'applicazione, i modelli di progetto di ruolo estendono automaticamente la classe **RoleEntryPoint** nei file *WebRole.cs* e *WorkerRole.cs*.
> 
> 

## <a name="onstart-method"></a>Metodo OnStart
Il metodo **OnStart** viene chiamato quando l'istanza del ruolo viene portata online da Azure. Mentre il codice OnStart viene eseguito, l'istanza del ruolo è contrassegnata come **occupata** e non verrà indirizzato alcun traffico esterno dal bilanciamento del carico. È possibile eseguire l'override di questo metodo per eseguire operazioni di inizializzazione, ad esempio implementare i gestori eventi e avviare la [diagnostica Azure](cloud-services-how-to-monitor.md).

Se **OnStart** restituisce **true**, l'inizializzazione dell'istanza viene completata e Azure chiama il metodo **RoleEntryPoint.Run**. Se **OnStart** restituisce **false**, il ruolo termina immediatamente senza eseguire alcuna sequenza di arresto pianificata.

Nell'esempio di codice seguente viene illustrato come eseguire l'override del metodo **OnStart**. Questo metodo configura e avvia un monitor di diagnostica all’avvio dell'istanza del ruolo e imposta il trasferimento dei dati di registrazione in un account di archiviazione:

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

## <a name="onstop-method"></a>Metodo OnStop
Il metodo **OnStop** viene chiamato dopo che un'istanza del ruolo è stata portata offline da Azure e prima della chiusura del processo. È possibile eseguire l'override di questo metodo per chiamare il codice necessario affinché l'istanza del ruolo effettui un arresto normale.

> [!IMPORTANT]
> Il codice del metodo **OnStop** dispone di un tempo di completamento limitato quando viene chiamato per motivi diversi da un arresto avviato dall'utente. Trascorso tale tempo, il processo viene terminato, pertanto è necessario assicurarsi che il codice del metodo **OnStop** possa essere eseguito rapidamente o tolleri il mancato completamento. Il metodo **OnStop** viene chiamato dopo che l'evento **Stopping** viene generato.
> 
> 

## <a name="run-method"></a>Metodo Run
È possibile eseguire l'override del metodo **Run** per implementare un thread a esecuzione prolungata per l'istanza del ruolo.

L'override del metodo **Run** non è obbligatorio; l'implementazione predefinita avvia un thread in costante stato di sospensione. Se si esegue l'override del metodo **Run**, il codice dovrebbe bloccarsi in modo indefinito. Se il metodo **Run** restituisce un valore, il nuovo ciclo del ruolo inizia in modo automatico e normale; in altri termini, l'evento **Stopping** viene generato e il metodo **OnStop** viene chiamato da Azure per consentire l'esecuzione delle sequenze di arresto prima che il ruolo venga portato offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementazione dei metodi del ciclo di vita ASP.NET per un ruolo web
È possibile utilizzare i metodi del ciclo di vita di ASP.NET, oltre a quelli forniti dalla classe **RoleEntryPoint**, per gestire le sequenze di inizializzazione e di arresto di un ruolo Web. Potrebbe essere utile per motivi di compatibilità se si trasferisce un'applicazione ASP.NET esistente in Azure. I metodi del ciclo di vita di ASP.NET vengono chiamati dall'interno dei metodi **RoleEntryPoint**. Il metodo **Application\_Start** viene chiamato al termine del metodo **RoleEntryPoint.OnStart**. Il metodo **Application\_End** viene chiamato prima del metodo **RoleEntryPoint.OnStop**.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [creare un pacchetto del servizio cloud](cloud-services-model-and-package.md).




