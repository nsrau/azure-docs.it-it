---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2daaf9bbdf90029f0aad4333ab94e2d1d1d3d7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128719"
---
## <a name="limitations"></a>Limitazioni

- I set di scalabilità delle macchine virtuali non sono attualmente supportati in host dedicati.

## <a name="benefits"></a>Vantaggi 

La prenotazione dell'intero host offre i seguenti vantaggi:

-   Isolamento hardware a livello di server fisico. Nessun'altra macchine virtuali verrà inserita nei host. Gli host dedicati vengono distribuiti negli stessi data center e condividono la stessa rete e la stessa infrastruttura di archiviazione sottostante di altri host non isolati.
-   Controllo sugli eventi di manutenzione avviati dalla piattaforma Azure.Control over maintenance events initiated by the Azure platform. Sebbene la maggior parte degli eventi di manutenzione abbia un impatto minimo o nessun sulle macchine virtuali, esistono alcuni carichi di lavoro sensibili in cui ogni secondo di pausa può avere un impatto. Con gli host dedicati, è possibile acconsentire esplicitamente a una finestra di manutenzione per ridurre l'impatto sul servizio.
-   Con il vantaggio ibrido di Azure, è possibile portare le proprie licenze per Windows e SQL in Azure.With the Azure hybrid benefit, you can bring your own licenses for Windows and SQL to Azure. L'utilizzo dei vantaggi ibridi offre ulteriori vantaggi. Per altre informazioni, vedere Vantaggi di Azure Hybrid .For more [information,](https://azure.microsoft.com/pricing/hybrid-benefit/)see Azure Hybrid Benefit .


## <a name="groups-hosts-and-vms"></a>Gruppi, host e macchine virtuali  

![Visualizzazione delle nuove risorse per gli host dedicati.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Un **gruppo host** è una risorsa che rappresenta una raccolta di host dedicati. Creare un gruppo host in un'area e una zona di disponibilità e aggiungervi host.

Un host è una risorsa, mappata a un server fisico in un data center di Azure.A **host** is a resource, mapped to a physical server in an Azure data center. Il server fisico viene allocato al momento della creazione dell'host. Viene creato un host all'interno di un gruppo host. Un host ha uno SKU che descrive le dimensioni della macchina virtuale che possono essere create. Ogni host può ospitare più macchine virtuali di dimensioni diverse, purché provengano dalla stessa serie di dimensioni.

Quando si crea una macchina virtuale in Azure, è possibile selezionare l'host dedicato da usare per la macchina virtuale. Si dispone del controllo completo su quali macchine virtuali vengono inserite nei host.


## <a name="high-availability-considerations"></a>Considerazioni sulla disponibilità elevataHigh Availability considerations 

Per la disponibilità elevata, è consigliabile distribuire più macchine virtuali distribuite tra più host (almeno 2). Con gli host dedicati di Azure sono disponibili diverse opzioni per eseguire il provisioning dell'infrastruttura per modellare i limiti di isolamento degli errori.

### <a name="use-availability-zones-for-fault-isolation"></a>Utilizzare le zone di disponibilità per l'isolamento degli erroriUse Availability Zones for fault isolation

Le zone di disponibilità sono posizioni fisiche univoche all'interno di un'area di Azure.Availability zones are unique physical locations within an Azure region. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Un gruppo host viene creato in una singola zona di disponibilità. Una volta creati, tutti gli host verranno posizionati all'interno di tale zona. Per ottenere la disponibilità elevata tra zone, è necessario creare più gruppi host (uno per zona) e distribuire gli host di conseguenza.

Se si assegna un gruppo host a una zona di disponibilità, tutte le macchine virtuali create in tale host devono essere create nella stessa zona.

### <a name="use-fault-domains-for-fault-isolation"></a>Usare i domini di errore per l'isolamento degli erroriUse Fault Domains for fault isolation

È possibile creare un host in un dominio di errore specifico. Analogamente alla macchina virtuale in un set di scalabilità o in un set di disponibilità, gli host in domini di errore diversi verranno posizionati in rack fisici diversi nel data center. Quando si crea un gruppo host, è necessario specificare il conteggio dei domini di errore. Quando si creano host all'interno del gruppo host, si assegna il dominio di errore per ogni host. Le macchine virtuali non richiedono alcuna assegnazione di dominio di errore.

I domini di errore non sono uguali alla collocazione. Avere lo stesso dominio di errore per due host non significa che siano in prossimità l'uno dell'altro.

L'ambito dei domini di errore è il gruppo host. Non è consigliabile fare supposizioni sull'anti-affinità tra due gruppi host (a meno che non si trovino in zone di disponibilità diverse).

Le macchine virtuali distribuite in host con domini di errore diversi disfaranno i servizi di dischi gestiti sottostanti su più indicatori di archiviazione, per aumentare la protezione dall'isolamento degli errori.

### <a name="using-availability-zones-and-fault-domains"></a>Utilizzo di zone di disponibilità e domini di erroreUsing Availability zones and Fault Domains

È possibile utilizzare entrambe le funzionalità insieme per ottenere ancora più isolamento degli errori. In questo caso, si specificherà la zona di disponibilità e il numero di domini di errore per ogni gruppo host, si assegnerà un dominio di errore a ogni host nel gruppo e si assegnerà una zona di disponibilità a ognuna delle macchine virtuali

Il modello di esempio di Resource Manager disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) usa zone e domini di errore per distribuire gli host per la massima resilienza in un'area.

## <a name="maintenance-control"></a>Controllo della manutenzione

