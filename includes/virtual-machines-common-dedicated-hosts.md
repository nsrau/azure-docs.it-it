---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/26/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d27b3613acb2980ff4116825197d018f9c183baa
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266858"
---
## <a name="benefits"></a>Vantaggi 

Per riservare l'intero host sono disponibili i vantaggi seguenti:

-   Isolamento hardware a livello di server fisico. Non verranno inserite altre macchine virtuali negli host. Gli host dedicati vengono distribuiti negli stessi Data Center e condividono la stessa rete e l'infrastruttura di archiviazione sottostante come altri host non isolati.
-   Controllo sugli eventi di manutenzione avviati dalla piattaforma Azure. Sebbene la maggior parte degli eventi di manutenzione non abbia alcun effetto sulle macchine virtuali, esistono alcuni carichi di lavoro sensibili in cui ogni secondo di pausa può avere un certo effetto. Con gli host dedicati è possibile acconsentire esplicitamente a una finestra di manutenzione per ridurre l'effetto sul servizio.
-   Con il vantaggio Azure Hybrid puoi usare licenze personalizzate per Windows e SQL in Azure. L'uso dei vantaggi ibridi offre vantaggi aggiuntivi. Per ulteriori informazioni, vedere [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).



## <a name="groups-hosts-and-vms"></a>Gruppi, host e macchine virtuali  

![Visualizzazione delle nuove risorse per gli host dedicati.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Un **gruppo host** è una risorsa che rappresenta una raccolta di host dedicati. È possibile creare un gruppo host in un'area e una zona di disponibilità e aggiungervi host.

Un **host** è una risorsa, mappata a un server fisico in un Data Center di Azure. Il server fisico viene allocato quando viene creato l'host. Un host viene creato all'interno di un gruppo host. Un host dispone di uno SKU che descrive le dimensioni della macchina virtuale che è possibile creare. Ogni host può ospitare più macchine virtuali di dimensioni diverse, purché provengano dalla stessa serie di dimensioni.

Quando si crea una macchina virtuale in Azure, è possibile selezionare l'host dedicato da usare per la macchina virtuale. Si dispone del controllo completo su quali macchine virtuali vengono posizionate negli host.


## <a name="high-availability-considerations"></a>Considerazioni sulla disponibilità elevata 

Per la disponibilità elevata, è consigliabile distribuire più macchine virtuali, distribuite tra più host (almeno 2). Con gli host dedicati di Azure sono disponibili diverse opzioni per eseguire il provisioning dell'infrastruttura per definire i limiti di isolamento degli errori.

### <a name="use-availability-zones-for-fault-isolation"></a>USA zone di disponibilità per l'isolamento degli errori

Le zone di disponibilità sono posizioni fisiche univoche all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Un gruppo host viene creato in una singola zona di disponibilità. Una volta creati, tutti gli host saranno posizionati all'interno di tale area. Per ottenere una disponibilità elevata tra le zone, è necessario creare più gruppi host (uno per zona) e distribuire gli host di conseguenza.

Se si assegna un gruppo host a una zona di disponibilità, tutte le macchine virtuali create in tale host devono essere create nella stessa zona.

### <a name="use-fault-domains-for-fault-isolation"></a>Usare i domini di errore per l'isolamento degli errori

Un host può essere creato in un dominio di errore specifico. Analogamente alla macchina virtuale in un set di scalabilità o un set di disponibilità, gli host in domini di errore diversi verranno posizionati su rack fisici diversi nel data center. Quando si crea un gruppo host, è necessario specificare il numero di domini di errore. Quando si creano host all'interno del gruppo host, si assegna il dominio di errore per ogni host. Le macchine virtuali non richiedono alcuna assegnazione di dominio di errore.

I domini di errore non corrispondono alla collocazione. Avere lo stesso dominio di errore per due host non significa che siano vicini tra loro.

I domini di errore hanno come ambito il gruppo host. Si consiglia di non prendere in considerazione l'anti-affinità tra due gruppi host, a meno che non si trovino in zone di disponibilità diverse.

Le macchine virtuali distribuite in host con domini di errore diversi avranno i servizi di dischi gestiti sottostanti su più timbri di archiviazione per aumentare la protezione dell'isolamento degli errori.

### <a name="using-availability-zones-and-fault-domains"></a>Uso di zone di disponibilità e domini di errore

È possibile utilizzare entrambe le funzionalità per ottenere un isolamento degli errori ancora maggiore. In questo caso, è necessario specificare la zona di disponibilità e il numero di domini di errore in per ogni gruppo host, assegnare un dominio di errore a ciascun host del gruppo e assegnare una zona di disponibilità a ciascuna VM.

Il modello di esempio Gestione risorse disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) USA zone e domini di errore per diffondere gli host per la resilienza massima in un'area.

## <a name="maintenance-control"></a>Controllo manutenzione

