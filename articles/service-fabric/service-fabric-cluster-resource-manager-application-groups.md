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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: eef9cbca0479801cbac53ae1ae5713d4dac6a4bf
ms.openlocfilehash: 57525cd41d518b12f3f465d5dea7247659e3c8b6


---
# <a name="introduction-to-application-groups"></a>Introduzione ai gruppi di applicazioni
Cluster Resource Manager di Service Fabric generalmente gestisce le risorse del cluster distribuendo il carico (rappresentato tramite [Metriche](service-fabric-cluster-resource-manager-metrics.md)) in modo uniforme nell'intero cluster. Service Fabric gestisce anche la capacità dei nodi del cluster e il cluster nel suo complesso tramite il concetto di [capacità](service-fabric-cluster-resource-manager-cluster-description.md). Metriche e capacità rappresentano un'ottima soluzione per molti tipi di carichi di lavoro, ma i modelli che fanno largo uso di diverse istanze di applicazione di Service Fabric comportano a volte requisiti aggiuntivi. Alcuni requisiti aggiuntivi sono in genere i seguenti:

* Possibilità di riservare la capacità per i servizi di un'istanza di applicazione nel cluster
* Possibilità di limitare il numero totale di nodi in cui vengono eseguiti i servizi all'interno di un'applicazione
* Definizione delle capacità nell'istanza di applicazione stessa per limitare il numero o il consumo totale delle risorse dei servizi al suo interno

Per soddisfare questi requisiti, Cluster Resource Manager di Service Fabric supporta i gruppi di applicazioni.

## <a name="managing-application-capacity"></a>Gestione della capacità dell'applicazione
La capacità dell'applicazione può essere usata per limitare il numero di nodi occupati da un'applicazione. La capacità può anche limitare il carico di metriche di tali servizi delle applicazioni nei singoli nodi e in totale. Può anche essere usata per riservare risorse nel cluster per l'applicazione.

La capacità dell'applicazione può essere impostata per le nuove applicazioni al momento della creazione; può anche essere aggiornata per le applicazioni esistenti.

### <a name="limiting-the-maximum-number-of-nodes"></a>Limitazione del numero massimo di nodi
Il caso d'uso più semplice della capacità dell'applicazione è quando un'istanza di applicazione deve essere limitata a un numero massimo di nodi. Se non viene specificata alcuna capacità di applicazione, Cluster Resource Manager di Service Fabric consente di creare e posizionare i servizi in base alle normali regole (bilanciamento del carico o deframmentazione).

Nell'immagine seguente è mostrata un'istanza di applicazione con e senza un numero massimo di nodi definiti. Non è possibile sapere quali repliche o istanze di servizi specifici verranno posizionate insieme o quali nodi specifici verranno usati.

<center>
![Istanza di applicazione che definisce il numero massimo di nodi][Image1]
</center>

Nell'esempio a sinistra, l'applicazione non ha una capacità impostata e ha tre servizi. Cluster Resource Manager ha distribuito tutte le repliche tra sei nodi disponibili per ottenere il bilanciamento ottimale nel cluster. Nell'esempio a destra, notiamo la stessa applicazione limitata a tre nodi.

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
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>Metriche, carico e capacità dell'applicazione
I gruppi di applicazioni consentono anche di definire le metriche associate a una determinata istanza di applicazione, nonché la capacità dell'applicazione in relazione a tali metriche. Ciò consente di tener traccia del consumo di risorse da parte dei servizi all'interno dell'istanza di applicazione, di riservarlo e di limitarlo.

Per ogni metrica di applicazione, è possibile impostare due valori:

* **Capacità totale dell'applicazione**: rappresenta la capacità totale dell'applicazione per una particolare metrica. Cluster Resource Manager impedisce di creare nuovi servizi all'interno dell'istanza di applicazione che farebbero superare questo valore di carico totale. Si supponga, ad esempio, che l'istanza di applicazione abbia una capacità pari a 10 e il carico sia già pari a cinque. In questo caso la creazione di un servizio con un carico totale predefinito di 10 non verrebbe consentita.
* **Capacità massima del nodo**: specifica il carico totale massimo per le repliche dei servizi dell'applicazione in un singolo nodo. Se il carico totale nel nodo supera questa capacità, Cluster Resource Manager tenterà di trasferire le repliche ad altri nodi, in modo da rispettare il vincolo di capacità.

## <a name="reserving-capacity"></a>Riserva della capacità
I gruppi di applicazioni vengono comunemente usati anche per garantire che le risorse all'interno del cluster siano riservate per una determinata istanza di applicazione. Ciò si verifica anche se l'istanza di applicazione non contiene ancora servizi o anche se i gruppi non utilizzano ancora le risorse. Di seguito è illustrato il potenziale funzionamento.

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>Definizione di un numero minimo di nodi e prenotazione delle risorse
Per prenotare le risorse per un'istanza dell'applicazione è necessario definire due parametri aggiuntivi: *MinimumNodes* e *NodeReservationCapacity*

* **MinimumNodes**: è possibile specificare un numero massimo e un numero minimo di nodi in cui eseguire i servizi in un'applicazione. Questa impostazione definisce il numero di nodi minimo in cui riservare le risorse, garantendo capacità all'interno del cluster nell'ambito della creazione dell'istanza di applicazione.
* **NodeReservationCapacity**: questo parametro può essere definito per ogni metrica all'interno dell'applicazione. Definisce il carico di metriche riservato per l'applicazione su qualsiasi nodo in cui vengono collocate le repliche o le istanze dei servizi contenute.

Di seguito viene illustrato un esempio di come viene riservata la capacità:

