<properties
   pageTitle="Aumentare o ridurre le istanze del cluster di Service Fabric | Microsoft Azure"
   description="Aumentare o ridurre le istanze di un cluster di Service Fabric per rispondere alla domanda impostando le regole di ridimensionamento automatico per ogni tipo di nodo o set di scalabilità di VM."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="chackdan"/>


# Aumentare o ridurre le istanze del cluster di Service Fabric con le regole di ridimensionamento

I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Service Fabric è configurato come un set di scalabilità di macchine virtuali separato. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. Per altre informazioni, vedere il documento sui [tipi di nodo di Service Fabric](service-fabric-cluster-nodetypes.md). Poiché i tipi di nodi di Service Fabric nel cluster sono costituiti da set di scalabilità di VM nel back-end, si dovranno configurare regole di ridimensionamento automatico per ogni tipo di nodo o set di scalabilità di VM.

>[AZURE.NOTE] È necessario che nella sottoscrizione sia incluso un numero di core sufficienti per aggiungere le nuove VM che costituiranno il cluster. Non esiste attualmente una funzionalità di convalida del modello quindi, se viene raggiunto uno dei limiti della quota, verrà visualizzato un errore in fase di distribuzione.

## Scegliere il tipo di nodo o il set di scalabilità di VM da ridimensionare

Attualmente non è possibile specificare le regole di ridimensionamento automatico per i set di scalabilità di VM tramite il portale, quindi si userà Azure PowerShell 1.0 o versione successiva per elencare i tipi di nodo e aggiungervi quindi le regole di ridimensionamento automatico.

Per ottenere l'elenco dei set di scalabilità di VM che costituiscono il cluster, eseguire i cmdlet seguenti:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## Impostare le regole di ridimensionamento automatico per il tipo di nodo o il set di scalabilità di VM

Se nel cluster sono disponibili più tipi di nodi, è necessario eseguire questa operazione per ogni tipo di nodo o set di scalabilità di VM per cui si vuole aumentare o ridurre le istanze. Considerare il numero di nodi che essere disponibili prima di configurare il ridimensionamento automatico. Il numero minimo di nodi necessari per il tipo di nodo primario è determinato dal livello di affidabilità scelto. Per altre informazioni, vedere la sezione relativa ai [livelli di affidabilità](service-fabric-cluster-capacity.md).

>[AZURE.NOTE]  La riduzione delle istanze del tipo di nodo primario, impostando un valore inferiore al numero minimo, renderà il cluster instabile o lo arresterà. In questo caso, è possibile che si verifichi una perdita di dati per le applicazioni e per i servizi di sistema.

Attualmente la funzionalità di ridimensionamento automatico non è determinata dai carichi che le applicazioni segnalano a Service Fabric. Al momento. il ridimensionamento automatico che si ottiene dipende esclusivamente dai contatori delle prestazioni generati da ognuna delle istanze del set di scalabilità di VM.

Seguire le istruzioni [per configurare il ridimensionamento automatico per ogni set di scalabilità di VM](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

>[AZURE.NOTE] In uno scenario di riduzione delle istanze, a meno che il tipo di nodo non abbia un livello di durabilità Gold o Silver, sarà necessario chiamare il cmdlet [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) con il nome del nodo appropriato.

## Comportamenti che è possibile osservare in Service Fabric Explorer

Quando si aumentano le istanze di un cluster, Service Fabric Explorer riflette il numero di nodi, ovvero le istanze del set di scalabilità di VM, che fanno parte del cluster. Quando tuttavia si riducono le istanze di un cluster, il nodo o l'istanza della VM rimossa viene ancora visualizzata con uno stato non integro, a meno di chiamare il cmdlet [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx) con il nome del nodo appropriato.

Ecco la spiegazione di questo comportamento.

I nodi elencati in Service Fabric Explorer riflettono le informazioni a disposizione dei servizi di sistema di Service Fabric, in particolare FM, circa il numero di nodi presenti nel cluster attualmente o in precedenza. Quando si riducono le istanze del set di scalabilità di VM, la VM viene eliminata, ma il servizio di sistema FM continua a ritenere che il nodo, precedentemente mappato alla VM eliminata, verrà ripristinato. Service Fabric Explorer continua quindi a visualizzare tale nodo, anche se lo stato di integrità è sconosciuto o di errore.

Per assicurarsi che un nodo venga rimosso quando si rimuove una VM, sono disponibili due opzioni:

1) Scegliere un livello di durabilità Gold o Silver (disponibile a breve) per i tipi di nodo del cluster, che fornirà l'integrazione dell'infrastruttura. In questo caso i nodi saranno rimossi automaticamente dallo stato dei servizi di sistema (FM) quando si riducono le istanze. Vedere [i dettagli sui livelli di durabilità](service-fabric-cluster-capacity.md).

2) Dopo aver ridotto le istanze della VM, si dovrà chiamare il [cmdlet Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

>[AZURE.NOTE] I cluster di Service Fabric richiedono che un certo numero di nodi sia attivo in ogni momento allo scopo di mantenere la disponibilità e lo stato, ossia per "mantenere il quorum". Di conseguenza, non è in genere sicuro arrestare tutte le macchine virtuali del cluster senza avere prima eseguito un [backup completo dello stato](service-fabric-reliable-services-backup-restore.md).

## Passaggi successivi
Per altre informazioni sulla pianificazione della capacità del cluster, l'aggiornamento di un cluster e il partizionamento dei servizi, vedere gli articoli seguenti:

- [Considerazioni sulla pianificazione della capacità del cluster di Service Fabric](service-fabric-cluster-capacity.md)
- [Aggiornare un cluster di Service Fabric](service-fabric-cluster-upgrade.md)
- [Partizionare Reliable Services di Service Fabric](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

<!---HONumber=AcomDC_0608_2016-->