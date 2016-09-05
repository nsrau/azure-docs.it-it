<properties
   pageTitle="Cluster Resource Manager di Service Fabric - Affinità | Microsoft Azure"
   description="Informazioni generali sulla configurazione dell'affinità per i servizi di Service Fabric"
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

# Configurazione e utilizzo dell'affinità del servizio in Service Fabric

Il controllo di affinità è disponibile principalmente per facilitare la transizione di grandi applicazioni monolitiche verso ambienti cloud e di microservizi. Ciò premesso, può anche essere usato in alcuni casi come ottimizzazione legittima per migliorare le prestazioni dei servizi, sebbene possa avere effetti collaterali.

Si supponga di voler importare in Service Fabric un'applicazione di grandi dimensioni o che non era stata progettata nell'ottica dei microservizi. Questa transizione è comune e ci è stata manifestata da molti clienti interni ed esterni. Per iniziare, si solleva l'intera applicazione nell'ambiente, la si impacchetta e rende attiva. Quindi, la si suddivide in vari servizi più piccoli che comunicano tra loro.

Poi si verifica un inconveniente. L'inconveniente rientra, in genere, in una di queste categorie:

1. Una parte del componente X dell'app monolitica aveva una dipendenza non documentata dal componente Y che abbiamo appena convertito in servizi separati. Poiché questi servizi sono in esecuzione su nodi diversi del cluster, vengono interrotti.
2.	Questi elementi comunicano tramite (named pipe locali| memoria condivisa | file su disco), ma è necessario poterli aggiornare in modo indipendente per velocizzarli. La dipendenza rigida verrà rimossa in un secondo momento.
3.	Tutto va bene, ma si scopre che questi due componenti comunicano molto tra loro o sono sensibili alle prestazioni. Quando sono stati spostati in servizi separati, le prestazioni globali hanno subito dell'applicazione ne hanno risentito pesantemente o hanno aumentato la latenza. Di conseguenza, l'applicazione globale non soddisfa le aspettative.

In questi casi si non vogliamo perdere il lavoro di ricostruzione e non vogliamo tornare all'app monolitica, ma è necessario un certo senso di posizionamento. Questo verrà mantenuto fino a quando non sarà possibile riprogettare i componenti perché funzionino in modo naturale come servizi o fino a quando non si sarà in grado di risolvere le aspettative delle prestazioni in un altro modo, se possibile.

Cosa fare? Si può provare ad attivare il servizio di affinità.

## Come configurare l'affinità
Per impostare l'affinità, è necessario definire una relazione di affinità tra due servizi. Si tratta di fare in modo che un servizio "punti" a un altro servizio affinché il primo possa essere eseguito solo se anche il secondo è in esecuzione. A volte, si fa riferimento all'affinità come a una relazioni padre-figlio, in cui l'elemento figlio punta all'elemento padre. L'affinità garantisce che le repliche o le istanze di un servizio vengano inserite negli stessi nodi in cui risiedono le repliche o le istanze di un altro servizio.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Diverse opzioni di affinità
L'affinità è rappresentata tramite vari possibili schemi di correlazione e ha due modalità diverse. La modalità di affinità più comune è la cosiddetta NonAlignedAffinity. Nella modalità NonAlignedAffinity le repliche o le istanze dei diversi servizi vengono inserite negli stessi nodi. L'altra modalità è AlignedAffinity. La modalità AlignedAffinity viene usata solo con i servizi con stato. La configurazione di due servizi con stati per l'allineamento dell'affinità garantisce che i primari di tali servizi vengano inseriti negli stessi nodi. La configurazione consente anche di inserire ogni coppia di secondari dei servizi negli stessi nodi. È possibile anche configurare una relazione NonAlignedAffinity per i servizi con stato, sebbene questa pratica sia meno comune. Per la relazione NonAlignedAffinity, le diverse repliche dei due servizi con stato saranno collocate sugli stessi nodi, ma non verrà eseguito alcun tentativo di allineamento di primarie o secondarie.

![Modalità di affinità e loro effetti][Image1]  

### Stato desiderato del massimo sforzo
Esistono alcune differenze tra il servizio di affinità e le architetture monolitiche. Molte di queste dipendono dal fatto che una relazione di affinità è migliore. I servizi in una relazione di affinità sono entità profondamente diverse che possono avere esito negativo ed essere spostate in modo indipendente. Sono disponibili anche le cause per cui una relazione di affinità potrebbe interrompersi. Ad esempio, i limiti di capacità per cui solo alcuni degli oggetti del servizio nella relazione di affinità possono contenere un determinato nodo. In questi casi, anche se è disponibile una relazione di affinità, non verrà applicata a causa di altri vincoli. Se è possibile applicare tutti gli altri vincoli e l'affinità in un secondo momento, la violazione del vincolo di affinità verrà corretta in automatico.

### Modelli a catena o a stella
Ad oggi non siamo in grado di modellare catene di relazioni di affinità. Ciò significa che un servizio che è un elemento figlio in una relazione di affinità non potrà essere un elemento padre in un'altra relazione di affinità. Se si desidera modellare questo tipo di relazione, è necessario modellarla in modo efficace a forma di stella, invece di una catena. A questo scopo, l'elemento figlio più basso sarebbe imparentato con il padre dell'elemento figlio "medio". A seconda della disposizione dei servizi può essere necessario creare un servizio "segnaposto" come padre per più elementi figlio.

![Modelli a catena o a stella nel contesto delle relazioni di affinità][Image2]  

Un altro aspetto da notare circa le relazioni di affinità attuali è che sono direzionali. Ciò significa che la regola "affinità" impone solo che l'elemento figlio si trovi nella stessa posizione dell'elemento padre. Se ad esempio l'elemento padre improvvisamente esegue il failover a un altro nodo, Cluster Resource Manager non ritiene che sia presente un problema effettivo fino a quando non nota che l'elemento figlio non si trova insieme a un elemento padre; la relazione non viene applicata immediatamente.

### Supporto del partizionamento
L'ultimo aspetto da notare è che le relazioni di affinità non sono supportate nelle situazioni in cui l'elemento padre è partizionato. È possibile che in futuro venga attivato il supporto per questo aspetto, ma attualmente non è previsto.

## Passaggi successivi
- Per maggiori informazioni sulle altre opzioni disponibili per la configurazione dei servizi, consultare l'articolo relativo alle altre configurazioni disponibili per Cluster Resource Manager [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
- Numerosi casi in cui gli utenti usano l'affinità, ad esempio per limitare un piccolo set di computer e provare ad aggregare il carico di una raccolta di servizi, sono supportati meglio dai gruppi di applicazioni. Vedere [Gruppi di applicazioni](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=AcomDC_0824_2016-->