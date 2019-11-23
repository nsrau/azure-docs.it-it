---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/21/2019
ms.openlocfilehash: f583796fc353852ef3898e28fa96524e08cfb4ad
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414574"
---
When you commit to an Azure reserved VM instance you can save money. Lo sconto della prenotazione si applica automaticamente alle macchine virtuali in esecuzione corrispondenti all'ambito di prenotazione e agli attributi. You don't need to assign a reservation to a virtual machine to get the discounts. A reserved instance purchase covers only the compute part of your VM usage. For Windows VMs, the usage meter is split into two separate meters. There's a compute meter, which is same as the Linux meter, and a Windows IP meter. The charges that you see when you make the purchase are only for the compute costs. Charges don't include Windows software costs. For more information about software costs, see [Software costs not included with Azure Reserved VM Instances](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinare le dimensioni corrette delle macchine virtuali prima dell'acquisto

Before you buy a reservation, you should determine the size of the VM that you need. The following sections will help you determine the right VM size.

### <a name="use-reservation-recommendations"></a>Use reservation recommendations

You can use reservation recommendations to help determine the reservations you should purchase.

- Purchase recommendations and recommended quantity are show when you purchase a VM reserved instance in the Azure portal.
- Azure Advisor provides purchase recommendations for individual subscriptions.  
- You can use the APIs to get purchase recommendations for both shared scope and single subscription scope. For more information, see [Reserved instance purchase recommendation APIs for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- For Enterprise Agreement (EA) and Microsoft Customer Agreement (MCA) customers, purchase recommendations for shared and single subscription scopes are available with the [Azure Consumption Insights Power BI content pack](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Servizi che ottengono sconti per la prenotazione di macchine virtuali

Le prenotazioni di macchine virtuali possono essere applicate all'utilizzo di macchine virtuali generato da più servizi, non solo per le proprie distribuzioni di macchine virtuali. Le risorse che ottengono sconti per la prenotazione cambiano a seconda dell'impostazione della flessibilità delle dimensioni dell'istanza.

#### <a name="instance-size-flexibility-setting"></a>Impostazione della flessibilità delle dimensioni istanza

L'impostazione della flessibilità delle dimensioni dell'istanza determina quali servizi ottengono gli sconti per le istanze riservate.

Indipendentemente dal fatto che l'impostazione sia attivata o disattivata, gli sconti per la prenotazione vengono applicati automaticamente a qualsiasi utilizzo di macchine virtuali corrispondente quando *ConsumedService* è `Microsoft.Compute`. Controllare quindi i dati di utilizzo per il valore *ConsumedService*. Di seguito sono riportati alcuni esempi:

- Macchine virtuali
- Set di scalabilità di macchine virtuali
- Servizio contenitore
- Distribuzioni di Azure Batch (in modalità sottoscrizioni utente)
- Servizio Azure Kubernetes
- Service Fabric

Quando l'impostazione è attivata, gli sconti per la prenotazione vengono applicati automaticamente all'utilizzo di macchine virtuali corrispondente quando *ConsumedService* è uno degli elementi seguenti:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Controllare il valore *ConsumedService* nei dati di utilizzo per determinare se l'utilizzo è idoneo per gli sconti per la prenotazione.

Per altre informazioni sulla flessibilità delle dimensioni istanza, vedere [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analyze your usage information
Analyze your usage information to help determine which reservations you should purchase.

Usage data is available in the usage file and APIs. Use them together to determine which reservation to purchase. Check for VM instances that have high usage on daily basis to determine the quantity of reservations to purchase.

Avoid the `Meter` subcategory and `Product` fields in usage data. They don't distinguish between VM sizes that use premium storage. If you use these fields to determine the VM size for reservation purchase, you may buy the wrong size. Then you won't get the reservation discount you expect. Instead, refer to the `AdditionalInfo` field in your usage file or usage API to determine the correct VM size.

### <a name="purchase-restriction-considerations"></a>Purchase restriction considerations

Reserved VM Instances are available for most VM sizes with some exceptions. Reservation discounts don't apply for the following VMs:

- **VM series** - A-series, Av2-series, or G-series.

- **Preview or Promo VMs** - Any VM-series or size that is in preview or uses promotional meter.

- **Clouds** - Reservations aren't available for purchase in Germany or China regions.

- **Insufficient quota** - A reservation that is scoped to a single subscription must have vCPU quota available in the subscription for the new RI. Ad esempio, se la sottoscrizione di destinazione ha un limite di quota pari a 10 vCPU per la serie D, non sarà possibile acquistare una prenotazione per 11 istanze Standard_D1. Il controllo della quota per le prenotazioni include le macchine virtuali già distribuite nella sottoscrizione. Ad esempio, se la sottoscrizione include una quota di 10 vCPU per la serie D e distribuisce due istanze Standard_D1, sarà possibile acquistare una prenotazione per le 10 istanze Standard_D1 nella sottoscrizione. You can [create quote increase request](../articles/azure-supportability/resource-manager-core-quotas-request.md) to resolve this issue.

- **Capacity restrictions** - In rare circumstances, Azure limits the purchase of new reservations for subset of VM sizes, because of low capacity in a region.

## <a name="buy-a-reserved-vm-instance"></a>Acquistare un'istanza di macchina virtuale riservata

You can buy a reserved VM instance in the [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Usare [pagamenti anticipati o mensili](../articles/billing/billing-monthly-payments-reservations.md) per acquistare la prenotazione.
These requirements apply to buying a reserved VM instance:

- You must be in an Owner role for at least one EA subscription or a subscription with a pay-as-you-go rate.
- For EA subscriptions, the **Add Reserved Instances** option must be enabled in the [EA portal](https://ea.azure.com/). Or, if that setting is disabled, you must be an EA Admin for the subscription.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare le prenotazioni.

Per acquistare un'istanza:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** > **Prenotazioni**.
1. Select **Add** to purchase a new reservation and then click **Virtual machine**.
1. Compilare i campi obbligatori. Lo sconto relativo alla prenotazione viene applicato alle istanze di macchine virtuali in esecuzione che corrispondono agli attributi. Il numero di istanze di macchine virtuali a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

If you have an EA agreement, you can use the **Add more option** to quickly add additional instances. The option isn't available for other subscription types.


| Campo      | Description|
|------------|--------------|
|Sottoscrizione|La sottoscrizione usata per pagare la prenotazione. The payment method on the subscription is charged the costs for the reservation. The subscription type must be an enterprise agreement (offer numbers: MS-AZR-0017P or MS-AZR-0148P) or Microsoft Customer Agreement or an individual subscription with pay-as-you-go rates (offer numbers: MS-AZR-0003P or MS-AZR-0023P). The charges are deducted from the monetary commitment balance, if available, or charged as overage. For a subscription with pay-as-you-go rates, the charges are billed to the credit card or invoice payment method on the subscription.|    
|Scope       |L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>**Gruppo di risorse singolo**: lo sconto per la prenotazione si applica solo alle risorse corrispondenti incluse nel gruppo di risorse selezionato.</li><li>**Sottoscrizione singola**: lo sconto della prenotazione viene applicato alle risorse corrispondenti incluse nella sottoscrizione selezionata.</li><li>**Condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione. For EA customers, the billing context is the enrollment. Per le singole sottoscrizioni con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.</li></ul>|
|Area geografica    |L'area di Azure coperta dalla prenotazione.|    
|Dimensioni VM     |Le dimensioni delle istanze della macchina virtuale.|
|Ottimizza per     |VM instance size flexibility is selected by default. Click **Advanced settings** to change the instance size flexibility value to apply the reservation discount to other VMs in the same [VM size group](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). La priorità di capacità assegna la capacità del data center dando priorità alle distribuzioni. It offers additional confidence in your ability to launch the VM instances when you need them. La priorità di capacità è disponibile solo quando l'ambito della prenotazione è sottoscrizione singola. |
|Durata        |Un anno o tre anni.|
|Quantità    |Il numero di istanze acquistate all'interno della prenotazione. La quantità è il numero di istanze di macchina virtuale in esecuzione che possono ottenere lo sconto sulla fatturazione. For example, if you are running 10 Standard_D2 VMs in the East US, then you would specify quantity as 10 to maximize the benefit for all running VMs. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Usage data and reservation utilization

I dati di utilizzo hanno un prezzo effettivo pari a zero per l'utilizzo che ottiene uno sconto sulla prenotazione. You can see which VM instance received the reservation discount for each reservation.

For more information about how reservation discounts appear in usage data, see [Understand Azure reservation usage for your Enterprise enrollment](../articles/billing/billing-understand-reserved-instance-usage-ea.md) if you are an EA customer. If you have an individual subscription, see [Understand Azure reservation usage for your Pay-As-You-Go subscription](../articles/billing/billing-understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Change a reservation after purchase

Dopo l'acquisto, a una prenotazione è possibile apportare i tipi di modifiche seguenti:

- Aggiornare l'ambito della prenotazione
- Instance size flexibility (if applicable)
- Ownership

You can also split a reservation into smaller chunks and merge already split reservations. None of the changes cause a new commercial transaction or change the end date of the reservation.

You can't make the following types of changes after purchase, directly:

- An existing reservation’s region
- SKU
- Quantità
- Duration

However, you can *exchange* a reservation if you want to make changes.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Cambi self-service e rimborsi per le prenotazioni di Azure](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Opzioni per Contatti.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
    - [Informazioni sulle prenotazioni di Azure](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Gestire le prenotazioni in Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Informazioni su come viene applicato lo sconto sulla prenotazione](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Costi del software Windows non inclusi nelle prenotazioni](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)
