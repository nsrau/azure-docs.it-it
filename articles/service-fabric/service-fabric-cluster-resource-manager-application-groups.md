<properties
   pageTitle="Cluster Resource Manager di Service Fabric - Gruppi di applicazioni | Microsoft Azure"
   description="Informazioni generali sulla funzionalità dei gruppi di applicazioni in Cluster Resource Manager di Service Fabric "
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# Introduzione ai gruppi di applicazioni
Cluster Resource Manager di Service Fabric gestisce le risorse cluster distribuendo il carico, rappresentato tramite metriche, in modo uniforme nell'intero cluster. Service Fabric gestisce anche la capacità dei nodi del cluster e il cluster nel suo complesso tramite il concetto di capacità. Si tratta di un'ottima soluzione per molti tipi di carichi di lavoro, ma i modelli che fanno largo uso di diverse istanze di applicazione di Service Fabric comportano a volte requisiti aggiuntivi. Alcuni requisiti aggiuntivi sono in genere i seguenti:

- Possibilità di riservare la capacità per i servizi di un'istanza di applicazione in un certo numero di nodi
- Possibilità di limitare il numero totale di nodi per l'esecuzione di un determinato set di servizi all'interno di un'applicazione
- Definizione delle capacità nell'istanza di applicazione stessa per limitare il numero o il consumo totale delle risorse dei servizi al suo interno

Per soddisfare questi requisiti è stato sviluppato il supporto per i cosiddetti gruppi di applicazioni.

## Gestione della capacità dell'applicazione
La capacità dell'applicazione può essere usata per limitare il numero di nodi su cui si estende un'applicazione, nonché il carico metrico totale delle istanze dell'applicazione nei singoli nodi. Può anche essere usata per riservare risorse nel cluster per l'applicazione.

La capacità dell'applicazione può essere impostata per le nuove applicazioni al momento della creazione; può anche essere aggiornata per le applicazioni esistenti che sono state create senza specificare la capacità.

### Limitazione del numero massimo di nodi
Il caso d'uso più semplice della capacità dell'applicazione è quando un'istanza di applicazione deve essere limitata a un numero massimo di nodi. Se non viene specificata una capacità dell'applicazione, Cluster Resource Manager di Service Fabric creerà le istanze delle repliche in base alle normali regole di bilanciamento del carico o deframmentazione; questo in genere significa che i servizi verranno distribuiti tra tutti i nodi disponibili del cluster oppure su un numero di nodi arbitrario più piccolo se è abilitata la deframmentazione.

L'immagine seguente illustra il potenziale posizionamento di un'istanza dell'applicazione senza il numero massimo di nodi definito e quindi la stessa applicazione con un numero massimo di nodi impostato. Si noti che non è possibile sapere quali repliche o istanze di servizi specifici verranno posizionate insieme.

