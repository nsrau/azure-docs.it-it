---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 07/01/2019
ms.openlocfilehash: 9e0caa8b98133dad3af083e8910d0603bbd2563b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67489924"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Pagare in anticipo le macchine virtuali con istanze di macchina virtuale riservate di Azure (RI)

Pagare in anticipo le macchine virtuali e risparmiare sui costi tramite le istanze di macchina virtuale riservate di Azure. Per altre informazioni, vedere [Istanze di macchina virtuale riservate di Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/).

È possibile acquistare un'istanza di macchina virtuale riservata nel [portale di Azure](https://portal.azure.com). Per acquistare un'istanza:

- È necessario essere un ruolo di proprietario per almeno una sottoscrizione Enterprise o una sottoscrizione con una tariffa con pagamento a consumo.
- Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare le prenotazioni.

Lo sconto della prenotazione si applica automaticamente alle macchine virtuali in esecuzione corrispondenti all'ambito di prenotazione e agli attributi. È possibile aggiornare l'ambito della prenotazione nel [portale di Azure](https://portal.azure.com), in PowerShell, nell'interfaccia della riga di comando o tramite le API.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinare le dimensioni corrette delle macchine virtuali prima dell'acquisto

Prima di acquistare una prenotazione, è necessario determinare le dimensioni della macchina virtuale che è necessario. Le sezioni seguenti verranno consentono di determinare le dimensioni VM a destra.

### <a name="use-reservation-recommendations"></a>Usare le raccomandazioni per la prenotazione

È possibile usare le raccomandazioni di prenotazione per determinare le prenotazioni che è possibile acquistare.

- Consigli per gli acquisti e quantità consigliata sono mostrano quando si acquista un'istanza riservata di macchina virtuale nel portale di Azure.
- Azure Advisor fornisce consigli per gli acquisti di sottoscrizioni individuali.  
- È possibile usare le API per ottenere consigli per gli acquisti per ambito condiviso e ambito della sottoscrizione singola. Per altre informazioni, vedere [riservato raccomandazione di acquisto di istanza API per clienti enterprise](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Per i clienti con contratto Enterprise Agreement, acquistare le raccomandazioni per condivise e gli ambiti di sottoscrizione singolo sono disponibili con il [pacchetto di contenuto di Power BI Insights il consumo di Azure](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="classic-vms-and-cloud-services"></a>Servizi cloud e macchine virtuali classiche

Le istanze di macchina virtuale riservate automaticamente si applicano a entrambe le macchine virtuali classiche e servizi cloud quando è abilitata la flessibilità di dimensioni di istanza. Non sono qualsiasi SKU speciali per le macchine virtuali classiche o servizi cloud. Lo stesso SKU della macchina virtuale si applicano a essi.

Ad esempio, si potrebbe convertire le macchine virtuali classiche o i servizi cloud alle macchine virtuali basate su Azure Resource Manager. In questo esempio, lo sconto di prenotazione viene applicato automaticamente a macchine virtuali corrispondenti. Non è necessario per *exchange* un'istanza riservata esistente - applica automaticamente.

### <a name="analyze-your-usage-information"></a>Analizzare le informazioni sull'utilizzo
È necessario analizzare le informazioni sull'utilizzo per consentire di determinare quali prenotazioni è possibile acquistare.

I dati di utilizzo sono disponibili nel file di utilizzo delle API. Usarli insieme per determinare quali prenotazione per l'acquisto. È necessario controllare istanze delle macchine Virtuali che hanno un utilizzo elevato su base giornaliera per determinare la quantità di prenotazione per l'acquisto.

Evitare il `Meter` subcategory e `Product` i campi nei dati di utilizzo. Essi non distingue tra le dimensioni di VM che usano archiviazione premium. Se si usano questi campi per determinare le dimensioni di macchina virtuale per l'acquisto della prenotazione, è possibile acquistare dimensione non corretta. Quindi si otterranno lo sconto di prenotazione che previsti. Vedere invece il `AdditionalInfo` campo nel file di utilizzo o API di utilizzo per determinare le dimensioni VM corretta.

### <a name="purchase-restriction-considerations"></a>Considerazioni sulla restrizione di acquisto

Istanze di VM riservate sono disponibili per la maggior parte delle dimensioni delle macchine Virtuali con alcune eccezioni. Gli sconti di prenotazione non si applicano per le macchine virtuali seguenti:

- **Macchine virtuali della serie** -serie a, serie Av2 o serie G.

- **Le macchine virtuali in fase di anteprima** -qualsiasi serie di macchine Virtuali o le dimensioni che sono disponibile in anteprima.

- **Cloud** -prenotazioni non sono disponibili per l'acquisto nelle aree Germania o Cina.

- **Quota insufficiente** -una prenotazione che ha come ambita una singola sottoscrizione deve avere una quota di vCPU disponibile nella sottoscrizione per la nuova istanza riservata. Ad esempio, se la sottoscrizione di destinazione ha un limite di quota pari a 10 vCPU per la serie D, non sarà possibile acquistare una prenotazione per 11 istanze Standard_D1. Il controllo della quota per le prenotazioni include le macchine virtuali già distribuite nella sottoscrizione. Ad esempio, se la sottoscrizione include una quota di 10 vCPU per la serie D e distribuisce due istanze Standard_D1, sarà possibile acquistare una prenotazione per le 10 istanze Standard_D1 nella sottoscrizione. È possibile [creare un'offerta di richiesta di aumento](../articles/azure-supportability/resource-manager-core-quotas-request.md) per risolvere il problema.

- **Restrizioni della capacità** : In rare circostanze, i limiti di Azure l'acquisto di prenotazioni di nuovo per subset di macchina virtuale con dimensioni, a causa della bassa capacità in un'area.

## <a name="buy-a-reserved-vm-instance"></a>Acquistare un'istanza di macchina virtuale riservata

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare **Aggiungi** per acquistare una nuova prenotazione.
4. Immettere i campi obbligatori. Lo sconto relativo alla prenotazione viene applicato alle istanze di macchine virtuali in esecuzione che corrispondono agli attributi. Il numero di istanze di macchine virtuali a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

    | Campo      | Descrizione|
    |------------|--------------|
    |NOME        |Il nome della prenotazione.|
    |Sottoscrizione|La sottoscrizione usata per pagare la prenotazione. L'acquisto delle istanze di macchina virtuale riservate viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri offerta: MS-AZR-0017P o MS-AZR - 0148p) o una singola sottoscrizione con tariffe a consumo (numeri dell'offerta: MS-AZR-0003P o MS-AZR-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Per una sottoscrizione con tariffe a consumo, il costo viene addebitato al metodo di pagamento della fattura o carta di credito alla sottoscrizione.|    
    |`Scope`       |L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>Sottoscrizione singola: lo sconto relativo alla prenotazione viene applicato alle macchine virtuali in questa sottoscrizione. </li><li>Condivisa: lo sconto relativo alla prenotazione viene applicato alle macchine virtuali in tutte le sottoscrizione all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con le sottoscrizioni con tariffe a consumo, l'ambito condiviso è tutte le sottoscrizioni con tariffe a consumo create dall'amministratore dell'account.</li></ul>|
    |Region    |L'area di Azure coperta dalla prenotazione.|    
    |Dimensioni macchina virtuale     |Le dimensioni delle istanze della macchina virtuale.|
    |Ottimizza per     |La flessibilità dell'istanza di macchina virtuale applicherà lo sconto di prenotazione ad altre macchine virtuali dello stesso [gruppo di macchine virtuali](https://aka.ms/RIVMGroups). La priorità di capacità assegna la capacità del data center dando priorità alle distribuzioni. Aumenta così la certezza di avere la possibilità di avviare le istanze di macchina virtuale quando servono. La priorità di capacità è disponibile solo quando l'ambito della prenotazione è sottoscrizione singola. |
    |Nome        |Un anno o tre anni.|
    |Quantità    |Il numero di istanze acquistate all'interno della prenotazione. La quantità è il numero di istanze di macchina virtuale in esecuzione che possono ottenere lo sconto sulla fatturazione. Ad esempio, se si eseguono 10 macchine virtuali Standard_D2 negli Stati Uniti orientali, si specificherà 10 come quantità per ottimizzare lo sconto per tutte le macchine in esecuzione. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>Modificare una prenotazione dopo l'acquisto

È possibile apportare le seguenti modifiche a una prenotazione dopo l'acquisto:

- Aggiornare l'ambito della prenotazione
- Flessibilità delle dimensioni dell'istanza (se applicabile)
- Proprietà

È inoltre possibile dividere una prenotazione in blocchi più piccoli e l'unione già dividere le prenotazioni. Nessuna modifica generare una nuova transazione commerciale o modificare la data di fine della prenotazione.

Dopo l'acquisto direttamente, è possibile apportare le seguenti modifiche:

- Area della prenotazione esistente
- SKU
- Quantità
- Duration

Tuttavia, è possibile *exchange* una prenotazione se si desidera apportare modifiche.

## <a name="cancellations-and-exchanges"></a>Annullamenti e scambi

Se è necessario annullare la prenotazione, potrebbe venire applicata una quota del 12% per l'interruzione anticipata. I rimborsi si basano sul prezzo più basso tra il prezzo di acquisto o il prezzo corrente della prenotazione. I rimborsi sono limitati a $50.000 dollari all'anno. Il rimborso ricevuto corrisponde al saldo ripartito rimanente meno la quota del 12% per l'interruzione anticipata. Per richiedere un annullamento, andare alla prenotazione nel portale di Azure e selezionare **Rimborso** per creare una richiesta di supporto.

Se è necessario modificare la prenotazione delle istanze di macchine virtuali riservate scegliendo un'area, un gruppo di dimensioni di macchine virtuali o un periodo diverso, è possibile effettuare uno scambio con un'altra prenotazione di valore uguale o superiore. Come data di inizio del periodo della nuova prenotazione non viene conservata quella della prenotazione scambiata. Il periodo di uno o tre anni inizia dal momento della creazione della nuova prenotazione. Per richiedere uno scambio, passare alla prenotazione nel portale di Azure e selezionare **Scambio** per creare una richiesta di supporto.

Per altre informazioni su come le prenotazioni di exchange o un rimborso, vedere [scambi di prenotazione e rimborsi](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
    - [Informazioni sulle prenotazioni di Azure](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Gestire le prenotazioni in Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Informazioni su come viene applicato lo sconto sulla prenotazione](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Comprendere l'utilizzo della prenotazione per una sottoscrizione con tariffe a consumo](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Costi del software Windows non inclusi nelle prenotazioni](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)
