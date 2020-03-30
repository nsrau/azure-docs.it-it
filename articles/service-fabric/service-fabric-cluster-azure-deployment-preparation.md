---
title: Pianificare una distribuzione cluster di Azure Service FabricPlan an Azure Service Fabric cluster deployment
description: Informazioni sulla pianificazione e la preparazione per una distribuzione del cluster di Service Fabric di produzione in Azure.Learn about planning and preparing for a production Service Fabric cluster deployment to Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1762a6975448301957579b3437a8af5c89b3accd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193477"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Pianificare e preparare una distribuzione di clusterPlan and prepare for a cluster deployment

La pianificazione e la preparazione per la distribuzione di un cluster di produzione è molto importante.  Ci sono molti fattori da considerare.  In questo articolo vengono illustrati i passaggi per preparare la distribuzione del cluster.

## <a name="read-the-best-practices-information"></a>Leggi le informazioni sulle best practice
Per gestire correttamente le applicazioni e i cluster di Azure Service Fabric, è consigliabile eseguire operazioni per ottimizzare l'affidabilità dell'ambiente di produzione.  Per altre informazioni, vedere Procedure consigliate per le applicazioni e i cluster di [Service Fabric](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Selezionare il sistema operativo per il cluster
Service Fabric permette di creare cluster di Service Fabric in qualsiasi VM o computer con Windows Server o Linux in esecuzione.  Prima di distribuire il cluster, è necessario scegliere il sistema operativo: Windows o Linux.  Ogni nodo (macchina virtuale) nel cluster esegue lo stesso sistema operativo, non è possibile combinare macchine virtuali Windows e Linux nello stesso cluster.

## <a name="capacity-planning"></a>Pianificazione della capacità
La pianificazione della capacità è un passaggio importante per qualsiasi distribuzione di produzione. Di seguito sono elencati alcuni aspetti da considerare nell'ambito di questo processo.

* Il numero iniziale di tipi di nodo per il cluster 
* Le proprietà di ogni tipo di nodo (dimensione, numero di istanze, primaria, con connessione Internet, numero di macchine virtuali e così via)
* Caratteristiche di affidabilità e durabilità del cluster

### <a name="select-the-initial-number-of-node-types"></a>Selezionare il numero iniziale di tipi di nodo
Prima di tutto è necessario stabilire per che cosa verrà usato il cluster che si sta creando. Che tipo di applicazioni si prevede di distribuire nel cluster? L'applicazione ha più servizi, alcuni dei quali devono essere pubblici o per Internet? I servizi (che costituiscono l'applicazione) hanno esigenze diverse per l'infrastruttura, ad esempio cicli di CPU più elevati o una quantità maggiore di RAM? Un cluster di Service Fabric può essere costituito da più di un tipo di nodo: un tipo di nodo primario e uno o più tipi di nodo non primari. Di ogni tipo di nodo viene eseguito il mapping a un set di scalabilità di macchine virtuali. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. [Le proprietà dei nodi e][placementconstraints] i vincoli di posizionamento possono essere impostati per vincolare servizi specifici a tipi di nodo specifici.  Per ulteriori informazioni, vedere [Il numero di tipi di nodo necessari al cluster per iniziare con](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Selezionare le proprietà del nodo per ogni tipo di nodoSelect node properties for each node type
I tipi di nodo definiscono lo SKU, il numero e le proprietà della macchina virtuale nel set di scalabilità associato.

La dimensione minima delle macchine virtuali per ogni tipo di nodo è determinata dal livello di [durabilità][durability] scelto per il tipo di nodo.

Il numero minimo delle macchine virtuali per il tipo di nodo primario è determinato dal [livello di affidabilità][reliability] scelto.

Vedere i consigli minimi per i [tipi di nodo primario,](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance)i carichi di lavoro con stato su tipi di nodo [non primari](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)e i carichi di lavoro senza stato nei tipi di nodo [non primari.](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads)

Qualsiasi numero di nodi superiore al numero minimo di nodi deve essere basato sul numero di repliche dell'applicazione o dei servizi che si desidera eseguire in questo tipo di nodo.  [La pianificazione della capacità per](service-fabric-capacity-planning.md) le applicazioni di Service Fabric consente di stimare le risorse necessarie per eseguire le applicazioni. È sempre possibile scalare il cluster verso l'alto o verso il basso in un secondo momento per regolare la modifica del carico di lavoro dell'applicazione. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Usare dischi del sistema operativo effimeri per i set di scalabilità delle macchine virtualiUse ephemeral OS disks for virtual machine scale sets

I dischi del sistema *operativo effimeri* vengono creati nella macchina virtuale (VM) locale e non vengono salvati in Archiviazione di Azure remota. Sono consigliati per tutti i tipi di nodo Service Fabric (primario e secondario), perché rispetto ai dischi del sistema operativo persistente tradizionali, i dischi del sistema operativo effimeri:

* Ridurre la latenza di lettura/scrittura sul disco del sistema operativo
* Abilitare operazioni di ripristino/ricreazione dei nodi di reimpostazione/riapplicazione dell'immagine più velociEnable faster reset/reimage
* Riduci i costi complessivi (i dischi sono gratuiti e non comportano costi di archiviazione aggiuntivi)

I dischi del sistema operativo effimeri non sono una funzionalità specifica di Service Fabric, ma piuttosto una funzionalità dei set di *scalabilità* delle macchine virtuali di Azure mappati ai tipi di nodo dell'infrastruttura del servizio. Il loro utilizzo con Service Fabric richiede quanto segue nel modello di Azure Resource Manager del cluster:Using them with Service Fabric requires the following in your cluster Azure Resource Manager template:

