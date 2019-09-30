---
title: Gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP | Microsoft Docs
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c56bfda2b4f74bf31ce847f1fdb42f77f43eb372
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677989"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP
Le applicazioni SAP basate sull'architettura SAP NetWeaver o SAP S/4HANA sono sensibili alla latenza di rete tra il livello applicazione SAP e il livello database SAP. Questa distinzione è il risultato della maggior parte della logica di business in esecuzione a livello di applicazione. Poiché il livello dell'applicazione SAP esegue la logica di business, emette query al livello del database a una frequenza elevata, a una frequenza di migliaia o decine di migliaia al secondo. Nella maggior parte dei casi, la natura di queste query è semplice. Spesso possono essere eseguite a livello di database in microsecondi di 500 o meno.

Il tempo impiegato nella rete per inviare una query di questo tipo dal livello applicazione al livello database e ricevere il set di risultati ha un notevole effetto sul tempo richiesto per l'esecuzione dei processi di business. Questa sensibilità alla latenza di rete è il motivo per cui è necessario ottenere una latenza di rete ottimale nei progetti di distribuzione SAP. Vedere [SAP note #1100926-domande frequenti: Prestazioni di rete @ no__t-0 per le linee guida su come classificare la latenza di rete.

In molte aree di Azure il numero di Data Center è aumentato. Questa crescita è stata inoltre attivata con l'introduzione di zone di disponibilità. Allo stesso tempo, i clienti, specialmente per i sistemi SAP di fascia alta, usano SKU di VM più speciali nella famiglia di serie M o in istanze large di HANA. Questi tipi di macchine virtuali di Azure non sono disponibili in tutti i Data Center di una specifica area di Azure. A causa di queste due tendenze, i clienti hanno riscontrato una latenza di rete non compresa nell'intervallo ottimale. In alcuni casi, questa latenza comporta prestazioni non ottimali dei sistemi SAP.

