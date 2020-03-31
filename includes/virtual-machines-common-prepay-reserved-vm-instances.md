---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/27/2020
ms.openlocfilehash: d41affc55134ad34c325c12ab4a14f4013c58f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371655"
---
Quando si esegue il commit in un'istanza di macchina virtuale riservata di Azure, è possibile risparmiare denaro. Lo sconto della prenotazione si applica automaticamente alle macchine virtuali in esecuzione corrispondenti all'ambito di prenotazione e agli attributi. Non è necessario assegnare una prenotazione a una macchina virtuale per ottenere gli sconti. Un acquisto di istanze riservate copre solo la parte di calcolo dell'utilizzo della macchina virtuale. Per le macchine virtuali Windows, il misuratore di utilizzo viene suddiviso in due metri separati. C'è un misuratore di calcolo, che è lo stesso del metro Linux, e un misuratore IP di Windows. Gli addebiti visualizzati quando si effettua l'acquisto sono solo per i costi di calcolo. Gli addebiti non includono i costi del software Windows. Per altre informazioni sui costi software, vedere [Costi software non inclusi nelle istanze di vm riservate di Azure.For](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)more information about software costs, see Software costs not included with Azure Reserved VM Instances .

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinare le dimensioni corrette delle macchine virtuali prima dell'acquisto

Prima di acquistare una prenotazione, è necessario determinare le dimensioni della macchina virtuale necessaria. Le sezioni seguenti consentono di determinare le dimensioni corrette della macchina virtuale.

### <a name="use-reservation-recommendations"></a>Utilizzare i consigli di prenotazione

È possibile utilizzare le raccomandazioni di prenotazione per determinare le prenotazioni da acquistare.

- I consigli di acquisto e la quantità consigliata vengono visualizzati quando si acquista un'istanza riservata della macchina virtuale nel portale di Azure.Purchase recommendations and recommended quantity are show when you purchase a VM reserved instance in the Azure portal.
- Azure Advisor fornisce consigli di acquisto per le singole sottoscrizioni.  
- È possibile usare le API per ottenere consigli di acquisto sia per l'ambito condiviso che per l'ambito della sottoscrizione singola. Per ulteriori informazioni, vedere API di raccomandazione per [l'acquisto di istanze riservate per i clienti aziendali.](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)
- Per i clienti con contratto Enterprise Agreement (EA) e Microsoft Customer Agreement (MCA), i consigli di acquisto per gli ambiti di sottoscrizione condivisa e singola sono disponibili con il pacchetto di contenuto Power BI di [Azure Consumption Insights.](/power-bi/service-connect-to-azure-consumption-insights)

### <a name="services-that-get-vm-reservation-discounts"></a>Servizi che ottengono sconti per la prenotazione di macchine virtuali

Le prenotazioni di macchine virtuali possono essere applicate all'utilizzo di macchine virtuali generato da più servizi, non solo per le proprie distribuzioni di macchine virtuali. Le risorse che ottengono sconti per la prenotazione cambiano a seconda dell'impostazione della flessibilità delle dimensioni dell'istanza.

#### <a name="instance-size-flexibility-setting"></a>Impostazione della flessibilità delle dimensioni istanza

L'impostazione della flessibilità delle dimensioni dell'istanza determina quali servizi ottengono gli sconti per le istanze riservate.

Indipendentemente dal fatto che l'impostazione sia attivata o disattivata, gli sconti per la prenotazione vengono applicati automaticamente a qualsiasi utilizzo di macchine virtuali corrispondente quando *ConsumedService* è `Microsoft.Compute`. Controllare quindi i dati di utilizzo per il valore *ConsumedService*. Di seguito sono riportati alcuni esempi:

- Macchine virtuali
- set di scalabilità di macchine virtuali
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

### <a name="analyze-your-usage-information"></a>Analizzare le informazioni sull'utilizzo

Analizza le informazioni sull'utilizzo per determinare le prenotazioni da acquistare. I dati di utilizzo sono disponibili nel file di utilizzo e nelle API. Utilizzarli insieme per determinare quale prenotazione acquistare. Verificare la presenza di istanze di macchine virtuali con utilizzo elevato su base giornaliera per determinare la quantità di prenotazioni da acquistare. Evitare `Meter` la `Product` sottocategoria e i campi nei dati di utilizzo. Non distinguono tra dimensioni di macchine virtuali che usano l'archiviazione Premium.They don't distinguish between VM sizes that use premium storage. Se si usano questi campi per determinare la dimensione della macchina virtuale per l'acquisto della prenotazione, è possibile acquistare la dimensione errata. Quindi non otterrai lo sconto sulla prenotazione che ti aspetti. Fare invece `AdditionalInfo` riferimento al campo nel file di utilizzo o nell'API di utilizzo per determinare le dimensioni corrette della macchina virtuale.

Il file di utilizzo mostra gli addebiti in base al periodo di fatturazione e all'utilizzo giornaliero. Per informazioni sul download del file di utilizzo, vedere [Visualizzare e scaricare l'utilizzo e gli addebiti](../articles/cost-management-billing/understand/download-azure-daily-usage.md)di Azure. Quindi, utilizzando le informazioni del file di utilizzo, è possibile [determinare quale prenotazione acquistare.](../articles/cost-management-billing/reservations/determine-reservation-purchase.md)

### <a name="purchase-restriction-considerations"></a>Considerazioni sulle restrizioni all'acquisto

Le istanze di macchine virtuali riservate sono disponibili per la maggior parte delle dimensioni delle macchine virtuali con alcune eccezioni. Gli sconti di prenotazione non si applicano alle macchine virtuali seguenti:Reservation discounts don't apply for the following VMs:

- **Serie VM** - Serie A, Serie Av2 o serie G.

- **Anteprima o macchine virtuali Promo:** qualsiasi serie di MACCHINE virtuali o dimensioni in anteprima o che usa un misuratore promozionale.

- **Cloud:** le prenotazioni non sono disponibili per l'acquisto in Germania o in Cina.

- **Quota insufficiente:** una prenotazione con ambito a una singola sottoscrizione deve disporre della quota vCPU disponibile nella sottoscrizione per il nuovo RI. Ad esempio, se la sottoscrizione di destinazione ha un limite di quota pari a 10 vCPU per la serie D, non sarà possibile acquistare una prenotazione per 11 istanze Standard_D1. Il controllo della quota per le prenotazioni include le macchine virtuali già distribuite nella sottoscrizione. Ad esempio, se la sottoscrizione include una quota di 10 vCPU per la serie D e distribuisce due istanze Standard_D1, sarà possibile acquistare una prenotazione per le 10 istanze Standard_D1 nella sottoscrizione. È possibile creare una richiesta di [aumento dell'offerta](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) per risolvere il problema.

- Restrizioni di **capacità:** in rari casi, Azure limita l'acquisto di nuove prenotazioni per subset di dimensioni di VM, a causa della bassa capacità in un'area.

## <a name="buy-a-reserved-vm-instance"></a>Acquistare un'istanza di macchina virtuale riservata

È possibile acquistare un'istanza di macchina virtuale riservata nel portale di Azure.You can buy a reserved VM instance in the [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Usare [pagamenti anticipati o mensili](../articles/cost-management-billing/reservations/monthly-payments-reservations.md) per acquistare la prenotazione.
Questi requisiti si applicano all'acquisto di un'istanza di macchina virtuale riservata:These requirements apply to buying a reserved VM instance:

- È necessario essere in un ruolo proprietario per almeno una sottoscrizione EA o una sottoscrizione con una tariffa con pagamento in base al quale è possibile.
- Per le sottoscrizioni EA, l'opzione **Aggiungi istanze riservate** deve essere abilitata nel [portale EA.](https://ea.azure.com/) Se tale impostazione è disabilitata, è necessario essere un amministratore del contratto EA della sottoscrizione.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare le prenotazioni.

Per acquistare un'istanza:

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare Tutte le**prenotazioni** **dei servizi** > .
1. Selezionare **Aggiungi** per acquistare una nuova prenotazione, quindi fare clic su **Macchina virtuale**.
1. Compilare i campi obbligatori. Lo sconto relativo alla prenotazione viene applicato alle istanze di macchine virtuali in esecuzione che corrispondono agli attributi. Il numero di istanze di macchine virtuali a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

Se si dispone di un accordo EA, è possibile utilizzare **l'opzione Aggiungi altro** per aggiungere rapidamente altre istanze. L'opzione non è disponibile per altri tipi di sottoscrizione.


| Campo      | Descrizione|
|------------|--------------|
|Subscription|La sottoscrizione usata per pagare la prenotazione. Al metodo di pagamento della sottoscrizione vengono addebitati i costi per la prenotazione. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri di offerta: MS-A-R-0017P o MS-A-R-0148P) o contratto microsoft con contratto cliente o una singola sottoscrizione con tariffe con pagamento in base al costo (numeri di offerta: MS-A-R-0003P o MS-A-R-0023P). Gli addebiti vengono detratti dal saldo dell'impegno monetario, se disponibile, o addebitati come eccessivamente. Per un abbonamento con tariffe con pagamento in base al pagamento, gli addebiti vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura.|    
|Scope       |L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>**Gruppo di risorse singolo**: lo sconto per la prenotazione si applica solo alle risorse corrispondenti incluse nel gruppo di risorse selezionato.</li><li>**Sottoscrizione singola**: lo sconto della prenotazione viene applicato alle risorse corrispondenti incluse nella sottoscrizione selezionata.</li><li>**Condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione. Per i clienti EA, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.</li></ul>|
|Region    |Area di Azure coperta dalla prenotazione.|    
|Dimensioni macchina virtuale     |Le dimensioni delle istanze della macchina virtuale.|
|Ottimizza per     |La flessibilità delle dimensioni dell'istanza della macchina virtuale è selezionata per impostazione predefinita. Fare clic su **Impostazioni avanzate** per modificare il valore di flessibilità delle dimensioni dell'istanza per applicare lo sconto prenotazione ad altre macchine virtuali nello stesso gruppo di dimensioni della [macchina virtuale.](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md) La priorità di capacità assegna la capacità del data center dando priorità alle distribuzioni. Offre maggiore sicurezza nella possibilità di avviare le istanze della macchina virtuale quando necessario. La priorità di capacità è disponibile solo quando l'ambito della prenotazione è sottoscrizione singola. |
|Termine        |Un anno o tre anni.|
|Quantità    |Il numero di istanze acquistate all'interno della prenotazione. La quantità è il numero di istanze di macchina virtuale in esecuzione che possono ottenere lo sconto sulla fatturazione. Ad esempio, se si eseguono 10 macchine virtuali Standard_D2 negli Stati Uniti orientali, è necessario specificare la quantità come 10 per ottimizzare il vantaggio per tutte le macchine virtuali in esecuzione. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Dati di utilizzo e utilizzo delle prenotazioni

