---
title: Cluster Resource Manager di Service Fabric - Gruppi di applicazioni | Microsoft Docs
description: "Informazioni generali sulla funzionalità dei gruppi di applicazioni in Cluster Resource Manager di Service Fabric "
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 3212631ede7125bd849c2d9ba86ba2a0747d69ca
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---
# <a name="introduction-to-application-groups"></a>Introduzione ai gruppi di applicazioni
Cluster Resource Manager di Service Fabric generalmente gestisce le risorse del cluster distribuendo il carico (rappresentato tramite [Metriche](service-fabric-cluster-resource-manager-metrics.md)) in modo uniforme nell'intero cluster. Service Fabric gestisce la capacità dei nodi del cluster e il cluster nel suo complesso tramite la [capacità](service-fabric-cluster-resource-manager-cluster-description.md). Metriche e capacità rappresentano un'ottima soluzione per molti tipi di carichi di lavoro, ma i modelli che fanno largo uso di diverse istanze di applicazione di Service Fabric comportano a volte requisiti aggiuntivi. Ad esempio, si può desiderare di:

- Riservare alcune capacità sui nodi del cluster per i servizi all'interno di qualche istanza di applicazione denominata
- Limitare il numero totale di nodi eseguiti dai servizi all'interno di un'istanza dell'applicazione denominata (anziché distribuirli tramite l'intero cluster)
- Definire le capacità nell'istanza di applicazione stessa denominata per limitare il numero di servizi o il consumo totale delle risorse dei servizi al suo interno

Per soddisfare questi requisiti, Cluster Resource Manager di Service Fabric supporta una funzionalità chiamata Gruppi di applicazioni.

## <a name="limiting-the-maximum-number-of-nodes"></a>Limitazione del numero massimo di nodi
Il caso d'uso più semplice della capacità dell'applicazione è quando un'istanza di applicazione deve essere limitata a un numero massimo di nodi. Consolida tutti i servizi all'interno di tale istanza dell'applicazione su un numero di set di macchine. Il consolidamento è utile quando si sta cercando di stimare o limitare l'utilizzo delle risorse fisiche da parte dei servizi all'interno di tale istanza dell'applicazione denominata. 

Nell'immagine seguente è mostrata un'istanza di applicazione con e senza un numero massimo di nodi definiti.

<center>
![Istanza di applicazione che definisce il numero massimo di nodi][Image1]
</center>

Nell'esempio a sinistra, l'applicazione non ha un numero massimo di nodi definito, e ha tre servizi. Cluster Resource Manager ha distribuito tutte le repliche tra sei nodi disponibili per ottenere il bilanciamento ottimale nel cluster (il comportamento predefinito). Nell'esempio a destra, notiamo la stessa applicazione limitata a tre nodi.

Il parametro che gestisce questo comportamento è denominato MaximumNodes. Questo parametro può essere impostato durante la creazione dell'applicazione oppure può essere aggiornato per un'istanza di applicazione già in esecuzione.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

All'interno del set di nodi, Cluster Resource Manager non garantisce quali oggetti di servizio si posizionano insieme o quali nodi vengono usati.

## <a name="application-metrics-load-and-capacity"></a>Metriche, carico e capacità dell'applicazione
I gruppi di applicazioni consentono anche di definire le metriche associate a una determinata istanza di applicazione denominata, nonché la capacità di quell'istanza dell'applicazione in relazione a tali metriche. Le metriche dell'applicazione consentono di tener traccia del consumo di risorse da parte dei servizi all'interno dell'istanza di applicazione, di riservarlo e di limitarlo.

Per ogni metrica di applicazione, è possibile impostare due valori:

- **Capacità totale dell'applicazione**: rappresenta la capacità totale dell'applicazione per una particolare metrica. Cluster Resource Manager impedisce di creare nuovi servizi all'interno dell'istanza di applicazione che farebbero superare questo valore di carico totale. Si supponga, ad esempio, che l'istanza di applicazione abbia una capacità pari a 10 e il carico sia già pari a cinque. La creazione di un servizio con un carico totale predefinito di 10 non verrebbe consentita.
- **Capacità massima del nodo**: specifica il carico totale massimo per l'applicazione in un singolo nodo. Se il carico supera questa capacità, Cluster Resource Manager sposta le repliche in altri nodi in modo che il carico diminuisca.


Powershell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Riserva della capacità
I gruppi di applicazioni vengono comunemente usati anche per garantire che le risorse all'interno del cluster siano riservate per una determinata istanza di applicazione. Lo spazio viene riservato sempre quando viene creata l'istanza dell'applicazione.

La riserva dello spazio nel cluster per l'applicazione si verifica immediatamente, anche se:
- l'istanza dell'applicazione viene creata ma non dispone ancora di tutti i servizi all'interno di essa
- il numero di servizi all'interno dell'istanza dell'applicazione cambia ogni volta 
- i servizi esistono ma non usano le risorse 

Per prenotare le risorse per un'istanza dell'applicazione è necessario definire due parametri aggiuntivi: *MinimumNodes* e *NodeReservationCapacity*

- **MinimumNodes**: definisce il numero minimo di nodi con cui l'istanza dell'applicazione deve essere eseguita.  
- **NodeReservationCapacity**: questa impostazione è per ogni metrica per l'applicazione. Il valore è la quantità di tale metrica riservata per l'applicazione su ogni nodo in cui si eseguono i servizi dell'applicazione.

