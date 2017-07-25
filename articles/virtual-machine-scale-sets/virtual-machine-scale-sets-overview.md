---
title: "Panoramica dei set di scalabilità di macchine virtuali di Azure | Microsoft Docs"
description: "Informazioni sui set di scalabilità di macchine virtuali di Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 8b2fbc230faf01797109114d6ebdffe5ec50e48b
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017


---
<a id="what-are-virtual-machine-scale-sets-in-azure" class="xliff"></a>

# Informazioni sui set di scalabilità di macchine virtuali in Azure
I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire un set di VM identiche. Dato che tutte le VM hanno la stessa configurazione, i set di scalabilità sono progettati per supportare l'effettiva scalabilità automatica, senza che sia necessario il provisioning preventivo delle VM. Ciò semplifica la creazione di servizi su larga scala destinati a carichi di lavoro Big Compute, Big Data e in contenitori.

Per le applicazioni che richiedono la scalabilità (aumento e riduzione di istanze e capacità) delle risorse di calcolo, le operazioni di ridimensionamento vengono bilanciate in modo implicito tra domini di errore e domini di aggiornamento. Per un'introduzione aggiuntiva ai set di scalabilità, vedere l'[annuncio nel blog di Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Per altre informazioni sui set di scalabilità, guardare questi video:

* [Mark Russinovich talks Azure scale sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich illustra i set di scalabilità di Azure)  
* [Set di scalabilità di macchine virtuali con Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

<a id="creating-and-managing-scale-sets" class="xliff"></a>

## Creazione e gestione dei set di scalabilità
È possibile creare un set di scalabilità nel [portale di Azure](https://portal.azure.com) selezionando **Nuovo** e digitando **scalabilità** nella barra di ricerca. Nei risultati verrà visualizzato **Set di scalabilità di macchine virtuali**. A questo punto è possibile compilare i campi obbligatori per personalizzare e distribuire il set di scalabilità. Nel portale sono disponibili anche opzioni per impostare regole di base di scalabilità automatica in base all'utilizzo della CPU.

I set di scalabilità possono essere definiti e distribuiti con modelli JSON e [API REST](https://msdn.microsoft.com/library/mt589023.aspx), esattamente come le singole VM di Azure Resource Manager. È quindi possibile usare qualsiasi metodo di distribuzione Azure Resource Manager standard. Per altre informazioni sui modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md).

Un set di modelli di esempio per i set di scalabilità di macchine virtuali è disponibile nel [repository GitHub dedicato ai modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates). Cercare i modelli contenenti **vmss** nel titolo.

Per gli esempi di modelli di avvio rapido, un pulsante "Distribuisci in Azure" nel file Leggimi di ogni modello consente di accedere alla funzionalità di distribuzione del portale. Per distribuire il set di scalabilità, fare clic sul pulsante e quindi immettere tutti i parametri richiesti nel portale. 

<a id="scaling-a-scale-set-out-and-in" class="xliff"></a>

## Aumento e riduzione delle istanze di un set di scalabilità
È possibile modificare la capacità di un set di scalabilità nel portale di Azure facendo clic sulla sezione **Ridimensionamento** in **Impostazioni**. 

Per modificare la capacità del set di scalabilità dalla riga di comando, usare il comando **scale** nell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-cli). Per impostare un set di scalabilità su una capacità di 10 VM, ad esempio, usare questo comando:

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Per impostare il numero di VM in un set di scalabilità con PowerShell, usare il comando **Update-AzureRmVmss**:

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Per aumentare o ridurre il numero di macchine virtuali in un set di scalabilità con un modello di Azure Resource Manager, modificare la proprietà **capacity** e ridistribuire il modello. Questa semplicità facilita l'integrazione della funzionalità di scalabilità automatica di Azure nei set di scalabilità o la scrittura di un livello di scalabilità personalizzato se è necessario definire eventi di ridimensionamento personalizzati non supportati da tale funzionalità. 

Se si ridistribuisce un modello di Azure Resource Manager per modificare la capacità, è possibile definire un modello molto più piccolo che include solo il pacchetto di proprietà **SKU** con la capacità aggiornata. Per un esempio, vedere [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).

<a id="autoscale" class="xliff"></a>

## Autoscale

Un set di scalabilità può essere facoltativamente configurato con impostazioni di scalabilità automatica quando viene creato nel portale di Azure. Il numero di VM può quindi essere aumentato o ridotto in base all'utilizzo medio della CPU. 

Molti modelli di set di scalabilità inclusi nei [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates) definiscono impostazioni di scalabilità automatica. È anche possibile aggiungere impostazioni di scalabilità automatica a un set di scalabilità esistente. Questo script di Azure PowerShell, ad esempio, aggiunge la scalabilità automatica in base alla CPU a un set di scalabilità:

```PowerShell

$subid = "yoursubscriptionid"
$rgname = "yourresourcegroup"
$vmssname = "yourscalesetname"
$location = "yourlocation" # e.g. southcentralus

$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionValue 1
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator LessThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Decrease -ScaleActionValue 1
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "autoprofile1"
Add-AzureRmAutoscaleSetting -Location $location -Name "autosetting1" -ResourceGroup $rgname -TargetResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -AutoscaleProfiles $profile1
```

Un elenco delle metriche valide su cui basare la scalabilità è disponibile in [Metriche supportate con il monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md), sotto il titolo "Microsoft.Compute/virtualMachineScaleSets". Sono disponibili anche opzioni di scalabilità automatica più avanzate, che includono la scalabilità automatica in base alla pianificazione e l'uso di webhook per l'integrazione con sistemi di avvisi.

<a id="monitoring-your-scale-set" class="xliff"></a>

## Monitoraggio del set di scalabilità
Il [portale di Azure](https://portal.azure.com) visualizza un elenco dei set di scalabilità e le relative proprietà. Il portale supporta anche operazioni di gestione, che possono essere eseguite sia sui set di scalabilità che sulle singole VM all'interno di un set, e offre un grafico dell'utilizzo delle risorse personalizzabile. 

Se è necessario visualizzare o modificare la definizione JSON sottostante di una risorsa di Azure, è possibile usare anche [Esplora risorse di Azure](https://resources.azure.com). I set di scalabilità sono una risorsa del provider di risorse di Azure Microsoft.Compute e possono essere visualizzati da questo sito espandendo i collegamenti seguenti:

**Sottoscrizioni** > **sottoscrizione dell'utente** > **Gruppi di risorse** > **Provider** > **Microsoft.Compute** > **virtualMachineScaleSets** > **set di scalabilità dell'utente** > e così via

<a id="scale-set-scenarios" class="xliff"></a>

## Scenari dei set di scalabilità
Questa sezione contiene un elenco di alcuni scenari tipici dei set di scalabilità. Questi scenari vengono usati anche da alcuni servizi di Azure di livello superiore, come Batch, Service Fabric e Servizio contenitore.

* **Connessione a istanze del set di scalabilità con RDP o SSH**: un set di scalabilità viene creato in una rete virtuale e alle singole VM del set non vengono allocati indirizzi IP pubblici per impostazione predefinita. Questo criterio evita le spese e il sovraccarico di gestione associati all'allocazione di indirizzi IP pubblici distinti a tutti i nodi della griglia di calcolo. Se sono necessarie connessioni esterne dirette alle VM del set di scalabilità è possibile configurare un set di scalabilità per l'assegnazione automatica di indirizzi IP pubblici a nuove VM. In alternativa è possibile connettersi a queste VM da altre risorse della rete virtuale alle quali è possibile allocare indirizzi IP pubblici, ad esempio servizi di bilanciamento del carico e macchine virtuali autonome. 
* **Connessione alle VM con regole NAT**: è possibile creare un indirizzo IP pubblico, assegnarlo a un servizio di bilanciamento del carico e definire un pool NAT in ingresso. Con queste azioni viene eseguito il mapping delle porte nell'indirizzo IP a una porta di una VM del set di scalabilità. Ad esempio:
  
  | Sorgente | Porta di origine | Destination | Porta di destinazione |
  | --- | --- | --- | --- |
  |  IP pubblico |Porta 50000 |vmss\_0 |Porta 22 |
  |  IP pubblico |Porta 50001 |vmss\_1 |Porta 22 |
  |  IP pubblico |Porta 50002 |vmss\_2 |Porta 22 |
  
   In [questo esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat) vengono definite regole NAT per abilitare una connessione SSH a ogni VM di un set di scalabilità usando un solo indirizzo IP pubblico.
  
   [Questo esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat) esegue la stessa operazione con RDP e Windows.
* **Connessione alle VM con un "jumpbox"**: se si creano un set di scalabilità e una VM autonoma nella stessa rete virtuale, la VM autonoma e una VM del set di scalabilità possono connettersi tra loro usando i rispettivi indirizzi IP interni, definiti dalla rete virtuale o dalla subnet. Se si crea un indirizzo IP pubblico e lo si assegna alla VM autonoma, è possibile connettersi alla VM autonoma usando RDP o SSH e quindi connettersi da tale macchina virtuale alle istanze del set di scalabilità. Si noterà quindi che un semplice set di scalabilità è intrinsecamente più sicuro di una semplice VM autonoma con un indirizzo IP pubblico nella configurazione predefinita.
  
   [Questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox), ad esempio, distribuisce un set di scalabilità semplice con una VM autonoma. 
* **Bilanciamento del carico nelle istanze del set di scalabilità**: per trasmettere dati a un cluster di elaborazione di VM con un approccio "round robin", è possibile configurare un servizio di bilanciamento del carico di Azure con regole di livello 4. Si possono definire probe per verificare che l'applicazione sia in esecuzione effettuando il ping delle porte con un protocollo, un intervallo e un percorso di richiesta specificati. Il [gateway applicazione di Azure](https://azure.microsoft.com/services/application-gateway/) supporta anche set di scalabilità, insieme a scenari di bilanciamento del carico più sofisticati e di livello 7.
  
   [Questo esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) crea un set di scalabilità che esegue server Web Apache e usa un servizio di bilanciamento del carico per bilanciare il carico ricevuto da ogni VM. Esaminare il tipo di risorsa Microsoft.Network/loadBalancers nonché networkProfile ed extensionProfile in virtualMachineScaleSet.

   Questi esempi [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway) e [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) usano il gateway applicazione.  

* **Distribuzione di un set di scalabilità come cluster di elaborazione in uno strumento di gestione cluster PaaS**: i set di scalabilità sono talvolta descritti come un ruolo di lavoro di prossima generazione. Nonostante sia una descrizione valida, rischia di creare confusione tra le funzionalità dei set di scalabilità e quelle di Servizi cloud di Azure. In un certo senso, i set di scalabilità offrono un effettivo ruolo di lavoro o una risorsa del ruolo di lavoro. Sono una risorsa di calcolo generalizzata indipendente dalla piattaforma o dal runtime, personalizzabile e integrata nel servizio IaaS di Azure Resource Manager.
  
   Un ruolo di lavoro di Servizi cloud è limitato in termini di supporto per la piattaforma o il runtime (sono supportate solo immagini della piattaforma Windows), ma include anche servizi come lo scambio di indirizzi VIP, le impostazioni di aggiornamento configurabili e le impostazioni specifiche della distribuzione del runtime o delle app. Questi servizi non sono *ancora* disponibili nei set di scalabilità o vengono forniti da altri servizi PaaS di livello superiore come Azure Service Fabric. I set di scalabilità possono essere considerati come un'infrastruttura che supporta PaaS. Soluzioni PaaS come [Service Fabric](https://azure.microsoft.com/services/service-fabric/) si basano su questa infrastruttura.
  
   In [questo esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) di questo approccio, [Servizio contenitore di Azure](https://azure.microsoft.com/services/container-service/) distribuisce un cluster basato su set di scalabilità con un agente di orchestrazione contenitore.

<a id="scale-set-performance-and-scale-guidance" class="xliff"></a>

## Indicazioni sulla scalabilità e le prestazioni dei set di scalabilità
* In un set di scalabilità sono supportate fino a 1.000 VM. Se si creano e si caricano immagini di VM personalizzate, il limite è 100. Per le considerazioni da tenere presenti per usare set di scalabilità di grandi dimensioni, vedere [Uso di set di scalabilità di macchine virtuali di grandi dimensioni](virtual-machine-scale-sets-placement-groups.md).
* Non è necessario creare in precedenza account di archiviazione di Azure per usare i set di scalabilità. I set di scalabilità supportano Azure Managed Disks, che evita i problemi di prestazioni associati al numero di dischi per account di archiviazione. Per altre informazioni, vedere [Set di scalabilità di macchine virtuali di Azure e dischi gestiti](virtual-machine-scale-sets-managed-disks.md).
* Per tempi di provisioning delle VM più rapidi e prevedibili e prestazioni di I/O migliori, valutare la possibilità di usare Archiviazione Premium di Azure invece di Archiviazione di Azure.
* Il numero di VM che è possibile creare è limitato dalla quota di core nell'area in cui si esegue la distribuzione. Potrebbe essere necessario contattare l'assistenza clienti per richiedere l'aumento del limite della quota di calcolo anche se l'attuale limite di core da usare con Servizi cloud di Azure è elevato. Per informazioni sulla quota, eseguire il comando `azure vm list-usage` dell'interfaccia della riga di comando di Azure. In alternativa, eseguire il comando `Get-AzureRmVMUsage` di PowerShell.

<a id="frequently-asked-questions-for-scale-sets" class="xliff"></a>

## Domande frequenti sui set di scalabilità
**D.** Quante VM si possono includere in un set di scalabilità?

**R.** Un set di scalabilità può includere da 0 a 1.000 VM basate su immagini della piattaforma oppure da 0 a 100 VM basate su immagini personalizzate. 

**D.** I dischi dati sono supportati nei set di scalabilità?

**R.** Sì. Un set di scalabilità può definire una configurazione dei dischi dati collegati che si applica a tutte le VM del set. Per altre informazioni, vedere l'articolo relativo a [set di scalabilità di Azure e dischi dati collegati](virtual-machine-scale-sets-attached-disks.md). Le altre opzioni per l'archiviazione dei dati includono:

* File di Azure (unità condivise SMB)
* Unità del sistema operativo
* Unità temporanea (locale, non supportata da Archiviazione di Azure)
* Servizio dati di Azure (ad esempio, tabelle di Azure e BLOB di Azure)
* Servizio dati esterno (ad esempio, un database remoto)

**D.** Quali aree di Azure supportano i set di scalabilità?

**R.** Tutte le aree supportano i set di scalabilità.

**D.** Come si crea un set di scalabilità con un'immagine personalizzata?

**R.** Creare un disco gestito basato sul disco rigido virtuale dell'immagine personalizzata e fare riferimento a tale disco gestito nel modello del set di scalabilità. Per un esempio, vedere [qui](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**D.** Se si riduce la capacità del set di scalabilità da 20 a 15, quali VM vengono rimosse?

**R.** Le macchine virtuali vengono rimosse dal set di scalabilità in modo uniforme tra domini di aggiornamento e domini di errore per ottimizzare la disponibilità. Le VM con ID più elevato vengono rimosse per prime.

**D.** Cosa accade se successivamente si aumenta la capacità da 15 a 18?

**R.** Se si aumenta la capacità a 18, vengono create 3 nuove VM. Ogni volta, l'ID istanza della VM viene ottenuto incrementando il valore più elevato precedente (ad esempio 20, 21, 22). Le VM vengono bilanciate tra domini di errore e domini di aggiornamento.

**D.** Quando si usano più estensioni in un set di scalabilità, è possibile imporre una sequenza di esecuzione?

**R.** Non direttamente. Per l'estensione customScript, tuttavia, lo script potrebbe attendere il completamento di un'altra estensione. Indicazioni aggiuntive per la sequenziazione delle estensioni sono disponibili nel post di blog [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)(Sequenziazione delle estensioni nei set di scalabilità di macchine virtuali di Azure).

**D.** I set di scalabilità si integrano con i set di disponibilità di Azure?

**R.** Sì. Un set di scalabilità è un set di disponibilità implicito con 5 domini di errore e 5 domini di aggiornamento. I set di scalabilità di più di 100 VM si estendono su più *gruppi di posizionamento*, equivalenti a più set di disponibilità. Per altre informazioni sui gruppi di posizionamento, vedere [Uso di set di scalabilità di macchine virtuali di grandi dimensioni](virtual-machine-scale-sets-placement-groups.md). Un set di disponibilità di macchine virtuali può trovarsi nella stessa rete virtuale di un set di scalabilità di macchine virtuali. Una configurazione comune consiste nell'inserire le VM del nodo di controllo, che spesso richiedono una configurazione univoca, in un set di disponibilità e i nodi di dati nel set di scalabilità.

Per altre domande e risposte sui set di scalabilità, vedere [Domande frequenti sui set di scalabilità di macchine virtuali di Azure](virtual-machine-scale-sets-faq.md).

