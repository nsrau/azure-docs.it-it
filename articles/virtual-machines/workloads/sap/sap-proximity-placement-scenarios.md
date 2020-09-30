---
title: Gruppi di posizionamento di prossimità di Azure per le applicazioni SAP | Microsoft Docs
description: Descrive gli scenari di distribuzione SAP con i gruppi di posizionamento di prossimità di Azure
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
ms.date: 09/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b6e15ef1b9bf488ac18e41dc09eb71e6ea3da39
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569793"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP
Le applicazioni SAP basate sull'architettura SAP NetWeaver o SAP S/4HANA sono sensibili alla latenza di rete tra il livello applicazione SAP e il livello database SAP. Questa distinzione è il risultato della maggior parte della logica di business in esecuzione a livello di applicazione. Poiché il livello dell'applicazione SAP esegue la logica di business, emette query al livello del database a una frequenza elevata, a una frequenza di migliaia o decine di migliaia al secondo. Nella maggior parte dei casi, la natura di queste query è semplice. Spesso possono essere eseguite a livello di database in microsecondi di 500 o meno.

Il tempo impiegato nella rete per inviare una query di questo tipo dal livello applicazione al livello database e ricevere il set di risultati ha un notevole effetto sul tempo richiesto per l'esecuzione dei processi di business. Questa sensibilità alla latenza di rete è il motivo per cui è possibile che si desideri ottenere una latenza di rete massima nei progetti di distribuzione SAP. Vedere la [Nota SAP #1100926-domande frequenti: prestazioni di rete](https://launchpad.support.sap.com/#/notes/1100926/E) per le linee guida su come classificare la latenza di rete.

In molte aree di Azure il numero di Data Center è aumentato. Allo stesso tempo, i clienti, specialmente per i sistemi SAP di fascia alta, usano SKU di VM più speciali della famiglia M o Mv2 o di istanze large di HANA. Questi tipi di macchine virtuali di Azure non sono sempre disponibili in tutti i data center che completano un'area di Azure. Questi fatti possono creare opportunità per ottimizzare la latenza di rete tra il livello applicazione SAP e il livello DBMS di SAP.

Per offrire la possibilità di ottimizzare la latenza di rete, Azure offre [gruppi di posizionamento di prossimità](../../linux/co-location.md). I gruppi di posizionamento di prossimità possono essere usati per forzare il raggruppamento di tipi di VM diversi in un singolo Data Center di Azure per ottimizzare la latenza di rete tra questi diversi tipi di VM nel modo migliore possibile. Nel processo di distribuzione della prima macchina virtuale in un gruppo di posizionamento di prossimità, la macchina virtuale viene associata a un data center specifico. Per quanto riguarda questo aspetto, l'utilizzo del costrutto introduce anche alcune restrizioni:

- Non è possibile presupporre che tutti i tipi di VM di Azure siano disponibili in tutti i Data Center di Azure. Di conseguenza, è possibile limitare la combinazione di diversi tipi di VM all'interno di un gruppo di posizionamento di prossimità. Queste restrizioni si verificano perché l'hardware host necessario per eseguire un determinato tipo di macchina virtuale potrebbe non essere presente nel Data Center in cui è stato distribuito il gruppo di posizionamento.
- Quando si ridimensionano parti delle macchine virtuali che si trovano all'interno di un gruppo di posizionamento di prossimità, non è possibile presupporre automaticamente che in tutti i casi il nuovo tipo di macchina virtuale sia disponibile nello stesso data center delle altre macchine virtuali che fanno parte del gruppo di posizionamento vicino
- Poiché Azure rimuove le autorizzazioni hardware, potrebbe forzare alcune macchine virtuali di un gruppo di posizionamento vicino in un altro Data Center di Azure. Per informazioni dettagliate su questo caso, vedere il documento relativo alla [condivisione percorso risorse per migliorare la latenza](https://docs.microsoft.com/azure/virtual-machines/linux/co-location#planned-maintenance-and-proximity-placement-groups)  

> [!IMPORTANT]
> In seguito alle potenziali restrizioni, è necessario usare i gruppi di posizionamento di prossimità:
>
> - Solo quando necessario
> - Solo per la granularità di un singolo sistema SAP e non per l'intero panorama del sistema o per un panorama applicativo SAP completo
> - Per evitare che i diversi tipi di VM e il numero di macchine virtuali in un gruppo di posizionamento di prossimità siano minime


## <a name="what-are-proximity-placement-groups"></a>Che cosa sono i gruppi di posizionamento di prossimità? 
Un gruppo di posizionamento di prossimità di Azure è un costrutto logico. Quando viene definito un gruppo di posizionamento di prossimità, questo viene associato a un'area di Azure e a un gruppo di risorse di Azure. Quando si distribuiscono macchine virtuali, viene fatto riferimento a un gruppo di posizionamento di prossimità:

- Prima VM di Azure distribuita nel Data Center. La prima macchina virtuale può essere considerata come una "VM di ambito" distribuita in un Data Center in base agli algoritmi di allocazione di Azure che vengono combinati con le definizioni utente per una zona di disponibilità specifica.
- Tutte le macchine virtuali successive distribuite che fanno riferimento al gruppo di posizionamento di prossimità, per inserire tutte le macchine virtuali di Azure successivamente distribuite nello stesso data center della prima macchina virtuale.

> [!NOTE]
> Se non è stato distribuito alcun hardware host in grado di eseguire un tipo specifico di VM nel Data Center in cui è stata posizionata la prima macchina virtuale, la distribuzione del tipo di macchina virtuale richiesto non riuscirà. Verrà ricevuto un messaggio di errore.

A un singolo [gruppo di risorse di Azure](../../../azure-resource-manager/management/manage-resources-portal.md) possono essere assegnati più gruppi di posizionamento di prossimità. Un gruppo di posizionamento di prossimità può tuttavia essere assegnato a un solo gruppo di risorse di Azure.


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Gruppi di posizionamento di prossimità con sistemi SAP che usano solo macchine virtuali di Azure
La maggior parte delle distribuzioni di sistema SAP NetWeaver e S/4HANA in Azure non usa le [istanze large di Hana](./hana-overview-architecture.md). Per le distribuzioni che non usano le istanze large di HANA, è importante fornire prestazioni ottimali tra il livello applicazione SAP e il livello DBMS. A tale scopo, definire un gruppo di posizionamento di prossimità di Azure solo per il sistema.

Nella maggior parte delle distribuzioni dei clienti, i clienti creano un singolo [gruppo di risorse di Azure](../../../azure-resource-manager/management/manage-resources-portal.md) per i sistemi SAP. In tal caso, esiste una relazione uno-a-uno tra, ad esempio, il gruppo di risorse di sistema ERP di produzione e il gruppo di posizionamento di prossimità. In altri casi, i clienti organizzano i gruppi di risorse orizzontalmente e raccolgono tutti i sistemi di produzione in un singolo gruppo di risorse. In questo caso, si avrà una relazione uno-a-molti tra il gruppo di risorse per i sistemi SAP di produzione e diversi gruppi di posizionamento di prossimità per SAP ERP di produzione, SAP BW e così via.

Evitare di aggregare diversi sistemi di produzione SAP o non di produzione in un singolo gruppo di posizionamento di prossimità. Quando un numero ridotto di sistemi SAP o di un sistema SAP e di alcune applicazioni circostanti deve avere una comunicazione di rete a bassa latenza, è possibile prendere in considerazione lo stato di trasferimento di questi sistemi in un gruppo di posizionamento di prossimità. Evitare bundle di sistemi perché maggiore è il numero di sistemi che si raggruppano in un gruppo di posizionamento vicino, maggiori sono le probabilità:

- È necessario un tipo di macchina virtuale che non può essere eseguito nel data center specifico in cui è stato definito l'ambito del gruppo di posizionamento di prossimità.
- Le risorse delle macchine virtuali non mainstream, come le macchine virtuali della serie M, potrebbero non essere soddisfatte quando sono necessarie altre, perché il software viene aggiunto a un gruppo di posizionamento vicino nel tempo.

Di seguito è illustrata la configurazione ideale, come descritto in:

![Gruppi di posizionamento di prossimità con solo macchine virtuali di Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

In questo caso, i singoli sistemi SAP vengono raggruppati in un gruppo di risorse ciascuno, con un gruppo di posizionamento di prossimità. Non esiste alcuna dipendenza se si usano le configurazioni con scalabilità orizzontale o DBMS di HANA.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Gruppi di posizionamento di prossimità e istanze large di HANA
Se alcuni dei sistemi SAP si basano su [istanze large di Hana](./hana-overview-architecture.md) per il livello dell'applicazione, è possibile riscontrare significativi miglioramenti nella latenza di rete tra l'unità di istanze large di Hana e le macchine virtuali di Azure quando si usano unità di istanze large di Hana distribuite nella [revisione 4 righe o timbri](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). Un miglioramento è che le unità di istanze large di HANA, così come sono distribuite, vengono distribuite con un gruppo di posizionamento di prossimità. Per distribuire le macchine virtuali a livello di applicazione, è possibile usare il gruppo di posizionamento di prossimità. Di conseguenza, tali macchine virtuali verranno distribuite nello stesso data center che ospita l'unità HANA in istanze large.

Per determinare se l'unità di istanze large di HANA viene distribuita in una riga o in un timbro di revisione 4, vedere l'articolo relativo al [controllo delle istanze large di Azure Hana tramite portale di Azure](./hana-li-portal.md#look-at-attributes-of-single-hli-unit). Nella panoramica degli attributi dell'unità di istanze large di HANA, è anche possibile determinare il nome del gruppo di posizionamento vicino perché è stato creato durante la distribuzione dell'unità di istanze large di HANA. Il nome visualizzato nella panoramica degli attributi è il nome del gruppo di posizionamento di prossimità in cui distribuire le macchine virtuali del livello applicazione.

Rispetto ai sistemi SAP che usano solo macchine virtuali di Azure, quando si usano le istanze large di HANA, è possibile decidere il numero di [gruppi di risorse di Azure](../../../azure-resource-manager/management/manage-resources-portal.md) da usare. Tutte le unità di istanze large di HANA di un [tenant di istanze large di Hana](./hana-know-terms.md) sono raggruppate in un singolo gruppo di risorse, come descritto in [questo articolo](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal). A meno che non vengano distribuiti in tenant diversi, ad esempio sistemi di produzione e non di produzione o altri sistemi, tutte le unità di istanze large di HANA verranno distribuite in un tenant di istanze large di HANA. Questo tenant ha una relazione uno-a-uno con un gruppo di risorse. Viene tuttavia definito un gruppo di posizionamento di prossimità separato per ognuna delle singole unità.

Di conseguenza, le relazioni tra i gruppi di risorse di Azure e i gruppi di posizionamento di prossimità per un singolo tenant verranno mostrate di seguito:

![Gruppi di posizionamento di prossimità e istanze large di HANA](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Esempio di distribuzione con gruppi di posizionamento vicini
Di seguito sono riportati alcuni comandi di PowerShell che è possibile usare per distribuire le macchine virtuali con i gruppi di posizionamento di prossimità di Azure.

Il primo passaggio, dopo l'accesso a [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/), consiste nel verificare se si è nella sottoscrizione di Azure che si vuole usare per la distribuzione:

<pre><code>
Get-AzureRmContext
</code></pre>

Se è necessario passare a una sottoscrizione diversa, è possibile eseguire questo comando:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Creare un nuovo gruppo di risorse di Azure eseguendo questo comando:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Creare il nuovo gruppo di posizionamento di prossimità eseguendo questo comando:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Distribuire la prima VM nel gruppo di posizionamento di prossimità usando un comando simile al seguente:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Il comando precedente distribuisce una macchina virtuale basata su Windows. Al termine della distribuzione della macchina virtuale, l'ambito del data center del gruppo di posizionamento vicino viene definito all'interno dell'area di Azure. Tutte le distribuzioni di macchine virtuali successive che fanno riferimento al gruppo di posizionamento di prossimità, come illustrato nel comando precedente, verranno distribuite nello stesso data center di Azure, purché il tipo di macchina virtuale possa essere ospitato nell'hardware posizionato in quel Data Center e la capacità per tale tipo di macchina virtuale sia disponibile.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Combinare set di disponibilità e zone di disponibilità con gruppi di posizionamento vicini
Uno degli svantaggi dell'uso di zone di disponibilità per le distribuzioni di sistemi SAP è che non è possibile distribuire il livello dell'applicazione SAP usando i set di disponibilità all'interno di una zona specifica. Si vuole che il livello dell'applicazione SAP venga distribuito nelle stesse zone del livello DBMS. Il riferimento a una zona di disponibilità e un set di disponibilità quando si distribuisce una singola macchina virtuale non è supportato. Quindi, in precedenza era necessario distribuire il livello dell'applicazione facendo riferimento a una zona. Si è persa la possibilità di assicurarsi che le macchine virtuali a livello di applicazione fossero distribuite in domini di aggiornamento e di errore diversi.

Usando i gruppi di posizionamento di prossimità, è possibile ignorare questa restrizione. La sequenza di distribuzione è la seguente:

- Creare un gruppo di posizionamento di prossimità.
- Distribuire la VM di ancoraggio, in genere il server DBMS, facendo riferimento a una zona di disponibilità.
- Creare un set di disponibilità che faccia riferimento al gruppo di prossimità di Azure. Vedere il comando più avanti in questo articolo.
- Distribuire le macchine virtuali a livello di applicazione facendo riferimento al set di disponibilità e al gruppo di posizionamento di prossimità.

Anziché distribuire la prima macchina virtuale come illustrato nella sezione precedente, si fa riferimento a una zona di disponibilità e al gruppo di posizionamento vicino quando si distribuisce la macchina virtuale:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Una distribuzione corretta di questa macchina virtuale ospita l'istanza di database del sistema SAP in una zona di disponibilità. L'ambito del gruppo di posizionamento vicino è fissato a uno dei data center che rappresentano la zona di disponibilità definita.

Si supponga di distribuire le VM di servizi centrali in modo analogo alle macchine virtuali DBMS, facendo riferimento alla stessa zona o alle stesse zone e agli stessi gruppi di posizionamento di prossimità. Nel passaggio successivo è necessario creare i set di disponibilità che si vuole usare per il livello dell'applicazione del sistema SAP.

Definire e creare il gruppo di posizionamento di prossimità. Il comando per la creazione del set di disponibilità richiede un riferimento aggiuntivo all'ID del gruppo di posizionamento vicino (non al nome). È possibile ottenere l'ID del gruppo di posizionamento di prossimità usando questo comando:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Quando si crea il set di disponibilità, è necessario prendere in considerazione parametri aggiuntivi quando si usa Managed Disks (impostazione predefinita, a meno che non sia specificato diversamente) e gruppi di posizionamento prossimità:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Idealmente, è consigliabile usare tre domini di errore. Tuttavia, il numero di domini di errore supportati può variare da un'area all'altra. In questo caso, il numero massimo di domini di errore possibili per le aree specifiche è due. Per distribuire le macchine virtuali a livello di applicazione, è necessario aggiungere un riferimento al nome del set di disponibilità e al nome del gruppo di posizionamento vicino, come illustrato di seguito:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Il risultato di questa distribuzione è:
- Un livello DBMS e servizi centrali per il sistema SAP che si trovano in una zona di disponibilità specifica o in zone di disponibilità.
- Livello dell'applicazione SAP che si trova attraverso i set di disponibilità negli stessi Data Center di Azure della VM o delle VM DBMS.

> [!NOTE]
> Poiché si distribuisce una macchina virtuale DBMS in una zona e la seconda macchina virtuale DBMS in un'altra zona per creare una configurazione a disponibilità elevata, è necessario un gruppo di posizionamento di prossimità diverso per ogni zona. Lo stesso vale per qualsiasi set di disponibilità utilizzato.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Spostare un sistema esistente in gruppi di posizionamento di prossimità
Se sono già stati distribuiti sistemi SAP, potrebbe essere necessario ottimizzare la latenza di rete di alcuni dei sistemi critici e individuare il livello dell'applicazione e il livello DBMS nello stesso data center. Per spostare le macchine virtuali di un set di disponibilità di Azure completo in un gruppo di posizionamento di prossimità esistente con ambito già definito, è necessario arrestare tutte le VM del set di disponibilità e assegnare il set di disponibilità al gruppo di posizionamento di prossimità esistente tramite portale di Azure, PowerShell o l'interfaccia della riga di comando. Se si vuole spostare una macchina virtuale che non fa parte di un set di disponibilità in un gruppo di posizionamento di prossimità esistente, è sufficiente arrestare la macchina virtuale e assegnarla a un gruppo di posizionamento di prossimità esistente. 


## <a name="next-steps"></a>Passaggi successivi
Consultare la documentazione:

- [Carichi di lavoro SAP in Azure: elenco di controllo di pianificazione e distribuzione](./sap-deployment-checklist.md)
- [Anteprima: distribuire le VM nei gruppi di posizionamento con prossimità usando l'interfaccia della riga di comando](../../linux/proximity-placement-groups.md)
- [Anteprima: distribuire le VM nei gruppi di posizionamento di prossimità usando PowerShell](../../windows/proximity-placement-groups.md)
- [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per carichi di lavoro SAP](./dbms_guide_general.md)