1. Verificare che i tipi di nodo specifichino [le dimensioni supportate](../virtual-machines/windows/ephemeral-os-disks.md) delle macchine virtuali di Azure per i dischi del sistema operativo effimero e che le dimensioni della macchina virtuale dispongano di dimensioni della cache sufficienti per supportare le dimensioni del disco del sistema operativo (vedere *la nota* più avanti). Per esempio:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Assicurarsi di selezionare una dimensione della macchina virtuale con una dimensione della cache uguale o maggiore della dimensione del disco del sistema operativo della macchina virtuale stessa, altrimenti la distribuzione di Azure potrebbe generare un errore (anche se viene inizialmente accettata).

2. Specificare una versione del`vmssApiVersion`set `2018-06-01` di scalabilità di macchine virtuali ( ) di o versione successiva:Specify a virtual machine scale set version ( ) of or later:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. Nella sezione del set di scalabilità `Local` della `diffDiskSettings`macchina virtuale del modello di distribuzione specificare l'opzione per:

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

Per altre info e altre opzioni di configurazione, vedere Dischi del sistema operativo effimeri per macchine virtuali di AzureFor more info and further configuration options, see [Ephemeral OS disks for Azure VMs](../virtual-machines/windows/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Selezionare i livelli di durata e affidabilità per il cluster
Il livello di durabilità viene usato per indicare al sistema i privilegi delle VM rispetto all'infrastruttura di Azure sottostante. Nel tipo di nodo primario questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di VM (ad esempio il riavvio di una VM, il re-imaging di una VM o la migrazione di una VM) che hanno effetto sui requisiti relativi al quorum per i servizi di sistema e i servizi con stato. Nei tipi di nodo non primari questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di macchina virtuale (ad esempio, il riavvio di una VM, il re-imaging di una VM e la migrazione di una VM) che hanno effetto sui requisiti relativi al quorum per i servizi con stato.  Per vantaggi dei diversi livelli e consigli su quale livello utilizzare e quando, vedere [Le caratteristiche di durabilità del cluster][durability].

Il livello di affidabilità viene usato per impostare il numero di repliche dei servizi di sistema che si vuole eseguire in questo cluster nel tipo di nodo primario. I servizi di sistema nel cluster sono tanto più affidabili quanto più elevato è il numero di repliche.  Per vantaggi dei diversi livelli e consigli su quale livello utilizzare e quando, vedere [Caratteristiche di affidabilità del cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Abilitare il proxy inverso e/o il DNS
I servizi che si connettono tra loro in un cluster possono in genere accedere direttamente agli endpoint di altri servizi perché i nodi di un cluster si trovano nella stessa rete locale. Per semplificare la connessione tra i servizi, Service Fabric fornisce servizi aggiuntivi: un [servizio DNS](service-fabric-dnsservice.md) e un servizio [proxy inverso.](service-fabric-reverseproxy.md)  Entrambi i servizi possono essere abilitati durante la distribuzione di un cluster.

Poiché molti servizi, in particolare i servizi containerizzati, possono avere un nome URL esistente, essere in grado di risolverli utilizzando il protocollo DNS standard (anziché il protocollo Naming Service) è conveniente, soprattutto negli scenari di "lift and shift" dell'applicazione. Questo è esattamente ciò che fa il servizio DNS. Consente di eseguire il mapping di nomi DNS a nomi di servizi e di conseguenza di risolvere gli indirizzi IP degli endpoint.

Il proxy inverso indirizza i servizi nel cluster che espongono gli endpoint HTTP (incluso HTTPS). Il proxy inverso semplifica notevolmente la chiamata ad altri servizi fornendo un formato URI specifico.  Il proxy inverso gestisce anche i passaggi di risoluzione, connessione e ripetizione dei tentativi necessari per la comunicazione di un servizio con un altro.

## <a name="prepare-for-disaster-recovery"></a>Preparare l'ambiente per il ripristino di emergenza
Una parte fondamentale della distribuzione a disponibilità elevata consiste nel garantire la resistenza dei servizi a tutti i tipi di errori. Ciò è particolarmente importante per gli errori imprevisti e incontrollabili. [Prepare for disaster recovery](service-fabric-disaster-recovery.md) descrive alcune modalità di errore comuni che potrebbero essere disastri se non modellati e gestiti correttamente. Vengono inoltre illustrate le attenuazioni e le azioni da intraprendere in caso di emergenza.

## <a name="production-readiness-checklist"></a>Elenco di controllo per l'idoneità per la produzione
L'applicazione e il cluster sono pronti ad accettare il traffico della produzione? Prima di distribuire il cluster nell'ambiente di produzione, eseguire l'elenco di [controllo Preparazione produzione.](service-fabric-production-readiness-checklist.md) Mantenere l'applicazione e il cluster in esecuzione senza problemi utilizzando gli elementi in questo elenco di controllo. Consigliamo vivamente che tutti questi articoli siano spuntati prima di entrare in produzione.

## <a name="next-steps"></a>Passaggi successivi
* [Creare un cluster di Service Fabric che esegue WindowsCreate a Service Fabric cluster running Windows](service-fabric-best-practices-overview.md)
* [Creare un cluster di Service Fabric che esegue LinuxCreate a Service Fabric cluster running Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster