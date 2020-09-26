---
title: Panoramica degli host dedicati di Azure per le macchine virtuali
description: Altre informazioni sul modo in cui è possibile usare gli host dedicati di Azure per la distribuzione di macchine virtuali.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/28/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 351b10b68cf1c014d358032f41bb01ac1093bd68
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91373130"
---
# <a name="azure-dedicated-hosts"></a>Host dedicati di Azure

L'host dedicato di Azure è un servizio che offre server fisici, in grado di ospitare una o più macchine virtuali, dedicati a una sottoscrizione di Azure. Gli host dedicati sono gli stessi server fisici usati nei data center, forniti come risorsa. È possibile effettuare il provisioning di host dedicati all'interno di un'area, di una zona di disponibilità e di un dominio di errore. In seguito è possibile inserire le VM direttamente negli host con provisioning, nella configurazione più adatta alle proprie esigenze.


## <a name="benefits"></a>Vantaggi 

Per riservare l'intero host sono disponibili i vantaggi seguenti:

-   Isolamento hardware a livello di server fisico. Non verranno inserite altre macchine virtuali negli host. Gli host dedicati vengono distribuiti negli stessi Data Center e condividono la stessa rete e l'infrastruttura di archiviazione sottostante come altri host non isolati.
-   Controllo sugli eventi di manutenzione avviati dalla piattaforma Azure. Sebbene la maggior parte degli eventi di manutenzione non abbia alcun effetto sulle macchine virtuali, esistono alcuni carichi di lavoro sensibili in cui ogni secondo di pausa può avere un certo effetto. Con gli host dedicati è possibile acconsentire esplicitamente a una finestra di manutenzione per ridurre l'effetto sul servizio.
-   Con il vantaggio Azure Hybrid puoi usare licenze personalizzate per Windows e SQL in Azure. L'uso dei vantaggi ibridi offre vantaggi aggiuntivi. Per ulteriori informazioni, vedere [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Gruppi, host e macchine virtuali  

![Visualizzazione delle nuove risorse per gli host dedicati.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Un **gruppo host** è una risorsa che rappresenta una raccolta di host dedicati. È necessario creare un gruppo host in un'area e in una zona di disponibilità e aggiungervi gli host.

Un **host** è una risorsa, mappata a un server fisico in un Data Center di Azure. Il server fisico viene allocato quando viene creato l'host. Un host viene creato all'interno di un gruppo host. Un host dispone di uno SKU che descrive le dimensioni della macchina virtuale che è possibile creare. Ogni host può ospitare più macchine virtuali di dimensioni diverse, purché provengano dalla stessa serie di dimensioni.


## <a name="high-availability-considerations"></a>Considerazioni sulla disponibilità elevata 

Per la disponibilità elevata, è consigliabile distribuire più macchine virtuali, distribuite tra più host (almeno 2). Con gli host dedicati di Azure sono disponibili diverse opzioni per eseguire il provisioning dell'infrastruttura per definire i limiti di isolamento degli errori.

### <a name="use-availability-zones-for-fault-isolation"></a>USA zone di disponibilità per l'isolamento degli errori

Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Un gruppo host viene creato in una singola zona di disponibilità. Una volta creati, tutti gli host saranno posizionati all'interno di tale area. Per ottenere una disponibilità elevata tra le zone, è necessario creare più gruppi host (uno per zona) e distribuire gli host di conseguenza.

Se si assegna un gruppo host a una zona di disponibilità, tutte le macchine virtuali create in tale host devono essere create nella stessa zona.

### <a name="use-fault-domains-for-fault-isolation"></a>Usare i domini di errore per l'isolamento degli errori

Un host può essere creato in un dominio di errore specifico. Analogamente alla macchina virtuale in un set di scalabilità o un set di disponibilità, gli host in domini di errore diversi verranno posizionati su rack fisici diversi nel data center. Quando si crea un gruppo host, è necessario specificare il numero di domini di errore. Quando si creano host all'interno del gruppo host, si assegna il dominio di errore per ogni host. Le macchine virtuali non richiedono alcuna assegnazione di dominio di errore.

I domini di errore non corrispondono a quelli della condivisione percorso. Avere lo stesso dominio di errore per due host non significa che siano vicini tra loro.

I domini di errore hanno come ambito il gruppo host. Si consiglia di non prendere in considerazione l'anti-affinità tra due gruppi host, a meno che non si trovino in zone di disponibilità diverse.

Le macchine virtuali distribuite in host con domini di errore diversi avranno i servizi di dischi gestiti sottostanti su più timbri di archiviazione per aumentare la protezione dell'isolamento degli errori.

### <a name="using-availability-zones-and-fault-domains"></a>Uso di zone di disponibilità e domini di errore

È possibile utilizzare entrambe le funzionalità per ottenere un isolamento degli errori ancora maggiore. In questo caso, è necessario specificare la zona di disponibilità e il numero di domini di errore in per ogni gruppo host, assegnare un dominio di errore a ciascun host del gruppo e assegnare una zona di disponibilità a ciascuna VM.

Il modello di esempio Gestione risorse disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) USA zone e domini di errore per diffondere gli host per la resilienza massima in un'area.


