---
ms.topic: include
ms.openlocfilehash: 509d2bc3f58c57a3e2e15eed2ea2c70ed17856f3
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063698"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure

Pagare in anticipo le macchine virtuali e risparmiare sui costi tramite le istanze di macchina virtuale riservate di Azure. Per altre informazioni, vedere [Istanze di macchina virtuale riservate di Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/).

È possibile acquistare le istanze riservate di Azure nel [portale di Azure](https://portal.azure.com). Per acquistare un'istanza riservata:
-   È necessario disporre del ruolo di Proprietario per almeno una sottoscrizione aziendale o con pagamento in base al consumo.
-   Per le sottoscrizioni Enterprise, gli acquisti di istanze riservate devono essere abilitati nel [portale EA](https://ea.azure.com).
-   Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o di vendita possono acquistare istanze riservate.

[!IMPORTANT]
Per identificare correttamente la dimensione della macchina virtuale per un fornitore di prenotazione, è necessario usare uno dei metodi descritti di seguito.

## <a name="determine-the-right-vm-size-before-purchase"></a>Determinare la dimensione corretta della macchina virtuale prima dell'acquisto
1. Fare riferimento al campo AdditionalInfo nel file di dati o nell'API di utilizzo per determinare la dimensione corretta della macchina virtuale per l'acquisto di una prenotazione. Non usare i valori dei campi Sottocategoria misuratore o Prodotto. Questi campi, infatti, non distinguono tra le versioni S e Non-S di una macchina virtuale.
2. È anche possibile ottenere informazioni accurate sulla dimensione della macchina virtuale tramite PowerShell o Azure Resource Manager, oppure dai dettagli sulla macchina virtuale nel portale di Azure.

## <a name="buy-a-reserved-virtual-machine-instance"></a>Acquistare un'istanza di macchine virtuali riservata
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare **Aggiungi** per acquistare una nuova istanza riservata.
4. Compilare i campi obbligatori. Lo sconto relativo all'istanza riservata viene applicato alle istanze di macchine virtuali in esecuzione che corrispondono agli attributi. Il numero di istanze di macchine virtuali a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

    | Campo      | DESCRIZIONE|
    |:------------|:--------------|
    |NOME        |Il nome di questa istanza riservata.| 
    |Sottoscrizione|La sottoscrizione usata per pagare l'istanza riservata. I costi iniziali per l'istanza riservata vengono addebitati al metodo di pagamento associato alla sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numero offerta: MS-AZR-0017P) o con pagamento in base al consumo (numero offerta: MS-AZR-0003P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.|    
    |Scope       |L'ambito dell'istanza riservata può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>Sottoscrizione singola: lo sconto relativo all'istanza riservata viene applicato alle macchine virtuali in questa sottoscrizione. </li><li>Condiviso: lo sconto relativo all'istanza riservata viene applicato alle macchine virtuali in esecuzione in tutte le sottoscrizioni all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni (eccetto le sottoscrizioni di sviluppo/test) all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</li></ul>|
    |Località    |L'area di Azure coperta dall'istanza riservata.|    
    |Dimensioni macchina virtuale     |Le dimensioni delle istanze della macchina virtuale.|
    |Termine        |Un anno o tre anni.|
    |Quantità    |Il numero di istanze in fase di acquisto all'interno dell'istanza riservata. La quantità è il numero di istanze di macchina virtuale in esecuzione che possono ottenere lo sconto sulla fatturazione. Ad esempio, se si eseguono 10 macchine virtuali Standard_D2 negli Stati Uniti orientali, si specificherà 10 come quantità per ottimizzare lo sconto per tutte le macchine in esecuzione. |
5. È possibile visualizzare il costo dell'istanza riservata quando si seleziona **Calcola costo**.

    ![Screenshot prima dell'invio dell'acquisto dell'istanza riservata](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Selezionare **Acquisto**.
7. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

    ![Screenshot dopo l'invio dell'acquisto dell'istanza riservata](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Passaggi successivi 
Lo sconto dell'istanza riservata si applica automaticamente al numero di macchine virtuali in esecuzione corrispondenti all'ambito e agli attributi dell'istanza riservata. È possibile aggiornare l'ambito dell'istanza riservata nel [portale di Azure](https://portal.azure.com), in PowerShell, nell'interfaccia della riga di comando o tramite l'API. 

Per informazioni su come gestire un'istanza riservata, vedere [Gestire le istanze riservate](../articles/billing/billing-manage-reserved-vm-instance.md).

Per altre informazioni sulle istanze riservate di Azure, vedere gli articoli seguenti:

- [Cosa sono le istanze di macchina virtuale riservate di Azure?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Gestire le istanze riservate](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Informazioni su come viene applicato lo sconto per le istanze riservate](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Informazioni su utilizzo istanze riservate per la sottoscrizione con pagamento in base al consumo](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Informazioni su utilizzo istanze riservate di Azure per l'iscrizione Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle istanze riservate](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Reserved instances in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Istanze riservate nel programma Cloud Solution Provider (CSP) del Centro per i partner)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.