L'infrastruttura che supporta le macchine virtuali può essere occasionalmente aggiornata per migliorare l'affidabilità, le prestazioni, la sicurezza e per avviare nuove funzionalità. La piattaforma Azure tenta di ridurre al minimo l'effetto della manutenzione della piattaforma laddove possibile, ma i clienti con carichi di lavoro *sensibili alla manutenzione* non possono tollerare anche pochi secondi che la macchina virtuale debba essere bloccata o disconnessa per la manutenzione.

Il **controllo di manutenzione** offre ai clienti un'opzione che consente di ignorare gli aggiornamenti regolari della piattaforma pianificati sui rispettivi host dedicati, quindi di applicarli al momento desiderato entro una finestra in sequenza di 35 giorni.

> [!NOTE]
>  Il controllo della manutenzione si trova attualmente in una fase di anteprima limitata e richiede un processo di onboarding. Per questa anteprima, inviare un [sondaggio di candidatura](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u).

## <a name="capacity-considerations"></a>Considerazioni sulla capacità

Una volta eseguito il provisioning di un host dedicato, Azure lo assegna al server fisico. Ciò garantisce la disponibilità della capacità quando è necessario effettuare il provisioning della macchina virtuale. Azure usa l'intera capacità nell'area (o zona) per selezionare un server fisico per l'host. Significa anche che i clienti si aspettano di poter espandere il footprint di host dedicato senza preoccuparsi di esaurire lo spazio disponibile nel cluster.

## <a name="quotas"></a>Quote

È previsto un limite di quota predefinito di 3000 vCPU per gli host dedicati, per area. Tuttavia, il numero di host che è possibile distribuire è limitato dalla quota per la famiglia di dimensioni di VM usata per l'host. Una sottoscrizione con **pagamento in base** al consumo, ad esempio, può avere solo una quota di 10 vCPU disponibili per la serie di dimensioni Dsv3 nell'area Stati Uniti orientali. In questo caso, è necessario richiedere un aumento della quota ad almeno 64 vCPU prima di poter distribuire un host dedicato. Selezionare il pulsante **Richiedi aumento** nell'angolo superiore destro per archiviare una richiesta, se necessario.

![Screenshot della pagina utilizzo e quote nel portale](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Per ulteriori informazioni, vedere la pagina relativa alle [quote vCPU per le macchine virtuali](/azure/virtual-machines/windows/quotas).

## <a name="pricing"></a>Prezzi

Gli utenti vengono addebitati in base all'host dedicato, indipendentemente dal numero di macchine virtuali distribuite. Nell'istruzione mensile viene visualizzato un nuovo tipo di risorsa fatturabile degli host. Le macchine virtuali in un host dedicato verranno comunque visualizzate nell'istruzione, ma con un prezzo pari a 0.

Il prezzo dell'host viene impostato in base alla famiglia di VM, al tipo (hardware size) e all'area. Il prezzo di un host è relativo alla dimensione di macchina virtuale più grande supportata nell'host.

L'utilizzo di licenze software, archiviazione e rete viene fatturato separatamente dall'host e dalle macchine virtuali. Non sono state apportate modifiche a tali elementi fatturabili.

Per altre informazioni, vedere [prezzi di host dedicati di Azure](https://aka.ms/ADHPricing).
 
## <a name="vm-families-and-hardware-generations"></a>Gruppi di macchine virtuali e generazioni hardware

Uno SKU viene definito per un host e rappresenta la serie di dimensioni e il tipo di VM. È possibile combinare più macchine virtuali di dimensioni diverse all'interno di un singolo host, purché siano delle stesse serie di dimensioni. Il tipo è la generazione hardware attualmente disponibile nell'area.

Diversi `types` per la stessa serie di VM proverranno da diversi fornitori di CPU e con diverse generazioni di CPU e numero di core.

Per altre informazioni, vedere la pagina relativa ai [prezzi](https://aka.ms/ADHPricing) per gli host.

Durante l'anteprima, si supporterà il SKU\types host seguente:  DSv3_Type1 e ESv3_Type1

 
## <a name="host-life-cycle"></a>Ciclo di vita dell'host


Azure monitora e gestisce lo stato di integrità degli host. Quando si esegue una query sull'host, verranno restituiti gli Stati seguenti:

| Stato di integrità   | Descrizione       |
|----------|----------------|
| Host disponibile     | Nessun problema noto con l'host.   |
| Host sottoposto a indagine  | Si sono verificati problemi con l'host che si sta cercando. Si tratta di uno stato di transizione necessario per consentire ad Azure di provare e identificare l'ambito e la causa radice del problema identificato. Le macchine virtuali in esecuzione nell'host potrebbero essere interessate. |
| Deallocazione in sospeso host   | Azure non può ripristinare lo stato integro dell'host e richiedere la ridistribuzione delle macchine virtuali da questo host. Se `autoReplaceOnFailure` è abilitato, le macchine virtuali vengono *risanate* in hardware integro. In caso contrario, è possibile che la macchina virtuale sia in esecuzione in un host che sta per avere esito negativo.|
| Host deallocato  | Tutte le macchine virtuali sono state rimosse dall'host. Non vengono più addebitati costi per questo host perché l'hardware è stato escluso dalla rotazione.   |

