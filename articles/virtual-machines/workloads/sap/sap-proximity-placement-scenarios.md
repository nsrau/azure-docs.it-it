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
ms.openlocfilehash: 02dcb7174dd9cb2926ef2fafda4b521b939ae68a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077977"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Gruppi di selezione host di prossimità di Azure per una latenza di rete ottimale con le applicazioni SAP
Le applicazioni SAP basate sull'architettura SAP NetWeaver o SAP S/4HANA sono sensibili alla latenza di rete tra il livello applicazione SAP e il livello database SAP. Il motivo di questa distinzione di queste architetture è radicato nel fatto che la maggior parte della logica di business viene eseguita a livello di applicazione. In seguito all'esecuzione della logica di business, il livello dell'applicazione SAP emette query al livello di database con frequenza elevata di migliaia e decine di migliaia al secondo. Nella maggior parte dei casi, la natura delle query è semplice. E spesso possono essere eseguite a livello di database in meno di 500 microsecondi o addirittura inferiori. Il tempo impiegato nella rete per inviare una query di questo tipo dal livello applicazione al livello database e ricevere il set di risultati dal livello database ha un notevole effetto sul tempo richiesto per l'esecuzione dei processi di business. Questa sensibilità alla latenza di rete è il motivo per cui è necessario trascorrere i progetti di distribuzione SAP per ottenere una latenza di rete ottimale. Nella [Nota SAP #1100926-domande frequenti: Prestazioni](https://launchpad.support.sap.com/#/notes/1100926/E)di rete, SAP ha pubblicato alcune linee guida per la classificazione della latenza di rete.

Da un lato, in molte aree di Azure è cresciuto il numero di Data Center, attivato anche dall'introduzione del zone di disponibilità. Dall'altro lato, i clienti, specialmente per i sistemi SAP di fascia alta, usavano più SKU di VM speciali per le famiglie della serie M o le istanze large di HANA. Tipi di macchine virtuali di Azure che non sono presenti in tutti i Data Center di una specifica area di Azure. In seguito a queste due tendenze, i clienti hanno riscontrato casi in cui la latenza di rete non era nell'intervallo ottimale e in alcuni casi ha comportato prestazioni non ottimali dei sistemi SAP.

Per evitare questo problema, Azure offre un costrutto denominato gruppo di posizionamento di [prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Questa nuova funzionalità è stata usata per distribuire già diversi sistemi SAP diversi. Vedere l'articolo a cui si fa riferimento per le restrizioni dei gruppi di posizionamento di prossimità. Questo articolo illustra i diversi scenari SAP in cui è possibile usare o usare i gruppi di posizionamento di prossimità di Azure.

## <a name="what-are-proximity-placement-groups"></a>Che cosa sono i gruppi di posizionamento di prossimità 
Un gruppo di posizionamento di prossimità di Azure è un costrutto logico, che alla fase di definizione è associato a un'area di Azure e a un gruppo di risorse di Azure. Durante la distribuzione delle macchine virtuali, viene fatto riferimento a un gruppo di posizionamento di prossimità:

- La prima macchina virtuale di Azure distribuita per stabilirsi nel datacenter. La prima macchina virtuale può essere considerata una VM di ancoraggio che viene distribuita in un Data Center in base agli algoritmi di allocazione di Azure combinati con definizioni utente per una zona di disponibilità di Azure specifica.
- Da tutte le macchine virtuali successive distribuite che fanno riferimento al gruppo di posizionamento vicino per inserire tutte le macchine virtuali di Azure successive distribuite nello stesso data center in cui è stata inserita la prima macchina virtuale.

> [!NOTE]
> Se non è stato distribuito alcun hardware host in grado di eseguire un tipo di macchina virtuale specifico nello stesso data center in cui è stata inserita la prima macchina virtuale, la distribuzione del tipo di VM richiesto avrà esito negativo e terminerà con un messaggio di errore. Questi possono essere casi in cui più macchine virtuali non mainstream, come le macchine virtuali con GPU o i tipi di VM HPC, dovrebbero essere centrate, ad esempio, una VM della serie M distribuita come primo tipo di macchina virtuale

A un singolo [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) possono essere assegnati più gruppi di posizionamento di prossimità. Tuttavia, un gruppo di posizionamento di prossimità può essere assegnato solo a un gruppo di risorse di Azure.

Utilizzando gruppi di posizionamento prossimità, è necessario tenere presente quanto segue:

- Quando si determinano le prestazioni più ottimali per il sistema SAP e si limitano a un singolo Data Center di Azure per questo sistema usando gruppi di posizionamento di prossimità, potrebbe non essere possibile combinare tutti i tipi di famiglie di macchine virtuali in un gruppo di posizionamento di prossimità. Il motivo è che alcuni componenti hardware host necessari per eseguire esclusivamente un determinato tipo di macchina virtuale potrebbero non essere presenti nel Data Center in cui è stata distribuita la macchina virtuale di ancoraggio del gruppo di posizionamento.
- Nel ciclo di vita di un sistema SAP di questo tipo, è possibile forzare lo spostamento del sistema in un altro Data Center. Questo tipo di spostamento può essere forzato nei casi in cui si è deciso che il livello di sistema DBMS di HANA con scalabilità orizzontale dovrebbe, ad esempio, passare da quattro nodi a 16 nodi. Ma non è più disponibile una capacità sufficiente per ottenere altre 12 macchine virtuali del tipo già usato nello stesso data center.
- A causa della rimozione delle autorizzazioni per l'hardware, Microsoft potrebbe creare capacità per i tipi di VM usati in un altro Data Center, anziché lo stesso data center. Tale occorrenza può comportare lo spostamento di tutte le macchine virtuali del gruppo di posizionamento vicino in un altro Data Center.


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>Gruppi di posizionamento di prossimità di Azure con sistemi SAP che usano esclusivamente macchine virtuali di Azure
La maggior parte delle distribuzioni di sistemi SAP NetWeaver e S/4HANA in Azure non usa le [istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Per le distribuzioni di tali sistemi, è importante fornire le prestazioni più ottimali tra il livello applicazione SAP e il livello DBMS. A tale scopo, è necessario definire un gruppo di posizionamento di prossimità di Azure solo per questo sistema. Nella maggior parte delle distribuzioni dei clienti, i clienti hanno scelto di creare un singolo [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) per i sistemi SAP. In tal caso, è presente una relazione 1:1 tra, ad esempio, i gruppi di risorse di sistema ERP di produzione e il relativo gruppo di posizionamento di prossimità. In altri casi di distribuzione, i clienti organizzavano i gruppi di risorse orizzontalmente e raccoglievano tutti i sistemi di produzione in un unico gruppo di risorse. In tal caso, si disporrà di una relazione da 1 a molti tra il gruppo di risorse per i sistemi SAP di produzione e diversi gruppi di posizionamento di prossimità di SAP ERP di produzione, SAP BW e così via. È consigliabile evitare di raggruppare diversi sistemi di produzione SAP o non di produzione in un singolo gruppo di posizionamento di prossimità. In eccezioni, in cui un numero ridotto di sistemi SAP o un sistema SAP e alcune applicazioni circostanti sono necessari per la comunicazione di rete a bassa latenza, è possibile provare a trasferire questi sistemi in un gruppo di posizionamento di prossimità. Il motivo di questa raccomandazione è che il maggior numero di sistemi raggruppati in un gruppo di posizionamento di prossimità è più elevato delle probabilità:

- È necessario un tipo di macchina virtuale che non può essere eseguito nel data center specifico in cui è stato ancorato il gruppo di posizionamento di prossimità.
- Le risorse di alcune macchine virtuali non mainstream, come la serie M, potrebbero non essere più soddisfatte quando si richiede più tempo durante l'aggiunta di software aggiuntivo a un gruppo di posizionamento di prossimità esistente nel tempo.

L'utilizzo ideale come descritto sarà simile al seguente:

![Gruppi di posti di prossimità per tutte le macchine virtuali di Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

In questo caso, i singoli sistemi SAP vengono raggruppati in un gruppo di risorse ciascuno con un gruppo di posizionamento di prossimità. Non esiste alcuna dipendenza se si usano le configurazioni con scalabilità orizzontale o DBMS di HANA.


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Gruppi di posizionamento di prossimità di Azure e istanze large di HANA
Per i casi in cui alcuni dei sistemi SAP si basano su [istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) come livello dell'applicazione, è possibile ottenere miglioramenti gravi nella latenza di rete tra l'unità di istanze large di Hana e le macchine virtuali di Azure quando si usano unità di istanze large di Hana che sono state distribuito in [revisione 4 righe o timbri](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Uno dei miglioramenti è che le unità di istanze large di HANA, così come sono state distribuite, ottengono già un gruppo di posizionamento vicino distribuito. Per distribuire le macchine virtuali a livello di applicazione, è possibile usare il gruppo di posizionamento di prossimità. Di conseguenza, tali macchine virtuali verranno distribuite nello stesso data center che ospita l'unità di istanze large di HANA.

Per rilevare se l'unità di istanze large di HANA viene distribuita in una riga o un timbro di revisione 4, vedere l'articolo [controllo delle istanze large di Azure Hana tramite portale di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit). Nella panoramica degli attributi dell'unità di istanze large di HANA, è anche possibile trovare il nome del gruppo di posizionamento di prossimità, perché è stato creato in fase di distribuzione per l'unità di istanze large di HANA. Il nome visualizzato nella panoramica degli attributi è il nome del gruppo di posizionamento vicino, che è necessario usare per distribuire le macchine virtuali a livello di applicazione.

Contrariamente ai sistemi SAP che usano solo macchine virtuali di Azure, la decisione sul numero di [gruppi di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) da usare viene sottratta a un certo livello di utilizzo delle istanze large di Hana. Tutte le unità di istanze large di HANA di un [tenant di istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) sono raggruppate in un singolo gruppo di risorse di Azure, come descritto [qui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). A meno che non si desideri una distribuzione in tenant diversi per separare, ad esempio, produzione e non di produzione o alcuni sistemi, tutte le unità di istanze large di HANA verranno distribuite in un tenant di istanze large di HANA, che ha di nuovo una relazione 1:1 con Azure Gruppo di risorse. Mentre per tutte le unità singole sarà definito un gruppo di posizionamento di prossimità separato. 

Di conseguenza, il raggruppamento tra i gruppi di risorse di Azure e i gruppi di posizionamento di prossimità per un singolo tenant sarà simile al seguente:

![Gruppi di posizionamento di prossimità per tutte le macchine virtuali di Azure](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>Breve esempio di distribuzione con i gruppi di posizionamento di prossimità di Azure
Per dimostrare come è possibile usare i gruppi di posizionamento di prossimità di Azure per distribuire la macchina virtuale, ecco un elenco di comandi di PowerShell che ne illustrano l'utilizzo per un primo piccolo esercizio con i gruppi di posizionamento di prossimità di Azure

Il primo passaggio dopo aver eseguito l'accesso con il [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) consiste nel verificare se si è nella sottoscrizione di Azure corretta da usare per la distribuzione con il comando:

<pre><code>
Get-AzureRmContext
</code></pre>

Se è necessario passare a una sottoscrizione diversa, è possibile eseguire questo comando:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Come terzo passaggio, si vuole creare un nuovo gruppo di risorse di Azure con questo comando:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

È ora possibile creare il nuovo gruppo di posizionamento vicino con:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

A questo punto è possibile iniziare a distribuire la prima VM in questo gruppo di posizionamento di prossimità con un comando simile al seguente:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Con il comando precedente, viene distribuita una macchina virtuale basata su Windows. Al termine della distribuzione della macchina virtuale, l'ambito del data center del gruppo di posizionamento vicino viene definito all'interno dell'area di Azure. Tutte le distribuzioni di macchine virtuali successive che fanno riferimento al gruppo di posizionamento prossimità come nell'ultimo comando verranno distribuite nello stesso data center di Azure, purché sia possibile ospitare il tipo di macchina virtuale nell'hardware posizionato in quel Data Center e/o la capacità per quel tipo di macchina virtuale sia disponibile.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Combinare set di disponibilità e zone di disponibilità con gruppi di posizionamento vicini 
Con zone di disponibilità per le distribuzioni di sistemi SAP, uno degli svantaggi è il fatto che non è possibile controllare il livello dell'applicazione SAP distribuito usando i set di disponibilità all'interno della zona specifica. Poiché si vuole che il livello dell'applicazione SAP venga distribuito nelle stesse zone del livello DBMS e il riferimento a una zona di disponibilità e a un set di disponibilità quando si distribuisce una singola macchina virtuale non è supportato, è necessario distribuire il livello dell'applicazione facendo riferimento a una zona e in questo modo si perde la possibilità di assicurarsi che le macchine virtuali del livello applicazione vengano distribuite in diversi domini di aggiornamento e di errore. Con l'ausilio dei gruppi di posizionamento vicino è possibile superare questa restrizione. La sequenza di distribuzioni avrà un aspetto simile al seguente:

- Creare un gruppo di selezione host di prossimità
- Distribuire la macchina virtuale di ancoraggio, in genere il server DBMS, facendo riferimento a una determinata zona di disponibilità di Azure
- Creare un set di disponibilità che fa riferimento al gruppo di prossimità di Azure (vedere di seguito)
- Distribuire le macchine virtuali a livello di applicazione facendo riferimento al set di disponibilità e al gruppo di posizionamento vicino

Invece di distribuire la prima VM, come illustrato in precedenza, si fa riferimento a una zona di disponibilità di Azure e al gruppo di posizionamento prossimità quando si distribuisce la macchina virtuale come:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Una distribuzione corretta della macchina virtuale che ospita l'istanza di database del sistema SAP in una zona di disponibilità di Azure, l'ambito del gruppo di posizionamento vicino è fissato a uno dei data center che rappresentano la zona di disponibilità definita. .

Si presuppone che le macchine virtuali dei servizi centrali vengano distribuite in modo analogo alle macchine virtuali DBMs facendo riferimento alle stesse zone di per le macchine virtuali DBMS e agli stessi gruppi di posizionamento di prossimità. Nel passaggio successivo è necessario creare i set di disponibilità che si vuole usare per il livello dell'applicazione del sistema SAP.
È necessario definire e creare il gruppo di posizionamento di prossimità. Il comando per la creazione del set di disponibilità richiede un riferimento aggiuntivo all'ID del gruppo di posizionamento vicino (non al nome). È possibile ottenere l'ID del gruppo di posizionamento con prossimità:



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Quando si crea il set di disponibilità, è necessario prendere in considerazione parametri aggiuntivi quando si usa Managed Disks (impostazione predefinita, a meno che non sia specificato diversamente) e gruppi di posizionamento di prossimità:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Idealmente, è consigliabile usare tre domini di errore. Tuttavia, il numero di domini di errore supportati può variare da un'area all'altra. In questo caso, il numero massimo di domini di errore possibili per le aree specifiche è due. Per la distribuzione delle macchine virtuali a livello di applicazione, è necessario aggiungere un riferimento al nome del set di disponibilità e al nome del gruppo di posizionamento vicino, come illustrato di seguito:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Il risultato finale di questa sequenza sarà un livello DBMS e servizi centrali del sistema SAP che si trovano in una zona di disponibilità specifica e un livello applicazione SAP che si trova attraverso i set di disponibilità negli stessi Data Center di Azure delle macchine virtuali DBMS ottenute distribuito.

> [!NOTE]
> Quando si distribuisce una macchina virtuale DBMS in una zona e la seconda macchina virtuale DBMS in un'altra zona per creare una configurazione a disponibilità elevata, verranno richiesti gruppi di posizionamento di prossimità diversi per ogni zona. Uguale a true per il set di disponibilità che è possibile usare

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>Ottenere un sistema esistente nei gruppi di posizionamento di prossimità di Azure
Poiché i sistemi SAP sono già stati distribuiti, potrebbe essere necessario ottimizzare la latenza di rete di alcuni dei sistemi critici e individuare il livello dell'applicazione e il livello DBMS nello stesso data center. Nella fase di anteprima pubblica delle funzionalità del gruppo di posizionamento di prossimità, un'eliminazione delle VM e una nuova creazione delle VM è necessaria per eseguire tale spostamento in gruppi di posizionamento di prossimità. In questa fase della funzionalità non è sufficiente arrestare le VM per poter assegnare le macchine virtuali di arresto ai gruppi di posizionamento di prossimità.


## <a name="next-steps"></a>Fasi successive
Consultare la documentazione:

- [Elenco di controllo del carico di lavoro SAP in pianificazione e distribuzione di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Anteprima: Distribuire macchine virtuali in gruppi di posizionamento con prossimità usando l'interfaccia della riga di comando](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Anteprima: Distribuire macchine virtuali in gruppi di posizionamento di prossimità usando PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

