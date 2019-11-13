---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08/29/2019
ms.openlocfilehash: 29cf947d1e9d26460dc34a6417e76b68bb75e9dc
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005471"
---
Quando si esegue il commit in un'istanza di VM riservata di Azure, è possibile risparmiare denaro. Lo sconto della prenotazione si applica automaticamente alle macchine virtuali in esecuzione corrispondenti all'ambito di prenotazione e agli attributi. Non è necessario assegnare una prenotazione a una macchina virtuale per ottenere gli sconti. Un acquisto di istanze riservate copre solo la parte di calcolo dell'utilizzo della VM. Per le macchine virtuali Windows, il contatore utilizzo è suddiviso in due contatori distinti. È disponibile un contatore di calcolo, che corrisponde al contatore Linux e a un contatore IP di Windows. Gli addebiti visualizzati quando si effettua l'acquisto sono solo per i costi di calcolo. Gli addebiti non includono i costi del software Windows. Per altre informazioni sui costi del software, vedere [costi software non inclusi nelle istanze di VM riservate di Azure](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinare le dimensioni corrette delle macchine virtuali prima dell'acquisto

Prima di acquistare una prenotazione, è necessario determinare le dimensioni della macchina virtuale necessaria. Le sezioni seguenti consentono di determinare le dimensioni appropriate per la macchina virtuale.

### <a name="use-reservation-recommendations"></a>USA consigli sulla prenotazione

È possibile usare le raccomandazioni di prenotazione per determinare le prenotazioni da acquistare.

- I consigli di acquisto e la quantità consigliata vengono visualizzati quando si acquista un'istanza riservata della macchina virtuale nell'portale di Azure.
- Azure Advisor fornisce consigli di acquisto per le singole sottoscrizioni.  
- È possibile usare le API per ottenere raccomandazioni di acquisto sia per l'ambito condiviso sia per l'ambito di una singola sottoscrizione. Per altre informazioni, vedere [API di raccomandazione per l'acquisto di istanze riservate per i clienti aziendali](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Per i clienti Enterprise Agreement (EA) e Microsoft Customer Agreement (MCA), i consigli di acquisto per gli ambiti di abbonamento condiviso e singolo sono disponibili con il [pacchetto di contenuto Informazioni dettagliate sul consumo di Azure Power bi](/power-bi/service-connect-to-azure-consumption-insights).

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
Analizzare le informazioni sull'utilizzo per determinare le prenotazioni da acquistare.

I dati di utilizzo sono disponibili nel file di utilizzo e nelle API. Usarli insieme per determinare la prenotazione da acquistare. Verificare la presenza di istanze di VM con utilizzo elevato su base giornaliera per determinare la quantità di prenotazioni da acquistare.

Evitare i campi `Meter` Subcategory e `Product` nei dati di utilizzo. Non si distinguono tra le dimensioni delle macchine virtuali che usano archiviazione Premium. Se si usano questi campi per determinare le dimensioni della macchina virtuale per l'acquisto della prenotazione, è possibile che si verifichino dimensioni errate. Non sarà quindi possibile usufruire dello sconto di prenotazione previsto. Al contrario, fare riferimento al campo `AdditionalInfo` nel file di utilizzo o nell'API di utilizzo per determinare le dimensioni corrette della macchina virtuale.

### <a name="purchase-restriction-considerations"></a>Considerazioni sulla restrizione degli acquisti

Le istanze di VM riservate sono disponibili per la maggior parte delle dimensioni delle VM con alcune eccezioni. Gli sconti per le prenotazioni non sono applicabili alle macchine virtuali seguenti:

- Serie **VM** : serie A, serie AV2 o G.

- **Anteprima o promozione di macchine virtuali** : qualsiasi serie di VM o dimensione in anteprima o che usa il contatore promozionale.

- **Cloud** : le prenotazioni non sono disponibili per l'acquisto nelle aree Germania o Cina.

- **Quota insufficiente** : una prenotazione con ambito limitato a una singola sottoscrizione deve avere una quota di vCPU disponibile nella sottoscrizione per il nuovo ri. Ad esempio, se la sottoscrizione di destinazione ha un limite di quota pari a 10 vCPU per la serie D, non sarà possibile acquistare una prenotazione per 11 istanze Standard_D1. Il controllo della quota per le prenotazioni include le macchine virtuali già distribuite nella sottoscrizione. Ad esempio, se la sottoscrizione include una quota di 10 vCPU per la serie D e distribuisce due istanze Standard_D1, sarà possibile acquistare una prenotazione per le 10 istanze Standard_D1 nella sottoscrizione. Per risolvere il problema, è possibile [creare una richiesta di aumento delle quote](../articles/azure-supportability/resource-manager-core-quotas-request.md) .

- **Restrizioni di capacità** : in rari casi, Azure limita l'acquisto di nuove prenotazioni per subset di dimensioni di VM, a causa della capacità ridotta in un'area.

## <a name="buy-a-reserved-vm-instance"></a>Acquistare un'istanza di macchina virtuale riservata

È possibile acquistare un'istanza di macchina virtuale riservata nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Usare [pagamenti anticipati o mensili](../articles/billing/billing-monthly-payments-reservations.md) per acquistare la prenotazione.

Questi requisiti si applicano all'acquisto di un'istanza di macchina virtuale riservata:

- È necessario avere un ruolo proprietario per almeno una sottoscrizione EA o una sottoscrizione con pagamento in base al consumo.
- Per le sottoscrizioni EA, l'opzione **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore EA per la sottoscrizione.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare le prenotazioni.

Per acquistare un'istanza:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** > **Prenotazioni**.
1. Selezionare **Aggiungi** per acquistare una nuova prenotazione e quindi fare clic su **macchina virtuale**.
1. Immettere i campi obbligatori. Lo sconto relativo alla prenotazione viene applicato alle istanze di macchine virtuali in esecuzione che corrispondono agli attributi. Il numero di istanze di macchine virtuali a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

| Campo      | DESCRIZIONE|
|------------|--------------|
|sottoscrizione|La sottoscrizione usata per pagare la prenotazione. Al metodo di pagamento per la sottoscrizione vengono addebitati i costi per la prenotazione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P) o un contratto di servizio Microsoft o una sottoscrizione singola con tariffe a consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Gli addebiti vengono dedotti dal saldo dell'impegno monetario, se disponibile, o addebitati come eccedenze. Per una sottoscrizione con tariffe con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione.|    
|Ambito       |L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>**Gruppo di risorse singolo**: lo sconto della prenotazione viene applicato solo alle risorse corrispondenti incluse nel gruppo di risorse selezionato.</li><li>**Sottoscrizione singola**: lo sconto della prenotazione viene applicato alle risorse corrispondenti incluse nella sottoscrizione selezionata.</li><li>**Condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione. Per i clienti con contratto Enterprise, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.</li></ul>|
|Area    |L'area di Azure coperta dalla prenotazione.|    
|Dimensioni macchina virtuale     |Le dimensioni delle istanze della macchina virtuale.|
|Ottimizza per     |Per impostazione predefinita, è selezionata la flessibilità delle dimensioni delle istanze di VM. Fare clic su **Impostazioni avanzate** per modificare il valore di flessibilità delle dimensioni dell'istanza per applicare lo sconto di prenotazione ad altre macchine virtuali nello stesso [gruppo di dimensioni della macchina virtuale](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). La priorità di capacità assegna la capacità del data center dando priorità alle distribuzioni. Offre una maggiore fiducia nella capacità di avviare le istanze di macchine virtuali quando necessario. La priorità di capacità è disponibile solo quando l'ambito della prenotazione è sottoscrizione singola. |
|Termine        |Un anno o tre anni.|
|Quantità    |Il numero di istanze acquistate all'interno della prenotazione. La quantità è il numero di istanze di macchina virtuale in esecuzione che possono ottenere lo sconto sulla fatturazione. Se ad esempio si esegue 10 Standard_D2 macchine virtuali negli Stati Uniti orientali, è necessario specificare Quantity come 10 per ottimizzare il vantaggio per tutte le macchine virtuali in esecuzione. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Utilizzo prenotazione e dati di utilizzo

I dati di utilizzo hanno un prezzo effettivo pari a zero per l'utilizzo che ottiene uno sconto sulla prenotazione. È possibile visualizzare l'istanza di macchina virtuale che ha ricevuto lo sconto di prenotazione per ogni prenotazione.

Per altre informazioni su come vengono visualizzati gli sconti di prenotazione nei dati di utilizzo, vedere informazioni [sull'utilizzo delle prenotazioni di Azure per la registrazione Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md) se si è clienti con contratto Enterprise. Se si ha una sottoscrizione singola, vedere [informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo](../articles/billing/billing-understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Modificare una prenotazione dopo l'acquisto

Dopo l'acquisto, a una prenotazione è possibile apportare i tipi di modifiche seguenti:

- Aggiornare l'ambito della prenotazione
- Flessibilità delle dimensioni dell'istanza (se applicabile)
- Proprietà

È anche possibile dividere una prenotazione in blocchi più piccoli e unire le prenotazioni già suddivise. Nessuna delle modifiche genera una nuova transazione commerciale o modifica la data di fine della prenotazione.

Non è possibile apportare i seguenti tipi di modifiche dopo l'acquisto, direttamente:

- Area della prenotazione esistente
- Sku
- Quantità
- Durata

Tuttavia, se si desidera apportare modifiche, è possibile *scambiare* una prenotazione.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattare Microsoft.

In caso di domande, creare una [Richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per assistenza.

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