L'infrastruttura che supporta le macchine virtuali può occasionalmente essere aggiornata per migliorare l'affidabilità, le prestazioni, la sicurezza e per avviare nuove funzionalità. La piattaforma Azure tenta di ridurre al minimo l'impatto della manutenzione della piattaforma quando possibile, ma i clienti con carichi di lavoro sensibili alla *manutenzione* non possono tollerare nemmeno pochi secondi che la macchina virtuale deve essere bloccata o disconnessa per la manutenzione.

**Il controllo** di manutenzione offre ai clienti la possibilità di saltare gli aggiornamenti regolari della piattaforma pianificati sui loro host dedicati, quindi applicarli al momento della loro scelta all'interno di una finestra di 35 giorni.

> [!NOTE]
>  Il controllo della manutenzione è attualmente in anteprima pubblica. Per altre informazioni, vedere **Controllare gli aggiornamenti con il controllo di manutenzione tramite [l'interfaccia della riga di comando](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) o [PowerShell.For](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)** more information, see Control updates with Maintenance Control using CLI or PowerShell .

## <a name="capacity-considerations"></a>Considerazioni sulla capacità

Dopo il provisioning di un host dedicato, Azure lo assegna al server fisico. Ciò garantisce la disponibilità della capacità quando è necessario eseguire il provisioning della macchina virtuale. Azure usa l'intera capacità nell'area (o zona) per selezionare un server fisico per l'host. Significa anche che i clienti possono aspettarsi di aumentare il loro footprint host dedicato senza la preoccupazione di esaurire lo spazio nel cluster.

## <a name="quotas"></a>Quote

Esiste un limite di quota predefinito di 3000 vCPU per gli host dedicati, per ogni area. Tuttavia, il numero di host che è possibile distribuire è limitato anche dalla quota per la famiglia di dimensioni della macchina virtuale utilizzata per l'host. Ad esempio, una sottoscrizione con pagamento in base al **riutilizzo** può avere solo una quota di 10 vCPU disponibili per la serie di dimensioni Dsv3, nell'area Stati Uniti orientali. In questo caso, è necessario richiedere un aumento della quota ad almeno 64 vCPU prima di poter distribuire un host dedicato. Selezionare il pulsante **Richiedi aumento** nell'angolo in alto a destra per presentare una richiesta, se necessario.

![Screenshot della pagina Utilizzo e quote nel portale](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Per ulteriori informazioni, vedere [Quote vCPU della macchina virtuale](/azure/virtual-machines/windows/quotas).

Gli abbonamenti di valutazione gratuita e MSDN non hanno quota per gli host dedicati di Azure.Free trial and MSDN subscriptions do not have quota for Azure Dedicated Hosts.

## <a name="pricing"></a>Prezzi

Agli utenti viene addebitato un costo per ogni host dedicato, indipendentemente dal numero di macchine virtuali distribuite. Nell'estratto conto mensile verrà visualizzato un nuovo tipo di risorsa fatturabile di host. Le macchine virtuali in un host dedicato verranno comunque visualizzate nell'estratto conto, ma avranno un prezzo pari a 0.The VMs on a dedicated host will still be shown in your statement, but will carry a price of 0.

Il prezzo host viene impostato in base alla famiglia di macchine virtuali, al tipo (dimensione hardware) e all'area. Il prezzo di un host è relativo alla dimensione massima della macchina virtuale supportata nell'host.

Le licenze software, l'archiviazione e l'utilizzo della rete vengono fatturati separatamente dall'host e dalle macchine virtuali. Non viene apportata alcuna modifica a tali articoli fatturabili.

Per altre informazioni, vedere [Prezzi dell'host dedicato di Azure.For more](https://aka.ms/ADHPricing)information, see Azure Dedicated Host pricing .

È anche possibile risparmiare sui costi con [un'istanza riservata di host dedicati di Azure](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Dimensioni e generazioni di hardware

Uno SKU viene definito per un host e rappresenta la serie e il tipo di dimensioni della macchina virtuale. È possibile combinare più macchine virtuali di dimensioni diverse all'interno di un singolo host, purché siano della stessa serie di dimensioni. 

Il *tipo* è la generazione dell'hardware. Tipi di hardware diversi per la stessa serie di macchine virtuali provengono da fornitori di CPU diversi e hanno generazioni di CPU e numero di core diversi. 

Le dimensioni e i tipi di hardware variano in base all'area geografica. Per ulteriori informazioni, consulta la [pagina dei prezzi](https://aka.ms/ADHPricing) host.


## <a name="host-life-cycle"></a>Ciclo di vita dell'ospite


Azure monitora e gestisce lo stato di integrità degli host. I seguenti stati verranno restituiti quando si esegue una query sull'host:

| Stato di integrità   | Descrizione       |
|----------|----------------|
| Host disponibile     | Non ci sono problemi noti con l'host.   |
| Host sotto indagine  | Stiamo avendo alcuni problemi con l'host che stiamo esaminando. Si tratta di uno stato di transizione necessario per Azure per tentare di identificare l'ambito e la causa radice del problema identificato. Le macchine virtuali in esecuzione nell'host potrebbero essere interessate. |
| Deallocazione host in sospeso   | Azure non può ripristinare l'host a uno stato integro e chiedere di ridistribuire le macchine virtuali da questo host. Se `autoReplaceOnFailure` è abilitata, le macchine virtuali vengono *servite* a hardware integro. In caso contrario, la macchina virtuale potrebbe essere in esecuzione in un host che sta per non riuscire.|
| Host deallocato  | Tutte le macchine virtuali sono state rimosse dall'host. Non ti viene più addebitato alcun costo per questo host poiché l'hardware è stato emesso dalla rotazione.   |