## <a name="manual-vs-automatic-placement"></a>Selezione host manuale e automatica 

> [!IMPORTANT]
> La selezione host automatica è attualmente disponibile in anteprima pubblica.
> Per partecipare all'anteprima, completare il sondaggio sull'onboarding di anteprima all'indirizzo [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) .
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando si crea una macchina virtuale in Azure, è possibile selezionare l'host dedicato da usare. È anche possibile usare l'opzione per collocare automaticamente le macchine virtuali in host esistenti, all'interno di un gruppo host. 

Quando si crea un nuovo gruppo host, assicurarsi che sia selezionata l'impostazione per la selezione host automatica della macchina virtuale. Quando si crea la VM, selezionare il gruppo host e lasciare che Azure scelga l'host migliore per la macchina virtuale. 

Per i gruppi host abilitati per la selezione host automatica non è necessario inserire automaticamente tutte le macchine virtuali. Sarà comunque possibile selezionare in modo esplicito un host anche quando si seleziona selezione host automatica per il gruppo host. 

### <a name="limitations"></a>Limitazioni

Problemi noti e limitazioni quando si usa la selezione host automatica delle macchine virtuali:

- Non sarà possibile applicare i vantaggi di Azure Hybrid per gli host dedicati.
- Non sarà possibile ridistribuire la macchina virtuale. 
- Non sarà possibile controllare la manutenzione per gli host dedicati.
- Non sarà possibile usare macchine virtuali della serie Lsv2, NVasv4, NVsv3, Msv2 o M con host dedicati 


## <a name="virtual-machine-scale-set-support"></a>Supporto del set di scalabilità di macchine virtuali

I set di scalabilità di macchine virtuali consentono di gestire un gruppo di macchine virtuali come una singola risorsa e di applicare i criteri di disponibilità, gestione, scalabilità e orchestrazione come gruppo. È anche possibile usare gli host dedicati esistenti per i set di scalabilità di macchine virtuali. 

> [!IMPORTANT]
> I set di scalabilità di macchine virtuali negli host dedicati sono attualmente in anteprima pubblica.
> Per partecipare all'anteprima, completare il sondaggio sull'onboarding di anteprima all'indirizzo [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) .
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando si crea un set di scalabilità di macchine virtuali, è possibile specificare un gruppo host esistente in modo che tutte le istanze di VM siano create in host dedicati.

Quando si crea un set di scalabilità di macchine virtuali in un gruppo host dedicato, si applicano i requisiti seguenti:

- Il posizionamento automatico delle macchine virtuali deve essere abilitato.
- L'impostazione di disponibilità del gruppo host deve corrispondere al set di scalabilità. 
    - Per i set di scalabilità a livello di area, è necessario usare un gruppo host regionale, creato senza specificare una zona di disponibilità.
    - Il gruppo host e il set di scalabilità devono usare la stessa zona di disponibilità. 
    - Il numero di domini di errore per il livello di gruppo host deve corrispondere al numero di domini di errore per il set di scalabilità. Il portale di Azure consente di specificare la *distribuzione massima* per il set di scalabilità, che imposta il numero di domini di errore 1.
- Per prima cosa è necessario creare host dedicati, con capacità sufficiente e le stesse impostazioni per le zone del set di scalabilità e i domini di errore.
- Le dimensioni delle macchine virtuali supportate per gli host dedicati devono corrispondere a quella usata per il set di scalabilità.

Non tutte le impostazioni dell'orchestrazione e delle ottimizzazioni del set di scalabilità sono supportate da host dedicati. Applicare le impostazioni seguenti al set di scalabilità: 
- Disabilitare il provisioning eccessivo.
- Usare la modalità di orchestrazione ScaleSetVM 
- Non usare i gruppi di posizionamento di prossimità per la condivisione percorso



## <a name="maintenance-control"></a>Controllo della manutenzione

L'infrastruttura che supporta le macchine virtuali può essere occasionalmente aggiornata per migliorare l'affidabilità, le prestazioni, la sicurezza e per avviare nuove funzionalità. La piattaforma Azure tenta di ridurre al minimo l'effetto della manutenzione della piattaforma laddove possibile, ma i clienti con carichi di lavoro *sensibili alla manutenzione* non possono tollerare anche pochi secondi che la macchina virtuale debba essere bloccata o disconnessa per la manutenzione.

Il **controllo di manutenzione** offre ai clienti un'opzione che consente di ignorare gli aggiornamenti regolari della piattaforma pianificati sui rispettivi host dedicati, quindi di applicarli al momento desiderato entro una finestra in sequenza di 35 giorni.

