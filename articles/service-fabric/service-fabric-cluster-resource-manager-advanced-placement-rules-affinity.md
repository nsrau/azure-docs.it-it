<properties
   pageTitle="Cluster Resource Manager di Service Fabric - Affinità | Microsoft Azure"
   description="Panoramica su altre politiche e regole per i servizi di Service Fabric"
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
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Configurazione e utilizzo dell'affinità del servizio in Service Fabric

Il concetto di affinità è uno dei tanti che, almeno a prima vista, non ha molto senso per un ambiente di microservizi. Perché in effetti non ha molto senso in un ambiente di microservizi. Il controllo di affinità è disponibile principalmente per facilitare la transizione di grandi applicazioni monolitiche tradizionali verso ambienti cloud e di microservizi.

Si supponga di voler importare in Service Fabric un'applicazione di grandi dimensioni o che non era stata progettata nell'ottica dei microservizi. Si tratta di un'esigenza piuttosto comune che ci è stata manifestata da molti clienti interni ed esterni. Per iniziare, si solleva l'intera applicazione nell'ambiente, la si impacchetta e rende attiva. Quindi, la si suddivide in vari servizi più piccoli che comunicano tra loro.

Poi si verifica un inconveniente. L'inconveniente rientra, in genere, in una di queste categorie:

1. Si scopre che il componente X dell'app monolitica aveva una dipendenza non documentata dal componente Y che abbiamo appena convertito in servizio e spostato altrove nel cluster. Il legame ora è stato interrotto.
2.	Questi elementi comunicano tramite (named pipe locali| memoria condivisa | file su disco), ma è necessario poterli aggiornare in modo indipendente per velocizzarli.
3.	Tutto va bene, ma si scopre che questi due componenti comunicano molto tra loro o sono sensibili alle prestazioni e pertanto, quando sono stati spostati in servizi separati, le prestazioni ne hanno risentito pesantemente.

In tutti questi casi, non si vuole perdere il lavoro di refactoring eseguito né tornare al monolite, ma è necessario ricomporre insieme tutti gli elementi in una configurazione che sembri "locale" fino a quando il problema non sarà stato risolto.

Cosa fare? Si può provare ad attivare il servizio di affinità.

## Funzionamento dell'affinità
Per impostare l'affinità, è necessario definire una relazione di affinità tra due servizi. In genere, si tratta di fare in modo che un servizio "punti" a un altro servizio affinché il primo possa essere eseguito solo se anche il secondo è in esecuzione. A volte, si fa riferimento a queste situazione come a una relazioni padre-figlio, in cui l'elemento figlio punta all'elemento padre. Questo espediente garantisce che le repliche o le istanze di un servizio vengano inserite negli stessi nodi in cui risiedono le repliche o le istanze del servizio collegato da una relazione di affinità.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Diverse opzioni di affinità
L'affinità è rappresentata tramite vari possibili schemi di correlazione e ha due modalità diverse. La modalità di affinità più comune è la cosiddetta NonAlignedAffinity. Nella modalità NonAlignedAffinity le repliche o le istanze dei diversi servizi vengono inserite negli stessi nodi. L'altra modalità è AlignedAffinity. La modalità AlignedAffinity viene utilizzata con i servizi con stato. Configurando una relazione di AlignedAffinity tra due servizi con stato si garantisce che gli elementi primari di tali servizi vengano inseriti negli stessi nodi e che ogni coppia di elementi secondari venga anch'essa inserita negli stessi nodi. È possibile anche configurare una relazione NonAlignedAffinity per i servizi con stato, sebbene questa pratica sia meno comune. In questo caso, si otterrebbe più o meno lo stesso comportamento che si osserva per i servizi senza stato: le diverse repliche dei due servizi con stato verrebbero collocate sugli stessi nodi, ma non verrebbe eseguito alcun tentativo per garantire che gli elementi primari e secondari venissero collocati sugli stessi nodi.

![Modalità di affinità e loro effetti][Image1]

### Stato desiderato del massimo sforzo
Esistono alcune differenze tra il servizio di affinità e le architetture monolitiche. Il tutto si riduce al fatto che una relazione di affinità rappresenta il massimo sforzo, poiché si tratta sostanzialmente di collegare tra loro servizi diversi in cui si possono verificare, ad esempio, degli errori in modo indipendente. Altri fattori possono provocare la separazione delle diverse repliche del servizio, ad esempio i limiti di capacità.


### Modelli a catena o a stella
Ad oggi non siamo in grado di modellare catene di relazioni di affinità. Ciò significa che un servizio che è un elemento figlio in una relazione di affinità non potrà essere un elemento padre in un'altra relazione di affinità. Ne consegue che, se si desidera modellare questo tipo di relazione, sarà necessario scegliere un modello a stella anziché un modello a catena, in cui il genitore dell'elemento figlio inferiore è anche genitore dell'elemento figlio "intermedio".

![Modelli a catena o a stella nel contesto delle relazioni di affinità][Image2]

Un altro aspetto da notare circa le relazioni di affinità attuali è che sono direzionali. Questo aspetto è particolare, ma significa che la regola di "affinità" impone solo che l'elemento figlio risieda dove si trova l'elemento padre. Se l'elemento padre è improvvisamente soggetto a failover su un altro nodo o a qualsiasi altra azione limitata che ne determina lo spostamento, Resource Manager non rileva alcun problema fino a quando non nota che l'elemento figlio non risiede dove si trova l'elemento padre. Ciò significa che la relazione non viene applicata immediatamente.

### Supporto del partizionamento
L'ultimo aspetto da notare è che le relazioni di affinità non sono supportate nelle situazioni in cui l'elemento padre è partizionato. È possibile che in futuro venga attivato il supporto per questo aspetto, ma attualmente non è previsto.

## Passaggi successivi
- Per maggiori informazioni sulle altre opzioni disponibili per la configurazione dei servizi, consultare l'articolo relativo alle altre configurazioni disponibili di Cluster Resource Manager [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=AcomDC_0316_2016-->