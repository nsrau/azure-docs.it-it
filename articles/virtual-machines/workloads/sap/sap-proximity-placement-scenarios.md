---
title: Gruppi di posizionamento di prossimità di Azure per le applicazioni SAP Documenti Microsoft
description: Descrive gli scenari di distribuzione SAP con i gruppi di posizionamento di prossimità di AzureDescribes SAP deployment scenarios with Azure proximity placement groups
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277605"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Gruppi di posizionamento di prossimità di Azure per una latenza di rete ottimale con le applicazioni SAPAzure proximity placement groups for optimal network latency with SAP applications
Le applicazioni SAP basate sull'architettura SAP NetWeaver o SAP S/4HANA sono sensibili alla latenza di rete tra il livello applicazione SAP e il livello di database SAP. Questa sensibilità è il risultato della maggior parte della logica di business in esecuzione nel livello dell'applicazione. Poiché il livello dell'applicazione SAP esegue la logica di business, invia query al livello di database a frequenza elevata, a una velocità di migliaia o decine di migliaia al secondo. Nella maggior parte dei casi, la natura di queste query è semplice. Spesso possono essere eseguiti sul livello di database in 500 microsecondi o meno.

Il tempo impiegato in rete per inviare una query di questo tipo dal livello applicazione al livello database e ricevere il set di risultati ha un impatto importante sul tempo necessario per eseguire i processi aziendali. Questa sensibilità alla latenza di rete è il motivo per cui è necessario ottenere una latenza di rete ottimale nei progetti di distribuzione SAP. Vedere [SAP Note #1100926 - Domande frequenti: Prestazioni di rete](https://launchpad.support.sap.com/#/notes/1100926/E) per linee guida su come classificare la latenza di rete.

In molte aree di Azure, il numero di data center è aumentato. Questa crescita è stata innescata anche dall'introduzione di zone di disponibilità. Allo stesso tempo, i clienti, in particolare per i sistemi SAP di fascia alta, utilizzano SKU VM più speciali nella famiglia M-Series o istanze di grandi dimensioni HANA. Questi tipi di macchine virtuali di Azure non sono disponibili in tutti i data center in un'area di Azure specifica. A causa di queste due tendenze, i clienti hanno sperimentato una latenza di rete che non è nell'intervallo ottimale. In alcuni casi, questa latenza determina prestazioni non ottimali dei sistemi SAP.

Per evitare questi problemi, Azure offre gruppi di [posizionamento di prossimità.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) Questa nuova funzionalità è già stata utilizzata per distribuire vari sistemi SAP. Per le restrizioni sui gruppi di posizionamento di prossimità, vedere l'articolo a cui si fa riferimento all'inizio di questo paragrafo. Questo articolo illustra gli scenari SAP in cui i gruppi di posizionamento di prossimità di Azure possono o devono essere usati.

## <a name="what-are-proximity-placement-groups"></a>Che cosa sono i gruppi di posizionamento di prossimità? 
Un gruppo di posizionamento di prossimità di Azure è un costrutto logico. Quando ne viene definito uno, viene associato a un'area di Azure e a un gruppo di risorse di Azure.When one is defined, it's bound to an Azure region and an Azure resource group. Quando le macchine virtuali vengono distribuite, un gruppo di posizionamento di prossimità fa riferimento a:When VMs are deployed, a a proximity placement group is referenced by:

- La prima macchina virtuale di Azure distribuita nel data center. È possibile considerare la prima macchina virtuale come una "VM di ambito" distribuita in un data center basato su algoritmi di allocazione di Azure che vengono eventualmente combinati con le definizioni utente per una zona di disponibilità specifica.
- Tutte le macchine virtuali successive distribuite che fanno riferimento al gruppo di posizionamento di prossimità, per inserire tutte le macchine virtuali di Azure distribuite successivamente nello stesso data center della prima macchina virtuale.

> [!NOTE]
> Se non è distribuito alcun hardware host in grado di eseguire un tipo di macchina virtuale specifico nel data center in cui è stata inserita la prima macchina virtuale, la distribuzione del tipo di macchina virtuale richiesta non riuscirà. Verrà visualizzato un messaggio di errore.

A un singolo gruppo di risorse di [Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) possono essere assegnati più gruppi di posizionamento di prossimità. Tuttavia, un gruppo di posizionamento di prossimità può essere assegnato a un solo gruppo di risorse di Azure.But a proximity placement group can be assigned to only one Azure resource group.

Quando utilizzi i gruppi di posizionamento di prossimità, tieni presente quanto segue:

- Quando si mira a ottenere prestazioni ottimali per il sistema SAP e ci si limita a un singolo data center di Azure per il sistema usando gruppi di posizionamento di prossimità, potrebbe non essere possibile combinare tutti i tipi di famiglie di macchine virtuali all'interno del gruppo di posizionamento. Queste limitazioni si verificano perché l'hardware host necessario per eseguire un determinato tipo di macchina virtuale potrebbe non essere presente nel data center in cui è stata distribuita la "VM con ambito" del gruppo di posizionamento.
- Durante il ciclo di vita di un sistema SAP di questo tipo, è possibile essere costretti a spostare il sistema in un altro data center. Questo spostamento potrebbe essere necessario se si decide che il livello HANA DBMS con scalabilità orizzontale deve, ad esempio, passare da quattro nodi a 16 nodi e non c'è capacità sufficiente per ottenere altre 12 macchine virtuali del tipo usato nel data center.
- A causa della rimozione delle autorizzazioni hardware, Microsoft potrebbe accumulare capacità per un tipo di macchina virtuale usato in un data center diverso, anziché quello usato inizialmente. In questo scenario, potrebbe essere necessario spostare tutte le macchine virtuali del gruppo di posizionamento di prossimità in un altro data center.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Gruppi di posizionamento di prossimità con sistemi SAP che usano solo macchine virtuali di AzureProximity placement groups with SAP systems that use only Azure VMs
La maggior parte delle distribuzioni di sistema SAP NetWeaver e S/4HANA in Azure non usa [istanze DI grandi dimensioni HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) Per le distribuzioni che non utilizzano istanze di grandi dimensioni HANA, è importante fornire prestazioni ottimali tra il livello dell'applicazione SAP e il livello DBMS. A tale scopo, definire un gruppo di posizionamento di prossimità di Azure solo per il sistema.

Nella maggior parte delle distribuzioni dei clienti, i clienti creano un singolo gruppo di risorse di [Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) per i sistemi SAP. In tal caso, esiste una relazione uno-a-uno tra, ad esempio, il gruppo di risorse di sistema ERP di produzione e il relativo gruppo di posizionamento di prossimità. In altri casi, i clienti organizzano i gruppi di risorse orizzontalmente e raccolgono tutti i sistemi di produzione in un singolo gruppo di risorse. In questo caso, si avrebbe una relazione uno-a-molti tra il gruppo di risorse per i sistemi SAP di produzione e diversi gruppi di posizionamento di prossimità per la produzione SAP ERP, SAP BW e così via.

Evitare di raggruppare più sistemi di produzione o non di produzione SAP in un singolo gruppo di posizionamento di prossimità. Quando un numero ridotto di sistemi SAP o un sistema SAP e alcune applicazioni circostanti devono avere una comunicazione di rete a bassa latenza, è consigliabile spostare questi sistemi in un unico gruppo di posizionamento di prossimità. È consigliabile evitare i pacchetti di sistemi perché più sistemi si raggruppano in un gruppo di posizionamento di prossimità, maggiori sono le probabilità:

- Che è necessario un tipo di macchina virtuale che non può essere eseguito nel data center specifico in cui è stato limitato il gruppo di posizionamento di prossimità.
- Che le risorse di macchine virtuali non mainstream, come le macchine virtuali serie M, potrebbero alla fine essere insoddisfatte quando è necessario più perché si aggiunge software a un gruppo di posizionamento di prossimità nel tempo.

Ecco come si presenta la configurazione ideale, come descritto:

![Gruppi di posizionamento di prossimità solo con macchine virtuali di AzureProximity placement groups with only Azure VMs](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

In questo caso, i singoli sistemi SAP vengono raggruppati in un gruppo di risorse ciascuno, con un gruppo di posizionamento di prossimità ciascuno. Non esiste alcuna dipendenza dall'utilizzo di configurazioni di scalabilità orizzontale o DBMS HANA.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Gruppi di posizionamento di prossimità e istanze di grandi dimensioni HANA
Se alcuni dei sistemi SAP si basano su [istanze di grandi dimensioni HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) per il livello dell'applicazione, è possibile che si verifichino miglioramenti significativi nella latenza di rete tra l'unità Istanze di grandi dimensioni HANA e le macchine virtuali di Azure quando si usano unità di istanze di grandi dimensioni HANA distribuite in [revisione 4 righe o timbri.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) Un miglioramento è che le unità HANA Large Instances, mentre vengono distribuite, vengono distribuite con un gruppo di posizionamento di prossimità. È possibile usare tale gruppo di posizionamento di prossimità per distribuire le macchine virtuali a livello di applicazione. Di conseguenza, tali macchine virtuali verranno distribuite nello stesso data center che ospita l'unità Istanze di grandi dimensioni HANA.

Per determinare se l'unità Istanze di grandi dimensioni HANA è distribuita in un timbro o una riga di revisione 4, vedere l'articolo Controllo delle istanze di [grandi dimensioni di Azure hanA tramite](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)il portale di Azure.To determine whether your HANA Large Instances unit is deployed in a Revision 4 stamp or row, check the article Azure HANA Large Instances control through Azure portal . Nella panoramica degli attributi dell'unità Istanze di grandi dimensioni HANA, è inoltre possibile determinare il nome del gruppo di posizionamento di prossimità perché è stato creato quando è stata distribuita l'unità Istanze di grandi dimensioni HANA. Il nome visualizzato nella panoramica degli attributi è il nome del gruppo di posizionamento di prossimità in cui distribuire le macchine virtuali a livello di applicazione.

Rispetto ai sistemi SAP che usano solo macchine virtuali di Azure, quando si usano istanze di grandi dimensioni HANA, si ha meno flessibilità nel decidere il numero di gruppi di risorse di [Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) da usare. Tutte le unità HANA Large Instances di un [tenant HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) sono raggruppate in un singolo gruppo di risorse, come descritto in questo [articolo.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal) A meno che non si distribuisca in tenant diversi per separare, ad esempio, sistemi di produzione e non di produzione o altri sistemi, tutte le unità di istanze di grandi dimensioni HANA verranno distribuite in un tenant di istanze di grandi dimensioni HANA. Questo tenant ha una relazione uno-a-uno con un gruppo di risorse. Ma verrà definito un gruppo di posizionamento di prossimità separato per ciascuna delle singole unità.

Di conseguenza, le relazioni tra i gruppi di risorse di Azure e i gruppi di posizionamento di prossimità per un singolo tenant saranno come illustrato di seguito:As a result, the relationships among Azure resource groups and proximity placement groups for a single tenant will be as shown here:

![Gruppi di posizionamento di prossimità e istanze di grandi dimensioni HANA](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Esempio di distribuzione con gruppi di posizionamento di prossimità
Di seguito sono riportati alcuni comandi di PowerShell che è possibile usare per distribuire le macchine virtuali con i gruppi di posizionamento di prossimità di Azure.Following are some PowerShell commands that you can use to deploy your VMs with Azure proximity placement groups.

Il primo passaggio, dopo l'accesso ad [Azure Cloud Shell,](https://azure.microsoft.com/features/cloud-shell/)consiste nel verificare se ci si trova nella sottoscrizione di Azure che si vuole usare per la distribuzione:

<pre><code>
Get-AzureRmContext
</code></pre>

Se è necessario passare a una sottoscrizione diversa, è possibile eseguire questo comando:If you need to change to a different subscription, you can do so by running this command:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Creare un nuovo gruppo di risorse di Azure eseguendo questo comando:Create a new Azure resource group by running this command:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Creare il nuovo gruppo di posizionamento di prossimità eseguendo questo comando:Create the new proximity placement group by running this command:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Distribuire la prima macchina virtuale nel gruppo di posizionamento di prossimità usando un comando simile al seguente:Deploy your first VM into the proximity placement group by using a command like this one:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Il comando precedente distribuisce una macchina virtuale basata su Windows.The preceding command deploys a Windows-based VM. Dopo che la distribuzione della macchina virtuale ha esito positivo, l'ambito del data center del gruppo di posizionamento di prossimità viene definito all'interno dell'area di Azure.After this VM deployment succeeds, the datacenter scope of the proximity placement group is defined within the Azure region. Tutte le distribuzioni di macchine virtuali successive che fanno riferimento al gruppo di posizionamento di prossimità, come illustrato nel comando precedente, verranno distribuite nello stesso data center di Azure, a condizione che il tipo di macchina virtuale possa essere ospitato nell'hardware inserito in tale data center e la capacità per tale tipo di macchina virtuale Disponibile.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Combinare set di disponibilità e zone di disponibilità con gruppi di posizionamento di prossimitàCombine availability sets and Availability zones with proximity placement groups
Uno degli svantaggi dell'utilizzo delle zone di disponibilità per le distribuzioni del sistema SAP è che non è possibile distribuire il livello dell'applicazione SAP usando i set di disponibilità all'interno della zona specifica. Si desidera che il livello dell'applicazione SAP venga distribuito nelle stesse zone del livello DBMS. Non è supportato fare riferimento a una zona di disponibilità e a un set di disponibilità durante la distribuzione di una singola macchina virtuale. Pertanto, in precedenza, si era costretti a distribuire il livello dell'applicazione facendo riferimento a una zona. Si è persa la possibilità di assicurarsi che le macchine virtuali a livello di applicazione siano state distribuite tra domini di aggiornamento e errore diversi.

Utilizzando i gruppi di posizionamento di prossimità, è possibile ignorare questa restrizione. Ecco la sequenza di distribuzione:Here's the deployment sequence:

- Creare un gruppo di posizionamento di prossimità.
- Distribuire la macchina virtuale di ancoraggio, in genere il server DBMS, facendo riferimento a una zona di disponibilità.
- Creare un set di disponibilità che fa riferimento al gruppo di prossimità di Azure.Create an availability set that references the Azure proximity group. (Vedere il comando più avanti in questo articolo.)
- Distribuire le macchine virtuali a livello di applicazione facendo riferimento al set di disponibilità e al gruppo di posizionamento di prossimità.

Anziché distribuire la prima macchina virtuale come illustrato nella sezione precedente, si fa riferimento a una zona di disponibilità e al gruppo di posizionamento di prossimità quando si distribuisce la macchina virtuale:Instead of deploying the first VM as demonstrated in the previous section, you reference an Availability zone and the proximity placement group when you deploy the VM:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Una corretta distribuzione di questa macchina virtuale ospiterebbe l'istanza di database del sistema SAP in un'unica zona di disponibilità. L'ambito del gruppo di posizionamento di prossimità è fisso a uno dei data center che rappresentano la zona di disponibilità definita.

Si supponga di distribuire le macchine virtuali dei servizi centrali nello stesso modo delle macchine virtuali DBMS, facendo riferimento alla stessa zona o zone e agli stessi gruppi di posizionamento di prossimità. Nel passaggio successivo, è necessario creare i set di disponibilità che si desidera utilizzare per il livello dell'applicazione del sistema SAP.

È necessario definire e creare il gruppo di posizionamento di prossimità. Il comando per la creazione del set di disponibilità richiede un riferimento aggiuntivo all'ID del gruppo di posizionamento di prossimità (non al nome). È possibile ottenere l'ID del gruppo di posizionamento di prossimità utilizzando questo comando:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Quando crei il set di disponibilità, devi considerare parametri aggiuntivi quando usi dischi gestiti (impostazione predefinita se non diversamente specificato) e gruppi di posizionamento di prossimità:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Idealmente, è consigliabile utilizzare tre domini di errore. Tuttavia, il numero di domini di errore supportati può variare da regione a regione. In questo caso, il numero massimo di domini di errore possibile per le aree specifiche è due. Per distribuire le macchine virtuali a livello di applicazione, è necessario aggiungere un riferimento al nome del set di disponibilità e al nome del gruppo di posizionamento di prossimità, come illustrato di seguito:To deploy your application layer VMs, you need to add a reference to your availability set name and the proximity placement group name, as shown here:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Il risultato di questa distribuzione è:
- Un livello DBMS e servizi centrali per il sistema SAP che si trova in una zona di disponibilità o in una zona di disponibilità specifica.
- Livello di applicazione SAP che si trova tramite set di disponibilità negli stessi data center di Azure della macchina virtuale o delle macchine virtuali DBMS.

> [!NOTE]
> Poiché si distribuisce una macchina virtuale DBMS in una zona e la seconda macchina virtuale DBMS in un'altra zona per creare una configurazione a disponibilità elevata, è necessario un gruppo di posizionamento di prossimità diverso per ognuna delle zone. Lo stesso vale per qualsiasi set di disponibilità usato.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Spostare un sistema esistente in gruppi di posizionamento di prossimità
Se si dispone già di sistemi SAP distribuiti, è possibile ottimizzare la latenza di rete di alcuni dei sistemi critici e individuare il livello dell'applicazione e il livello DBMS nello stesso datacenter. Per spostare le macchine virtuali di un set di disponibilità di Azure completo in un gruppo di posizionamento di prossimità esistente con ambito già definito, è necessario arrestare tutte le macchine virtuali del set di disponibilità e assegnare il set di disponibilità al gruppo di posizionamento di prossimità esistente tramite il portale di Azure, PowerShell o CLI. Se si desidera spostare una macchina virtuale che non fa parte di un set di disponibilità in un gruppo di posizionamento di prossimità esistente, è sufficiente arrestare la macchina virtuale e assegnarla a un gruppo di posizionamento di prossimità esistente. 


## <a name="next-steps"></a>Passaggi successivi
Consulta la documentazione:

- [Carichi di lavoro SAP in Azure: elenco di controllo per la pianificazione e la distribuzione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Anteprima: Distribuire macchine virtuali a gruppi di posizionamento di prossimità usando l'interfaccia della riga di comando di AzurePreview: Deploy VMs to proximity placement groups using Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Anteprima: Distribuire macchine virtuali a gruppi di posizionamento di prossimità tramite PowerShellPreview: Deploy VMs to proximity placement groups using PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Considerazioni per la distribuzione DBMS di Macchine virtuali di Azure per carichi di lavoro SAPConsiderations for Azure Virtual Machines DBMS deployment for SAP workloads](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