![Istanza dell'applicazione che definisce il numero massimo di nodi][Image1]

Nell'esempio a sinistra, l'applicazione non ha una capacità impostata e ha tre servizi. Cluster Resource Manager ha deciso logicamente di distribuire tutte le repliche tra sei nodi disponibili per ottenere il bilanciamento ottimale nel cluster. Nell'esempio a destra è visibile la stessa applicazione vincolata in tre nodi e il punto in cui Cluster Resource Manager di Service Fabric ha ottenuto il bilanciamento ottimale per le repliche dei servizi dell'applicazione.

Il parametro che gestisce questo comportamento è denominato MaximumNodes. Questo parametro può essere impostato durante la creazione dell'applicazione o aggiornato per un'istanza di applicazione già in esecuzione; in questo caso, Cluster Resource Manager di Service Fabric limiterà le repliche dei servizi dell'applicazione al numero massimo di nodi definito.

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

## Metriche, carico e capacità dell'applicazione
I gruppi di applicazioni consentono anche di definire le metriche associate a una determinata istanza dell'applicazione, nonché la capacità dell'applicazione in relazione a tali metriche. È ad esempio possibile definire la possibilità di creare qualunque numero di servizi

Per ogni metrica sono presenti 2 valori che è possibile impostare per descrivere la capacità dell'istanza dell'applicazione:

-	Capacità totale dell'applicazione: rappresenta la capacità totale dell'applicazione per una particolare metrica. Cluster Resource Manager di Service Fabric tenterà di limitare la somma dei carichi metrici dei servizi dell'applicazione al valore specificato. Se i servizi dell'applicazione usano già il carico fino a questo limite, Cluster Resource Manager di Service Fabric impedirà la creazione di eventuali nuovi servizi o partizioni che comporterebbero il superamento di questo limite.
-	Capacità massima del nodo: specifica il carico totale massimo per le repliche dei servizi dell'applicazione in un singolo nodo. Se il carico totale nel nodo supera questa capacità, Cluster Resource Manager di Service Fabric tenterà di trasferire le repliche in altri nodi, in modo da rispettare il vincolo di capacità.

## Riserva della capacità
Un altro uso comune dei gruppi di applicazioni è garantire che le risorse all'interno del cluster siano riservate per una determinata istanza dell'applicazione, anche se l'istanza dell'applicazione non contiene ancora servizi o anche se i servizi non utilizzano ancora le risorse. Di seguito è illustrato il potenziale funzionamento.

### Definizione di un numero minimo di nodi e prenotazione delle risorse
La prenotazione delle risorse per un'istanza dell'applicazione richiede la definizione di due parametri aggiuntivi: *MinimumNodes* e *NodeReservationCapacity*

- MinimumNodes: così come si definisce un numero massimo di nodi per l'esecuzione dei servizi di un'applicazione, è anche possibile specificare il numero minimo di nodi per l'esecuzione dell'applicazione. Questa impostazione definisce in modo efficace il numero di nodi minimo per le risorse, garantendo capacità all'interno del cluster nell'ambito della creazione dell'istanza dell'applicazione.
- NodeReservationCapacity: il parametro NodeReservationCapacity può essere definito per ogni metrica all'interno dell'applicazione. Definisce il carico metrico riservato per l'applicazione su qualsiasi nodo in cui vengono collocate le repliche o le istanze dei servizi contenuti.

Di seguito viene illustrato un esempio di prenotazione della capacità:

![Istanze dell'applicazione che definiscono la capacità riservata][Image2]

Nell'esempio a sinistra, le applicazioni non hanno una capacità definita. Cluster Resource Manager di Service Fabric bilancia le repliche dei servizi figlio e le istanze dell'applicazione insieme a quelle di altri servizi all'esterno dell'applicazione, per garantire il bilanciamento del cluster.

Nell'esempio a destra, si supponga che l'applicazione sia stata creata con un parametro MinimumNodes impostato su 2, MaximumNodes impostato su 3 e una metrica di applicazione definita con una prenotazione di 20, capacità massima in un nodo pari a 50 e una capacità totale dell'applicazione di 100. Service Fabric riserverà la capacità in due nodi per l'applicazione blu e non consentirà ad altre repliche nel cluster di utilizzare tale capacità. Questa capacità riservata dell'applicazione verrà considerata utilizzata e conteggiata a fronte della capacità residua del cluster.

Quando viene creata un'applicazione con prenotazione, la capacità riservata da Cluster Resource Manager corrisponderà al parametro MinimumNodes * NodeReservationCapacity del cluster, ma non verrà riservata capacità in nodi specifici fino a quando non verranno create e posizionate le repliche dei servizi dell'applicazione. Ciò consente flessibilità perché i nodi vengono scelti per le nuove repliche solo al momento della creazione. La capacità viene riservata in un nodo specifico quando almeno una replica viene posizionata su di esso.

## Ottenimento delle informazioni sul carico dell'applicazione
Per ogni applicazione con una capacità definita è possibile ottenere le informazioni relative al carico aggregato segnalato dalle repliche dei suoi servizi. Service Fabric offre query PowerShell e di API gestite per questo scopo.

È ad esempio possibile recuperare il carico usando il cmdlet PowerShell seguente:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

L'output della query conterrà le informazioni di base sulla capacità specificata per l'applicazione, ad esempio numero minimo e massimo di nodi. Saranno anche disponibili informazioni sul numero di nodi che l'applicazione usa attualmente. Per ogni metrica di carico saranno disponibili informazioni sugli elementi seguenti:
- Nome della metrica: nome della metrica.
-	Capacità di prenotazione: capacità riservata nel cluster per questa applicazione.
-	Carico dell'applicazione: carico totale delle repliche figlio dell'applicazione.
-	Capacità dell'applicazione: valore massimo consentito di carico dell'applicazione.

## Rimozione della capacità dell'applicazione
Dopo aver impostato i parametri di capacità per un'applicazione, questi parametri possono essere rimossi usando le API di aggiornamento dell'applicazione o i cmdlet PowerShell. ad esempio:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Questo comando rimuoverà tutti i parametri di capacità dall'applicazione e Cluster Resource Manager di Service Fabric inizierà a trattare questa applicazione come qualsiasi altra applicazione nel cluster che non ha questi parametri definiti. L'effetto del comando è immediato e Cluster Resource Manager eliminerà tutti i parametri di capacità per l'applicazione; per specificare di nuovo i parametri sarà necessario chiamare le API di aggiornamento dell'applicazione con i parametri idonei.

## Restrizioni relative alla capacità dell'applicazione
Esistono diverse restrizioni da rispettare per i parametri di capacità dell'applicazione. In caso di errori di convalida, la creazione e l'aggiornamento dell'applicazione verranno rifiutati con un errore. Tutti i parametri di tipo integer devono essere numeri non negativi. Per i singoli parametri, le restrizioni sono le seguenti:

-	MinimumNodes non deve essere mai maggiore di MaximumNodes.
-	Se definite, le capacità per una metrica di carico devono rispettare queste regole:
  - La capacità di prenotazione del nodo non deve essere maggiore della capacità massima del nodo. Non è ad esempio possibile limitare la capacità per la metrica "CPU" nel nodo a 2 unità e provare a prenotare 3 unità in ogni nodo.
  - Se viene specificato MaximumNodes, il prodotto di MaximumNodes e della capacità massima del nodo non deve essere maggiore della capacità totale dell'applicazione. Se ad esempio si imposta la capacità massima del nodo per la metrica di carico "CPU" su 8 e si imposta il numero massimo di nodi su 10, la capacità totale dell'applicazione dovrà essere maggiore di 80 per questa metrica di carico.

Le restrizioni vengono applicate durante la creazione (sul lato client) e l'aggiornamento dell'applicazione (sul lato server). Durante la creazione, questo è un esempio di chiara violazione dei requisiti perché MaximumNodes < MinimumNodes e il comando avrà esito negativo nel client ancora prima che la richiesta venga inviata anche al cluster di Service Fabric:

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

Un esempio di aggiornamento non valido è il seguente. Se per un'applicazione esistente si aggiorna il numero massimo di nodi su un valore, l'aggiornamento andrà a buon fine:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

Sarà quindi possibile provare ad aggiornare il numero minimo di nodi:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

Il client non ha un contesto sufficiente per l'applicazione in modo da consentire all'aggiornamento di passare al cluster di Service Fabric. Nel cluster, tuttavia, Service Fabric convaliderà il nuovo parametro con i parametri esistenti e l'operazione di aggiornamento non riuscirà perché il valore del numero minimo di nodi è maggiore del valore del numero massimo di nodi. In questo caso, i parametri di capacità dell'applicazione rimarranno invariati.

Queste restrizioni vengono applicate affinché Cluster Resource Manager possa applicare il posizionamento ottimale per le repliche dei servizi delle applicazioni.

## Come usare la capacità dell'applicazione

-	Non usare la capacità dell'applicazione per vincolare l'applicazione a uno specifico sottoinsieme di nodi: nonostante Service Fabric garantisca il rispetto del numero massimo di nodi per ogni applicazione con capacità specificata, gli utenti non possono decidere in quali nodi verranno create le istanze. È possibile ottenere questo risultato usando vincoli di posizionamento per i servizi.
-	Non usare la capacità dell'applicazione per garantire che due servizi della stessa applicazione vengano sempre posizionati uno accanto a altro. È possibile ottenere questo risultato usando una relazione di affinità tra servizi e l'affinità può essere limitata solo ai servizi che devono essere effettivamente posizionati insieme.

## Passaggi successivi
- Per maggiori informazioni sulle altre opzioni disponibili per la configurazione dei servizi, consultare l'articolo relativo alle altre configurazioni disponibili per Cluster Resource Manager [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
- Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
- Partire dall'inizio e vedere l'[introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Per altre informazioni sul funzionamento generale delle metriche, vedere l'articolo sulle [metriche di carico di Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
- Cluster Resource Manager dispone di molte opzioni per la descrizione del cluster. Per altre informazioni a riguardo vedere l'articolo [Descrizione di un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)


[Image1]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

<!---HONumber=AcomDC_0824_2016-->