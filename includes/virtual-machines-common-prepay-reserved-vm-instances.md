---
author: yashesvi
ms.author: yashar
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08-07-2018
ms.openlocfilehash: 6be00937a6645fbdd266ac0b1aa1ce95ecc6c3d9
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301918"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure

Pagare in anticipo le macchine virtuali e risparmiare sui costi tramite le istanze di macchina virtuale riservate di Azure. Per altre informazioni, vedere [Istanze di macchina virtuale riservate di Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/).

È possibile acquistare un'istanza di macchina virtuale riservata nel [portale di Azure](https://portal.azure.com). Per acquistare un'istanza:

- È necessario disporre del ruolo di Proprietario per almeno una sottoscrizione aziendale o con pagamento in base al consumo.
- Per le sottoscrizioni aziendali gli acquisti di prenotazioni devono essere abilitati nel [portale EA](https://ea.azure.com).
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare le prenotazioni.

## <a name="determine-the-right-vm-size-before-purchase"></a>Determinare la dimensione corretta della macchina virtuale prima dell'acquisto

I campi Sottocategoria del contatore e Prodotto nei dati di utilizzo non distinguono tra le dimensioni di macchine virtuali che usano l'archiviazione Premium e quelle che non la usano. L'uso di questi campi per determinare le dimensioni della macchina virtuale per l'acquisto della prenotazione può portare all'acquisto di una prenotazione non corretta e impedire di godere degli sconti per la prenotazione. Usare uno dei metodi seguenti per determinare le dimensioni della macchina virtuale corrette per l'acquisto della prenotazione.

- Fare riferimento al campo AdditionalInfo nel file di dati o nell'API di utilizzo per determinare la dimensione corretta della macchina virtuale per l'acquisto di una prenotazione. Non usare i valori dei campi Sottocategoria misuratore o Prodotto. Questi campi, infatti, non distinguono tra le versioni S e Non-S di una macchina virtuale.
- È anche possibile ottenere informazioni accurate sulla dimensione della macchina virtuale tramite PowerShell o Azure Resource Manager, oppure dai dettagli sulla macchina virtuale nel portale di Azure.

Le istanze di macchina virtuale riservate sono disponibili per la maggior parte delle dimensioni delle macchine virtuali con alcune eccezioni:

- Alle macchine virtuali classiche e ai servizi cloud non viene applicato lo sconto per la prenotazione.
- Alle macchine virtuali con core eliminato non viene applicato lo sconto per la prenotazione.
- Alle macchine virtuali seguenti non viene applicato lo sconto per la prenotazione: serie a, serie Av2 o serie G.
- Macchine virtuali in anteprima: qualsiasi serie di macchine virtuali o dimensione non disponibile in anteprima per l'acquisto della prenotazione.
- Cloud: le prenotazioni non sono disponibili per l'acquisto nelle aree Azure Governo degli Stati Uniti, Germania o Cina.
- Quota insufficiente: una prenotazione con ambito riferito a una singola sottoscrizione deve avere una quota di CPU virtuali disponibile nell'istanza riservata. Ad esempio, se la sottoscrizione di destinazione ha un limite di quota pari a 10 vCPU per la serie D, non sarà possibile acquistare una prenotazione per 11 istanze Standard_D1. Il controllo della quota per le prenotazioni include le macchine virtuali già distribuite nella sottoscrizione. Ad esempio, se la sottoscrizione include una quota di 10 vCPU per la serie D e distribuisce due istanze Standard_D1, sarà possibile acquistare una prenotazione per le 10 istanze Standard_D1 nella sottoscrizione. 
- Restrizioni della capacità: in rari casi Azure limita l'acquisto di nuove prenotazioni per subset di dimensioni di macchine virtuali a causa della bassa capacità in un'area specifica.

## <a name="buy-a-reserved-vm-instance"></a>Acquistare un'istanza di macchina virtuale riservata

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare **Aggiungi** per acquistare una nuova prenotazione.
4. Compilare i campi obbligatori. Lo sconto relativo alla prenotazione viene applicato alle istanze di macchine virtuali in esecuzione che corrispondono agli attributi. Il numero di istanze di macchine virtuali a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

    | Campo      | DESCRIZIONE|
    |:------------|:--------------|
    |NOME        |Il nome della prenotazione.| 
    |Sottoscrizione|La sottoscrizione usata per pagare la prenotazione. L'acquisto delle istanze di macchina virtuale riservate viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numero offerta: MS-AZR-0017P) o con pagamento in base al consumo (numero offerta: MS-AZR-0003P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.|    
    |Scope       |L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>Sottoscrizione singola: lo sconto relativo alla prenotazione viene applicato alle macchine virtuali in questa sottoscrizione. </li><li>Condivisa: lo sconto relativo alla prenotazione viene applicato alle macchine virtuali in tutte le sottoscrizione all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni (eccetto le sottoscrizioni di sviluppo/test) all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</li></ul>|
    |Località    |L'area di Azure coperta dalla prenotazione.|    
    |Dimensioni macchina virtuale     |Le dimensioni delle istanze della macchina virtuale.|
    |Ottimizza per     |La flessibilità dell'istanza di macchina virtuale applicherà lo sconto di prenotazione ad altre macchine virtuali dello stesso [gruppo di macchine virtuali](https://aka.ms/RIVMGroups). La priorità di capacità riserva capacità del data center per le distribuzioni. Aumenta così la certezza di avere la possibilità di avviare le istanze di macchina virtuale quando servono. La priorità di capacità è disponibile solo quando l'ambito della prenotazione è sottoscrizione singola. |
    |Termine        |Un anno o tre anni.|
    |Quantità    |Il numero di istanze acquistate all'interno della prenotazione. La quantità è il numero di istanze di macchina virtuale in esecuzione che possono ottenere lo sconto sulla fatturazione. Ad esempio, se si eseguono 10 macchine virtuali Standard_D2 negli Stati Uniti orientali, si specificherà 10 come quantità per ottimizzare lo sconto per tutte le macchine in esecuzione. |
5. È possibile visualizzare il costo della prenotazione quando si seleziona **Calcola costo**.

    ![Screenshot prima di inviare l'acquisto della prenotazione](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Selezionare **Acquisto**.
7. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

    ![Screenshot dopo l'invio dell'acquisto della prenotazione](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Passaggi successivi

Lo sconto della prenotazione si applica automaticamente alle macchine virtuali in esecuzione corrispondenti all'ambito di prenotazione e agli attributi. È possibile aggiornare l'ambito della prenotazione nel [portale di Azure](https://portal.azure.com), in PowerShell, nell'interfaccia della riga di comando o tramite le API.

Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](../articles/billing/billing-save-compute-costs-reservations.md)
- [Gestire le prenotazioni in Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Informazioni su come viene applicato lo sconto sulla prenotazione](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle prenotazioni](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