Per ulteriori informazioni, vedere [gestione degli aggiornamenti della piattaforma con il controllo di manutenzione](https://docs.microsoft.com/azure/virtual-machines/maintenance-control).

## <a name="capacity-considerations"></a>Considerazioni sulla capacità

Una volta eseguito il provisioning di un host dedicato, Azure lo assegna al server fisico. Ciò garantisce la disponibilità della capacità quando è necessario effettuare il provisioning della macchina virtuale. Azure usa l'intera capacità nell'area (o zona) per selezionare un server fisico per l'host. Significa anche che i clienti si aspettano di poter espandere il footprint di host dedicato senza preoccuparsi di esaurire lo spazio disponibile nel cluster.

## <a name="quotas"></a>Quote

Quando si distribuisce un host dedicato, vengono utilizzati due tipi di quota.

1. Quota vCPU host dedicata. Il limite di quota predefinito è 3000 vCPU, per area.
1. Quota della famiglia di dimensioni della macchina virtuale. Una sottoscrizione con **pagamento in base** al consumo, ad esempio, può avere solo una quota di 10 vCPU disponibili per la serie di dimensioni Dsv3 nell'area Stati Uniti orientali. Per distribuire un host dedicato Dsv3, è necessario richiedere un aumento della quota ad almeno 64 vCPU prima di poter distribuire l'host dedicato. 

Per richiedere un aumento della quota, creare una richiesta di supporto nella [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

Il provisioning di un host dedicato utilizzerà sia vCPU host dedicato che la quota vCPU della famiglia di macchine virtuali, ma non utilizzerà l'vCPU regionale.


![Screenshot della pagina utilizzo e quote nel portale](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Per ulteriori informazioni, vedere la pagina relativa alle [quote vCPU per le macchine virtuali](/azure/virtual-machines/windows/quotas).

La versione di valutazione gratuita e gli abbonamenti MSDN non hanno una quota per gli host dedicati di Azure.

## <a name="pricing"></a>Prezzi

Gli utenti vengono addebitati in base all'host dedicato, indipendentemente dal numero di macchine virtuali distribuite. Nell'istruzione mensile viene visualizzato un nuovo tipo di risorsa fatturabile degli host. Le macchine virtuali in un host dedicato verranno comunque visualizzate nell'istruzione, ma con un prezzo pari a 0.

Il prezzo dell'host viene impostato in base alla famiglia di VM, al tipo (hardware size) e all'area. Il prezzo di un host è relativo alla dimensione di macchina virtuale più grande supportata nell'host.

L'utilizzo di licenze software, archiviazione e rete viene fatturato separatamente dall'host e dalle macchine virtuali. Non sono state apportate modifiche a tali elementi fatturabili.

Per altre informazioni, vedere [prezzi di host dedicati di Azure](https://aka.ms/ADHPricing).

È anche possibile risparmiare sui costi con un' [istanza riservata di host dedicati di Azure](prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Dimensioni e generazioni hardware

Uno SKU viene definito per un host e rappresenta la serie di dimensioni e il tipo di VM. È possibile combinare più macchine virtuali di dimensioni diverse all'interno di un singolo host, purché siano delle stesse serie di dimensioni. 

Il *tipo* è la generazione dell'hardware. Tipi di hardware diversi per la stessa serie di VM proverranno da diversi fornitori di CPU e con diverse generazioni di CPU e numero di core. 

Le dimensioni e i tipi di hardware variano in base all'area. Per ulteriori informazioni, fare riferimento alla [pagina dei prezzi](https://aka.ms/ADHPricing) dell'host.


## <a name="host-life-cycle"></a>Ciclo di vita dell'host


Azure monitora e gestisce lo stato di integrità degli host. Quando si esegue una query sull'host, verranno restituiti gli Stati seguenti:

| Stato di integrità   | Descrizione       |
|----------|----------------|
| Host disponibile     | Nessun problema noto con l'host.   |
| Host sottoposto a indagine  | Si sono verificati problemi con l'host che si sta cercando. Si tratta di uno stato di transizione necessario per consentire ad Azure di provare e identificare l'ambito e la causa radice del problema identificato. Le macchine virtuali in esecuzione nell'host potrebbero essere interessate. |
| Deallocazione in sospeso host   | Azure non può ripristinare lo stato integro dell'host e richiedere la ridistribuzione delle macchine virtuali da questo host. Se `autoReplaceOnFailure` è abilitato, le macchine virtuali vengono *risanate* in hardware integro. In caso contrario, è possibile che la macchina virtuale sia in esecuzione in un host che sta per avere esito negativo.|
| Host deallocato  | Tutte le macchine virtuali sono state rimosse dall'host. Non vengono più addebitati costi per questo host perché l'hardware è stato escluso dalla rotazione.   |


## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire un host dedicato usando [Azure PowerShell](./windows/dedicated-hosts-powershell.md), il [portale](./windows/dedicated-hosts-portal.md)e l' [interfaccia](./linux/dedicated-hosts-cli.md)della riga di comando di Azure.

- È disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) un modello di esempio che usa sia le zone che i domini di errore per la resilienza massima in un'area.

- È anche possibile risparmiare sui costi con un' [istanza riservata di host dedicati di Azure](prepay-dedicated-hosts-reserved-instances.md).