Per evitare questi problemi, Azure offre [gruppi di posizionamento di prossimità](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Questa nuova funzionalità è già stata usata per la distribuzione di vari sistemi SAP. Per le restrizioni sui gruppi di posizionamento di prossimità, vedere l'articolo indicato all'inizio di questo paragrafo. Questo articolo descrive gli scenari SAP in cui è possibile o usare i gruppi di posizionamento di prossimità di Azure.

## <a name="what-are-proximity-placement-groups"></a>Che cosa sono i gruppi di posizionamento di prossimità? 
Un gruppo di posizionamento di prossimità di Azure è un costrutto logico. Quando ne viene definito uno, questo viene associato a un'area di Azure e a un gruppo di risorse di Azure. Quando si distribuiscono macchine virtuali, viene fatto riferimento a un gruppo di posizionamento di prossimità:

- Prima VM di Azure distribuita nel Data Center. La prima macchina virtuale può essere considerata come una "VM di ancoraggio" distribuita in un Data Center in base agli algoritmi di allocazione di Azure che vengono combinati con le definizioni utente per una zona di disponibilità specifica.
- Tutte le macchine virtuali successive distribuite che fanno riferimento al gruppo di posizionamento di prossimità, per inserire tutte le macchine virtuali di Azure successivamente distribuite nello stesso data center della prima macchina virtuale.

> [!NOTE]
> Se non è stato distribuito alcun hardware host in grado di eseguire un tipo specifico di VM nel Data Center in cui è stata posizionata la prima macchina virtuale, la distribuzione del tipo di macchina virtuale richiesto non riuscirà. Verrà ricevuto un messaggio di errore.

A un singolo [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) possono essere assegnati più gruppi di posizionamento di prossimità. Un gruppo di posizionamento di prossimità può tuttavia essere assegnato a un solo gruppo di risorse di Azure.

Quando si usano i gruppi di posizionamento prossimità, tenere presenti le considerazioni seguenti:

- Quando si mirano a ottenere prestazioni ottimali per il sistema SAP e si è limitati a un singolo Data Center di Azure per il sistema usando gruppi di posizionamento di prossimità, potrebbe non essere possibile combinare tutti i tipi di famiglie di macchine virtuali all'interno del gruppo di posizionamento. Queste limitazioni si verificano perché l'hardware host necessario per eseguire un determinato tipo di macchina virtuale potrebbe non essere presente nel Data Center in cui è stata distribuita la macchina virtuale di ancoraggio del gruppo di posizionamento.
- Durante il ciclo di vita di un sistema SAP di questo tipo, è possibile forzare lo spostamento del sistema in un altro Data Center. Questa operazione può essere necessaria se si decide che il livello di scalabilità orizzontale di HANA è necessario, ad esempio, passare da quattro nodi a 16 nodi e non è disponibile una capacità sufficiente per ottenere altre 12 macchine virtuali del tipo usato nel Data Center.
- A causa della rimozione delle autorizzazioni per l'hardware, Microsoft potrebbe creare capacità per un tipo di macchina virtuale usato in un data center diverso, anziché quello usato inizialmente. In questo scenario, potrebbe essere necessario spostare tutte le macchine virtuali del gruppo di posizionamento vicino in un altro Data Center.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Gruppi di posizionamento di prossimità con sistemi SAP che usano solo macchine virtuali di Azure
La maggior parte delle distribuzioni di sistema SAP NetWeaver e S/4HANA in Azure non usa le [istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Per le distribuzioni che non usano le istanze large di HANA, è importante fornire prestazioni ottimali tra il livello applicazione SAP e il livello DBMS. A tale scopo, definire un gruppo di posizionamento di prossimità di Azure solo per il sistema.

Nella maggior parte delle distribuzioni dei clienti, i clienti creano un singolo [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) per i sistemi SAP. In tal caso, esiste una relazione uno-a-uno tra, ad esempio, il gruppo di risorse di sistema ERP di produzione e il gruppo di posizionamento di prossimità. In altri casi, i clienti organizzano i gruppi di risorse orizzontalmente e raccolgono tutti i sistemi di produzione in un singolo gruppo di risorse. In questo caso, si avrà una relazione uno-a-molti tra il gruppo di risorse per i sistemi SAP di produzione e diversi gruppi di posizionamento di prossimità per SAP ERP di produzione, SAP BW e così via.

Evitare di aggregare diversi sistemi di produzione SAP o non di produzione in un singolo gruppo di posizionamento di prossimità. Quando un numero ridotto di sistemi SAP o di un sistema SAP e di alcune applicazioni circostanti deve avere una comunicazione di rete a bassa latenza, è possibile prendere in considerazione lo stato di trasferimento di questi sistemi in un gruppo di posizionamento di prossimità. È consigliabile evitare bundle di sistemi perché più sistemi si raggruppano in un gruppo di posizionamento di prossimità, maggiori sono le probabilità:

- È necessario un tipo di macchina virtuale che non può essere eseguito nel data center specifico in cui è stato ancorato il gruppo di posizionamento di prossimità.
- Le risorse delle macchine virtuali non mainstream, come le macchine virtuali della serie M, potrebbero non essere soddisfatte quando sono necessarie altre, perché il software viene aggiunto a un gruppo di posizionamento vicino nel tempo.

Di seguito è illustrata la configurazione ideale, come descritto in:

![Gruppi di posizionamento di prossimità con solo macchine virtuali di Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

In questo caso, i singoli sistemi SAP vengono raggruppati in un gruppo di risorse ciascuno, con un gruppo di posizionamento di prossimità. Non esiste alcuna dipendenza se si usano le configurazioni con scalabilità orizzontale o DBMS di HANA.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Gruppi di posizionamento di prossimità e istanze large di HANA
Se alcuni dei sistemi SAP si basano su [istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) per il livello dell'applicazione, è possibile riscontrare significativi miglioramenti nella latenza di rete tra l'unità di istanze large di Hana e le macchine virtuali di Azure quando si usano unità di istanze large di Hana distribuito in [revisione 4 righe o timbri](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Un miglioramento è che le unità di istanze large di HANA, così come sono distribuite, vengono distribuite con un gruppo di posizionamento di prossimità. Per distribuire le macchine virtuali a livello di applicazione, è possibile usare il gruppo di posizionamento di prossimità. Di conseguenza, tali macchine virtuali verranno distribuite nello stesso data center che ospita l'unità HANA in istanze large.

Per determinare se l'unità di istanze large di HANA viene distribuita in una riga o in un timbro di revisione 4, vedere l'articolo relativo al [controllo delle istanze large di Azure Hana tramite portale di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit). Nella panoramica degli attributi dell'unità di istanze large di HANA, è anche possibile determinare il nome del gruppo di posizionamento vicino perché è stato creato durante la distribuzione dell'unità di istanze large di HANA. Il nome visualizzato nella panoramica degli attributi è il nome del gruppo di posizionamento di prossimità in cui distribuire le macchine virtuali del livello applicazione.

Rispetto ai sistemi SAP che usano solo macchine virtuali di Azure, quando si usano le istanze large di HANA, è possibile decidere il numero di [gruppi di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) da usare. Tutte le unità di istanze large di HANA di un [tenant di istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) sono raggruppate in un singolo gruppo di risorse, come descritto in [questo articolo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). A meno che non vengano distribuiti in tenant diversi, ad esempio sistemi di produzione e non di produzione o altri sistemi, tutte le unità di istanze large di HANA verranno distribuite in un tenant di istanze large di HANA. Questo tenant ha una relazione uno-a-uno con un gruppo di risorse. Viene tuttavia definito un gruppo di posizionamento di prossimità separato per ognuna delle singole unità.

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

Il comando precedente distribuisce una macchina virtuale basata su Windows. Al termine della distribuzione della macchina virtuale, l'ambito del data center del gruppo di posizionamento vicino viene definito all'interno dell'area di Azure. Tutte le distribuzioni di macchine virtuali successive che fanno riferimento al gruppo di posizionamento di prossimità, come illustrato nel comando precedente, verranno distribuite nello stesso data center di Azure, purché il tipo di macchina virtuale possa essere ospitato nell'hardware posizionato nel Data Center e la capacità per quel tipo di macchina virtuale sia disponibile.

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

È necessario definire e creare il gruppo di posizionamento di prossimità. Il comando per la creazione del set di disponibilità richiede un riferimento aggiuntivo all'ID del gruppo di posizionamento vicino (non al nome). È possibile ottenere l'ID del gruppo di posizionamento di prossimità usando questo comando:

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
Se sono già stati distribuiti sistemi SAP, potrebbe essere necessario ottimizzare la latenza di rete di alcuni dei sistemi critici e individuare il livello dell'applicazione e il livello DBMS nello stesso data center. Durante l'anteprima pubblica dei gruppi di posizionamento vicino, è necessario eliminare le macchine virtuali e crearne di nuove per spostare il sistema in gruppi di posizionamento di prossimità. Attualmente non è possibile arrestare le VM e assegnarle ai gruppi di posizionamento di prossimità.


## <a name="next-steps"></a>Passaggi successivi
Consultare la documentazione:

- [Carichi di lavoro SAP in Azure: elenco di controllo di pianificazione e distribuzione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Anteprima: Distribuire macchine virtuali in gruppi di posizionamento con prossimità usando l'interfaccia della riga di comando](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Anteprima: Distribuire macchine virtuali in gruppi di posizionamento di prossimità usando PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per carichi di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

