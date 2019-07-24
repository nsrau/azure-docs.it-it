---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 07/11/2019
ms.openlocfilehash: 766856438b22661b961bfbadc0b63376031622f6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850776"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Pagamento anticipato per le macchine virtuali con istanze di VM riservate di Azure

Pagare in anticipo le macchine virtuali e risparmiare sui costi tramite le istanze di macchina virtuale riservate di Azure. Per altre informazioni, vedere [Istanze di macchina virtuale riservate di Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/).

È possibile acquistare un'istanza di macchina virtuale riservata nel [portale di Azure](https://portal.azure.com). Per acquistare un'istanza:

- È necessario avere un ruolo proprietario per almeno una sottoscrizione Enterprise o una sottoscrizione con pagamento in base al consumo.
- Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare le prenotazioni.

Lo sconto della prenotazione si applica automaticamente alle macchine virtuali in esecuzione corrispondenti all'ambito di prenotazione e agli attributi. È possibile aggiornare l'ambito della prenotazione nel [portale di Azure](https://portal.azure.com), in PowerShell, nell'interfaccia della riga di comando o tramite le API.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinare le dimensioni corrette delle macchine virtuali prima dell'acquisto

Prima di acquistare una prenotazione, è necessario determinare le dimensioni della macchina virtuale necessaria. Le sezioni seguenti consentono di determinare le dimensioni appropriate per la macchina virtuale.

### <a name="use-reservation-recommendations"></a>USA consigli sulla prenotazione

È possibile usare le raccomandazioni di prenotazione per determinare le prenotazioni da acquistare.

- I consigli di acquisto e la quantità consigliata vengono visualizzati quando si acquista un'istanza riservata della macchina virtuale nell'portale di Azure.
- Azure Advisor fornisce consigli di acquisto per le singole sottoscrizioni.  
- È possibile usare le API per ottenere raccomandazioni di acquisto sia per l'ambito condiviso sia per l'ambito di una singola sottoscrizione. Per altre informazioni, vedere [API di raccomandazione per l'acquisto di istanze riservate per i clienti aziendali](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Per i clienti con contratto Enterprise, sono disponibili raccomandazioni per l'acquisto per ambiti di sottoscrizione singoli e condivisi con il [pacchetto di contenuto Informazioni dettagliate sul consumo di Azure Power bi](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Servizi che ricevono sconti per la prenotazione di macchine virtuali

Le prenotazioni della macchina virtuale possono essere applicate all'utilizzo della macchina virtuale emesso da più servizi, non solo per le distribuzioni di macchine virtuali. Le risorse che ricevono sconti per la prenotazione cambiano a seconda dell'impostazione della flessibilità delle dimensioni dell'istanza.

#### <a name="instance-size-flexibility-setting"></a>Impostazione della flessibilità delle dimensioni dell'istanza

L'impostazione della flessibilità delle dimensioni dell'istanza determina quali servizi ottengono gli sconti per le istanze riservate.

Se l'impostazione è on o off, gli sconti per la prenotazione si applicano automaticamente a qualsiasi  utilizzo di `Microsoft.Compute`VM corrispondente quando il ConsumedService è. Controllare quindi i dati di utilizzo per il valore *ConsumedService* . Di seguito sono riportati alcuni esempi:

- Macchine virtuali
- set di scalabilità di macchine virtuali
- Servizio contenitore
- Distribuzioni di Azure Batch (in modalità sottoscrizioni utente)
- Servizio Azure Kubernetes
- Service Fabric

Quando l'impostazione è impostata su on, gli sconti per le prenotazioni si applicano automaticamente all'utilizzo della macchina virtuale corrispondente quando *ConsumedService* è uno degli elementi seguenti:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Controllare il valore di *ConsumedService* nei dati di utilizzo per determinare se l'utilizzo è idoneo per gli sconti di prenotazione.

Per altre informazioni sulla flessibilità delle dimensioni delle istanze, vedere [flessibilità delle dimensioni delle macchine virtuali con istanze di VM riservate](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analizzare le informazioni sull'utilizzo
Analizzare le informazioni sull'utilizzo per determinare le prenotazioni da acquistare.

I dati di utilizzo sono disponibili nel file di utilizzo e nelle API. Usarli insieme per determinare la prenotazione da acquistare. Verificare la presenza di istanze di VM con utilizzo elevato su base giornaliera per determinare la quantità di prenotazioni da acquistare.

Evitare la `Meter` sottocategoria `Product` e i campi nei dati di utilizzo. Non si distinguono tra le dimensioni delle macchine virtuali che usano archiviazione Premium. Se si usano questi campi per determinare le dimensioni della macchina virtuale per l'acquisto della prenotazione, è possibile che si verifichino dimensioni errate. Non sarà quindi possibile usufruire dello sconto di prenotazione previsto. Al contrario, fare riferimento `AdditionalInfo` al campo nel file di utilizzo o nell'API di utilizzo per determinare le dimensioni corrette della macchina virtuale.

### <a name="purchase-restriction-considerations"></a>Considerazioni sulla restrizione degli acquisti

Le istanze di VM riservate sono disponibili per la maggior parte delle dimensioni delle VM con alcune eccezioni. Gli sconti per le prenotazioni non sono applicabili alle macchine virtuali seguenti:

- Serie **VM** : serie A, serie AV2 o G.

- **VM in anteprima** : tutte le serie di VM o le dimensioni disponibili in anteprima.

- **Cloud** : le prenotazioni non sono disponibili per l'acquisto nelle aree Germania o Cina.

- **Quota insufficiente** : una prenotazione con ambito limitato a una singola sottoscrizione deve avere una quota di vCPU disponibile nella sottoscrizione per il nuovo ri. Ad esempio, se la sottoscrizione di destinazione ha un limite di quota pari a 10 vCPU per la serie D, non sarà possibile acquistare una prenotazione per 11 istanze Standard_D1. Il controllo della quota per le prenotazioni include le macchine virtuali già distribuite nella sottoscrizione. Ad esempio, se la sottoscrizione include una quota di 10 vCPU per la serie D e distribuisce due istanze Standard_D1, sarà possibile acquistare una prenotazione per le 10 istanze Standard_D1 nella sottoscrizione. Per risolvere il problema, è possibile [creare una richiesta di aumento delle quote](../articles/azure-supportability/resource-manager-core-quotas-request.md) .

- **Restrizioni di capacità** : in rari casi, Azure limita l'acquisto di nuove prenotazioni per subset di dimensioni di VM, a causa della capacità ridotta in un'area.

## <a name="buy-a-reserved-vm-instance"></a>Acquistare un'istanza di macchina virtuale riservata

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** > **Prenotazioni**.
1. Selezionare **Aggiungi** per acquistare una nuova prenotazione e quindi fare clic su **macchina virtuale**.
1. Immettere i campi obbligatori. Lo sconto relativo alla prenotazione viene applicato alle istanze di macchine virtuali in esecuzione che corrispondono agli attributi. Il numero di istanze di macchine virtuali a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

| Campo      | DESCRIZIONE|
|------------|--------------|
|Sottoscrizione|La sottoscrizione usata per pagare la prenotazione. L'acquisto delle istanze di macchina virtuale riservate viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri offerta: MS-AZR-0017P o MS-AZR-0148P) o una singola sottoscrizione con tariffe con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Per una sottoscrizione con tariffe con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione.|    
|Ambito       |L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>**Ambito del gruppo di risorse singolo** : applica lo sconto di prenotazione alle risorse corrispondenti solo nel gruppo di risorse selezionato.</li><li>**Singolo ambito di sottoscrizione** : applica lo sconto di prenotazione alle risorse corrispondenti nella sottoscrizione selezionata.</li><li>**Ambito condiviso** : applica lo sconto di prenotazione alle risorse corrispondenti nelle sottoscrizioni idonee presenti nel contesto di fatturazione. Per Enterprise Agreement clienti, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni con tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.</li></ul>|
|Region    |L'area di Azure coperta dalla prenotazione.|    
|Dimensioni macchina virtuale     |Le dimensioni delle istanze della macchina virtuale.|
|Ottimizza per     |Per impostazione predefinita, è selezionata la flessibilità delle dimensioni delle istanze di VM. Fare clic su **Impostazioni avanzate** per modificare il valore di flessibilità delle dimensioni dell'istanza per applicare lo sconto di prenotazione ad altre macchine virtuali nello stesso [gruppo di dimensioni della macchina virtuale](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). La priorità di capacità assegna la capacità del data center dando priorità alle distribuzioni. Offre una maggiore fiducia nella capacità di avviare le istanze di macchine virtuali quando necessario. La priorità di capacità è disponibile solo quando l'ambito della prenotazione è sottoscrizione singola. |
|Termine        |Un anno o tre anni.|
|Quantità    |Il numero di istanze acquistate all'interno della prenotazione. La quantità è il numero di istanze di macchina virtuale in esecuzione che possono ottenere lo sconto sulla fatturazione. Ad esempio, se si eseguono 10 macchine virtuali Standard_D2 negli Stati Uniti orientali, è necessario specificare Quantity come 10 per ottimizzare il vantaggio per tutte le macchine virtuali in esecuzione. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>Modificare una prenotazione dopo l'acquisto

È possibile apportare i seguenti tipi di modifiche a una prenotazione dopo l'acquisto:

- Aggiorna ambito prenotazione
- Flessibilità delle dimensioni dell'istanza (se applicabile)
- Proprietà

È anche possibile dividere una prenotazione in blocchi più piccoli e unire le prenotazioni già suddivise. Nessuna delle modifiche genera una nuova transazione commerciale o modifica la data di fine della prenotazione.

Non è possibile apportare i seguenti tipi di modifiche dopo l'acquisto, direttamente:

- Area della prenotazione esistente
- SKU
- Quantità
- Duration

Tuttavia, se si desidera apportare modifiche, è possibile *scambiare* una prenotazione.

## <a name="cancellations-and-exchanges"></a>Annullamenti e scambi

Se è necessario annullare la prenotazione, potrebbe venire applicata una quota del 12% per l'interruzione anticipata. I rimborsi si basano sul prezzo più basso tra il prezzo di acquisto o il prezzo corrente della prenotazione. I rimborsi sono limitati a $50.000 dollari all'anno. Il rimborso ricevuto corrisponde al saldo ripartito rimanente meno la quota del 12% per l'interruzione anticipata. Per richiedere un annullamento, andare alla prenotazione nel portale di Azure e selezionare **Rimborso** per creare una richiesta di supporto.

Se è necessario modificare la prenotazione delle istanze di VM riservate in un'altra area, gruppo di dimensioni della macchina virtuale o termine, è possibile scambiarlo. Lo scambio deve essere per un'altra prenotazione con un valore uguale o maggiore. Come data di inizio del periodo della nuova prenotazione non viene conservata quella della prenotazione scambiata. Il termine uno o tre anni inizia da quando si crea la nuova prenotazione. Per richiedere uno scambio, passare alla prenotazione nel portale di Azure e selezionare **Scambio** per creare una richiesta di supporto.

Per ulteriori informazioni su come scambiare o rimborsare le prenotazioni, vedere la pagina relativa agli [scambi di prenotazione e](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md)ai rimborsi.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
    - [Informazioni sulle prenotazioni di Azure](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Gestire le prenotazioni in Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Informazioni su come viene applicato lo sconto sulla prenotazione](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Informazioni sull'utilizzo della prenotazione per una sottoscrizione con tariffe con pagamento in base al consumo](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Costi del software Windows non inclusi nelle prenotazioni](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)