<center>
![Istanze di applicazione che definiscono la capacità riservata][Image2]
</center>

Nell'esempio a sinistra, le applicazioni non hanno una capacità definita. Cluster Resource Manager bilancia tutti gli elementi in base alle normali regole.

Nell'esempio a destra, l'applicazione è stata creata con le impostazioni seguenti:

* MinimumNodes impostato su due
* MaximumNodes impostato su tre
* Una metrica dell'applicazione definita con le impostazioni seguenti:
  * NodeReservationCapacity pari a 20
  * MaximumNodeCapacity pari a 50
  * TotalApplicationCapacity pari a 100

Service Fabric riserva capacità in due nodi per l'applicazione blu e impedisce ai servizi delle altre istanze di applicazione nel cluster di utilizzarla. Questa capacità riservata dell'applicazione verrà considerata utilizzata e conteggiata a fronte della capacità residua del nodo e del cluster.

Quando viene creata un'applicazione con prenotazione, Cluster Resource Manager riserva una quantità di capacità pari a MinimumNodes * NodeReservationCapacity (per ogni metrica). Tuttavia, la capacità viene riservata in un nodo specifico quando sul nodo stesso viene posizionata almeno una replica. Questa prenotazione successiva consente un utilizzo ottimale delle risorse e flessibilità, dal momento che le risorse sono riservate nei nodi solo quando necessario.

## <a name="obtaining-the-application-load-information"></a>Ottenimento delle informazioni sul carico dell'applicazione
Per ogni applicazione con una capacità definita è possibile ottenere le informazioni relative al carico aggregato segnalato dalle repliche dei suoi servizi.

È ad esempio possibile recuperare il carico usando il cmdlet PowerShell seguente:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

La query ApplicationLoad restituisce le informazioni di base sulla capacità dell'applicazione che è stata specificata. Queste informazioni includono le informazioni sul numero minimo e massimo di nodi e sul numero attualmente occupato dall'applicazione. Sono inoltre incluse informazioni su ogni metrica di carico dell'applicazione, tra cui:

* Nome della metrica: nome della metrica.
* Capacità di prenotazione: capacità riservata nel cluster per questa applicazione.
* Carico dell'applicazione: carico totale delle repliche figlio dell'applicazione.
* Capacità dell'applicazione: valore massimo consentito di carico dell'applicazione.

## <a name="removing-application-capacity"></a>Rimozione della capacità dell'applicazione
Dopo aver impostato i parametri di capacità per un'applicazione, questi parametri possono essere rimossi usando le API di aggiornamento dell'applicazione o i cmdlet PowerShell. Ad esempio:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Questo comando rimuove tutti i parametri di capacità dall'applicazione. L'effetto del comando è immediato. Una volta eseguito il comando, Cluster Resource Manager tornerà al comportamento predefinito per la gestione delle applicazioni. I parametri di capacità dell'applicazione possono essere specificati nuovamente mediante Update-ServiceFabricApplication.

### <a name="restrictions-on-application-capacity"></a>Restrizioni relative alla capacità dell'applicazione
Esistono diverse restrizioni da rispettare per i parametri di capacità dell'applicazione. In caso di errori di convalida, la creazione e l'aggiornamento dell'applicazione verranno rifiutati con un errore e non verrà eseguita alcuna modifica.

* Tutti i parametri di tipo integer devono essere numeri non negativi.
* MinimumNodes non deve essere mai maggiore di MaximumNodes.
* Se definite, le capacità per una metrica di carico devono rispettare queste regole:
  * La capacità di prenotazione del nodo non deve essere maggiore della capacità massima del nodo. Ad esempio, non è possibile limitare la capacità per la metrica "CPU" nel nodo a due unità e provare a prenotare tre unità in ogni nodo.
  * Se viene specificato MaximumNodes, il prodotto di MaximumNodes e della capacità massima del nodo non deve essere maggiore della capacità totale dell'applicazione. Si supponga, ad esempio, che la capacità massima del nodo per la metrica di carico "CPU" sia impostata su&8;. Si supponga inoltre che il numero massimo di nodi sia impostato su&10;. In questo caso quindi la capacità totale dell'applicazione deve essere superiore a 80 per la metrica di carico.

Le restrizioni vengono applicate durante la creazione (sul lato client) e l'aggiornamento dell'applicazione (sul lato server).

## <a name="how-not-to-use-application-capacity"></a>Come usare la capacità dell'applicazione
* Non tentare di usare le funzionalità dei gruppi di applicazioni per vincolare l'applicazione a uno _specifico_ subset di nodi. In altre parole, è possibile specificare che l'applicazione venga eseguita su un massimo di cinque nodi, ma non indicare i cinque nodi del cluster specifici. È possibile vincolare un'applicazione a nodi specifici usando i vincoli di posizionamento per i servizi.
* Non usare la capacità dell'applicazione per garantire che due servizi della stessa applicazione vengano sempre posizionati uno accanto all'altro. È possibile garantire che i servizi siano eseguiti nello stesso nodo mediante l'affinità o vincoli di posizionamento sulla base di requisiti specifici.

## <a name="next-steps"></a>Passaggi successivi
* Per maggiori informazioni sulle altre opzioni disponibili per la configurazione dei servizi, consultare l'articolo relativo alle altre configurazioni disponibili per Cluster Resource Manager [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
* Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
* Partire dall'inizio e vedere l' [introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Per altre informazioni sul funzionamento generale delle metriche, vedere l'articolo sulle [metriche di carico di Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
* Cluster Resource Manager dispone di varie opzioni per descrivere il cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png



<!--HONumber=Jan17_HO1-->