I dati di utilizzo hanno un prezzo effettivo pari a zero per l'utilizzo che ottiene uno sconto sulla prenotazione. È possibile vedere quale istanza della macchina virtuale ha ricevuto lo sconto di prenotazione per ogni prenotazione.

Per altre informazioni su come vengono visualizzati gli sconti di prenotazione nei dati di utilizzo, vedere [Informazioni sull'utilizzo](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) della prenotazione di Azure per la registrazione Enterprise se si è un cliente EA. Se si ha una singola sottoscrizione, vedere [Informazioni sull'utilizzo](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo.

## <a name="change-a-reservation-after-purchase"></a>Modificare una prenotazione dopo l'acquisto

Dopo l'acquisto, a una prenotazione è possibile apportare i tipi di modifiche seguenti:

- Aggiornare l'ambito della prenotazione
- Flessibilità delle dimensioni dell'istanza (se applicabile)Instance size flexibility (if applicable)
- Proprietario

È inoltre possibile dividere una prenotazione in blocchi più piccoli e unire le prenotazioni già suddivise. Nessuna delle modifiche causa una nuova transazione commerciale o modifica la data di fine della prenotazione.

Non è possibile apportare i seguenti tipi di modifiche direttamente dopo l'acquisto:

- Regione di una prenotazione esistente
- SKU
- Quantità
- Duration

Tuttavia, è possibile *scambiare* una prenotazione se si desidera apportare modifiche.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se hai domande o hai bisogno di aiuto, [crea una richiesta](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)di supporto .

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
    - [Informazioni sulle prenotazioni di Azure](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Gestire le prenotazioni in AzureManage Reservations in Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Informazioni su come viene applicato lo sconto sulla prenotazione](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Costi del software Windows non inclusi nelle prenotazioni](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)
