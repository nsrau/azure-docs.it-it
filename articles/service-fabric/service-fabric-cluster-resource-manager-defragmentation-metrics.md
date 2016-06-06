<properties
   pageTitle="Deframmentazione delle metriche in Azure Service Fabric | Microsoft Azure"
   description="Informazioni generali sull'uso della deframmentazione o della compressione come strategia per le metriche in Service Fabric"
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
   ms.date="05/20/2016"
   ms.author="masnider"/>

# Deframmentazione delle metriche e del carico in Service Fabric
Cluster Resource Manager di Service Fabric si occupa principalmente del bilanciamento del carico in termini di distribuzione del carico stesso, assicurandosi che tutti i nodi del cluster vengano usati allo stesso modo. Si tratta in genere del layout più intelligente e sicuro per la sopravvivenza agli errori perché garantisce che qualsiasi anomalia non interessi gran parte di un carico di lavoro. Cluster Resource Manager di Service Fabric supporta anche una strategia diversa, ovvero la deframmentazione. La deframmentazione in genere implica che invece di tentare di distribuire l'utilizzo di una metrica all'interno del cluster, si cerca di favorirne un consolidamento effettivo. La relativa implementazione non è stata in realtà troppo difficile: invece di aumentare il punteggio per una soluzione basata sulla riduzione al minimo della deviazione standard media del carico per una determinata metrica, è stato diminuito il carico. In altre parole, si inizia a ottimizzare l'incremento di deviazione anziché tentare di ridurlo al minimo. Ma perché si dovrebbe desiderare questa strategia?

Se si ripartisce il carico uniformemente tra i nodi nel cluster, verranno usate alcune delle risorse offerte dai nodi. In genere ciò non rappresenta un problema, ma a volte alcuni carichi di lavoro creano servizi eccezionalmente grandi, destinando ad esempio il 75% - 95% delle risorse di un nodo a un singolo servizio. Ciò non rappresenta un problema dal momento che Resource Manager rileva, in fase di creazione, la necessità di riorganizzare il cluster per far posto a questo carico di lavoro di grandi dimensioni e consentirne l'implementazione. Tuttavia, nel frattempo il carico di lavoro deve rimanere in attesa.

Dato che la pianificazione di nuovi carichi di lavoro è in genere almeno in parte sensibile alla latenza, se non si fa qualcosa di diverso si rischia di non riuscire a rispettare i contratti di servizio in caso di una gran quantità di servizi e il relativo stato da spostare. In effetti, misurando i tempi di creazione nelle simulazioni basate su dati cluster reali, si è notato che se i servizi fossero abbastanza grandi e il cluster fosse equamente utilizzato, la creazione di quei servizi di grandi dimensioni risulterebbe rallentata, problema che potrebbe essere risolto con l'introduzione della nozione di metriche di deframmentazione.

Proprio come il processo di creazione o accesso a un file potrebbe essere rallentato in caso di frammentazione del disco rigido del computer e velocizzato tramite la deframmentazione dell'unità, è possibile configurare le metriche di deframmentazione per permettere a Resource Manager di concentrare il carico dei servizi in pochi nodi per lasciare (quasi) sempre spazio per servizi di dimensioni ancora maggiori, consentendone la rapida creazione. La maggior parte degli utenti non avrà bisogno di questa soluzione perché i servizi sono in genere di piccole dimensioni e quindi non sarà difficile trovare spazio per ospitarli. Per servizi di grandi dimensioni che devono essere creati rapidamente e per i quali si possano accettare altri compromessi, le metriche di deframmentazione sono tuttavia un'ottima soluzione.

Il diagramma di seguito offre una rappresentazione visiva di due cluster diversi, uno deframmentato e uno non deframmentato. Nel caso bilanciato, considerare i movimenti che sarebbero necessari per posizionare uno degli oggetti di servizio più grandi, se ne venisse creato uno nuovo, rispetto al cluster deframmentato, in cui potrebbe essere posizionato immediatamente nei nodi 4 o 5.

![Confronto tra cluster bilanciati e deframmentati][Image1]

## Vantaggi e svantaggi della deframmentazione
Quali sono quindi questi altri compromessi concettuali? Si consiglia di misurare accuratamente i carichi di lavoro prima di attivare le metriche di deframmentazione. Ecco una tabella riepilogativa degli aspetti da considerare:

| Vantaggi della deframmentazione | Svantaggi della deframmentazione |
|----------------------|----------------------|
|Consente di creare servizi di grandi dimensioni più rapidamente |	Concentra i carichi in meno nodi, aumentando il conflitto
|Consente di ridurre lo spostamento dei dati durante la creazione | Eventuali errori possono impattare più servizi e causare una maggiore varianza
|Consente una descrizione completa dei requisiti e il recupero di spazio |	Configurazione di gestione delle risorse complessiva più complessa

È possibile combinare metriche normali e deframmentate nello stesso cluster. Resource Manager farà il possibile per offrire all'utente un layout che consolidi quante più metriche di deframmentazione possibili mentre tenta di distribuire il resto. I risultati esatti ottenuti dipenderanno dal numero di metriche di bilanciamento rispetto al numero di metriche di deframmentazione, i relativi pesi e così via.

## Configurazione delle metriche di deframmentazione
La configurazione delle metriche di deframmentazione è una decisione globale nel cluster ed è possibile selezionare metriche singole per la deframmentazione:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## Passaggi successivi
- Cluster Resource Manager dispone di molte opzioni per la descrizione del cluster. Per altre informazioni a riguardo vedere l'articolo [Descrizione di un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png

<!---HONumber=AcomDC_0525_2016-->