La combinazione di **MinimumNodes** e **NodeReservationCapacity** garantisce una riserva minima del carico per l'applicazione all'interno del cluster. Se si ha meno capacità rimanente nel cluster rispetto al totale della riserva richiesta, la creazione dell'applicazione ha esito negativo. 

Di seguito viene illustrato un esempio di come viene riservata la capacità:

<center>
![Istanze di applicazione che definiscono la capacità riservata][Image2]
</center>

Nell'esempio a sinistra, le applicazioni non hanno una capacità definita. Cluster Resource Manager bilancia tutti gli elementi in base alle normali regole.

Nell'esempio a destra, si supponga che Applicazione1 sia stata creata con le impostazioni seguenti:

- MinimumNodes impostato su due
- Una metrica dell'applicazione definita con le impostazioni seguenti:
  - NodeReservationCapacity pari a 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric riserva la capacità in due nodi per Applicazione1 e non consente ai servizi da Applicazione2 di usare tale capacità, anche se non sono presenti carichi usati dai servizi all'interno di Applicazione1 al momento. Questa capacità riservata dell'applicazione viene considerata usata e conta a fronte della capacità residua del nodo e del cluster.  La riserva viene sottratta immediatamente dalla capacità del cluster rimanente, tuttavia il consumo riservato viene sottratto dalla capacità di un nodo specifico solo quando almeno un oggetto del servizio viene posizionato su di esso. Questa prenotazione successiva consente un utilizzo ottimale delle risorse e flessibilità, dal momento che le risorse sono riservate nei nodi solo quando necessario.

## <a name="obtaining-the-application-load-information"></a>Ottenimento delle informazioni sul carico dell'applicazione
Per ogni applicazione con una Capacità dell'applicazione definita per una o più metriche è possibile ottenere le informazioni relative al carico aggregato segnalato dalle repliche dei suoi servizi.

Powershell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

La query ApplicationLoad restituisce le informazioni di base sulla capacità dell'applicazione che è stata specificata. Queste informazioni includono le informazioni sul numero minimo e massimo di nodi e sul numero attualmente occupato dall'applicazione. Sono inoltre incluse informazioni su ogni metrica di carico dell'applicazione, tra cui:

* Nome della metrica: nome della metrica.
* Capacità di prenotazione: capacità riservata nel cluster per questa applicazione.
* Carico dell'applicazione: carico totale delle repliche figlio dell'applicazione.
* Capacità dell'applicazione: valore massimo consentito di carico dell'applicazione.

## <a name="removing-application-capacity"></a>Rimozione della capacità dell'applicazione
Dopo aver impostato i parametri di capacità per un'applicazione, questi parametri possono essere rimossi usando le API di aggiornamento dell'applicazione o i cmdlet PowerShell. ad esempio:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Questo comando rimuove tutti i parametri di gestione della capacità dall'istanza dell'applicazione. Ciò include MinimumNodes, MaximumNodes e le metriche dell'applicazione, se presenti. L'effetto del comando è immediato. Una volta eseguito il comando, Cluster Resource Manager usa il comportamento predefinito per la gestione delle applicazioni. I parametri di capacità dell'applicazione possono essere specificati nuovamente tramite `Update-ServiceFabricApplication`/`System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Restrizioni relative alla capacità dell'applicazione
Esistono diverse restrizioni da rispettare per i parametri di capacità dell'applicazione. Se sono presenti errori di convalida non viene apportata alcuna modifica.

- Tutti i parametri di tipo integer devono essere numeri non negativi.
- MinimumNodes non deve essere mai maggiore di MaximumNodes.
- Se definite, le capacità per una metrica di carico devono rispettare queste regole:
  - La capacità di prenotazione del nodo non deve essere maggiore della capacità massima del nodo. Ad esempio, non è possibile limitare la capacità per la metrica "CPU" nel nodo a due unità e provare a prenotare tre unità in ogni nodo.
  - Se viene specificato MaximumNodes, il prodotto di MaximumNodes e della capacità massima del nodo non deve essere maggiore della capacità totale dell'applicazione. Si supponga, ad esempio, che la capacità massima del nodo per la metrica di carico "CPU" sia impostata su 8. Si supponga anche che il numero massimo di nodi sia impostato su 10. In questo caso, la capacità totale dell'applicazione deve essere superiore a 80 per la metrica di carico.

Le restrizioni vengono applicate sia durante la creazione dell'applicazione sia durante i suoi aggiornamenti.

## <a name="how-not-to-use-application-capacity"></a>Come usare la capacità dell'applicazione
- Non tentare di usare le funzionalità dei gruppi di applicazioni per vincolare l'applicazione a uno _specifico_ subset di nodi. In altre parole, è possibile specificare che l'applicazione venga eseguita su un massimo di cinque nodi, ma non indicare i cinque nodi del cluster specifici. È possibile vincolare un'applicazione a nodi specifici usando i vincoli di posizionamento per i servizi.
- Non usare la capacità dell'applicazione per garantire che due servizi della stessa applicazione vengano sempre posizionati sugli stessi nodi. Usare invece [affinità](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) o [vincoli di posizionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulla configurazione dei servizi, [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
- Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
- Partire dall'inizio e vedere l' [introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Per altre informazioni sul funzionamento generale delle metriche, vedere l'articolo sulle [metriche di carico di Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
- Cluster Resource Manager dispone di varie opzioni per descrivere il cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

