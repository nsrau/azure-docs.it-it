---
title: Cosa sono le istanze riservate di Azure? - Fatturazione di Azure | Microsoft Docs
description: Informazioni sulle istanze di macchina virtuale riservate di Azure e sui prezzi per risparmiare i costi sostenuti per le macchine virtuali e ottenere il prezzo migliore.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: 93be4bb037af400599b88bb71f34143ee65a5deb
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301703"
---
# <a name="what-are-azure-reserved-vm-instances"></a>Cosa sono le istanze di macchina virtuale riservate di Azure?
Le [istanze di macchina virtuale riservate di Azure](https://azure.microsoft.com/pricing/reserved-vm-instances) consentono di risparmiare effettuando il pagamento anticipato della capacità di calcolo per un intervallo di tempo di uno o tre anni e pertanto di ottenere uno sconto sulle macchine virtuali in uso. Le istanze riservate di Azure consentono di ridurre in modo significativo i costi delle macchine virtuali, ovvero fino al 72% rispetto ai prezzi con pagamento in base al consumo, con un impegno anticipato di uno o tre anni. Le istanze riservate rappresentano uno sconto a livello di fatturazione e non hanno alcuna ripercussione sullo stato di runtime delle macchine virtuali.

È possibile acquistare le istanze riservate nel [portale di Azure](https://aka.ms/reservations). Per altre informazioni, vedere [Pagare in anticipo le macchine virtuali e risparmiare sui costi tramite le istanze riservate](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-instance"></a>Perché acquistare un'istanza riservata?
Se sono presenti macchine virtuali in esecuzione per lunghi periodi di tempo, l'acquisto di un'istanza riservata offre il prezzo effettivo migliore. Ad esempio, se si eseguono in modo continuo quattro istanze di macchine virtuali Standard D2 nell'area Stati Uniti occidentali, senza istanze riservate verranno applicate le tariffe in base al consumo. Se si acquista un'istanza riservata per le quattro macchine virtuali, si otterrà un immediato vantaggio a livello di fatturazione. Infatti non verranno più addebitate tariffe in base al consumo. 

## <a name="what-charges-does-a-reserved-instance-cover"></a>Quali sono le spese coperte da un'istanza riservata?
Un'istanza riservata copre solo i costi dell'infrastruttura di macchine virtuali Windows o Linux e non i costi aggiuntivi relativi a software, rete o archiviazione. Per le macchine virtuali Windows è possibile coprire i costi delle licenze Windows tramite le offerte descritte nella pagina [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>Chi è idoneo per l'acquisto di un'istanza riservata?
Possono acquistare un'istanza riservata i clienti di Azure con i tipi di sottoscrizione seguenti:
-   Tipo di offerta di sottoscrizione Contratto Enterprise (MS-AZR-0017P).
-   Tipo di offerta di sottoscrizione [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Per poter acquistare un'istanza riservata, l'utente deve avere il ruolo "Proprietario" nella sottoscrizione. Per l'acquisto di istanze riservate con un contratto Enterprise, l'amministratore Enterprise deve abilitare l'acquisto di istanze riservate nel portale EA. Per impostazione predefinita, questa impostazione è abilitata.
-   I partner CSP (Cloud Solution Provider) possono usare il portale di Azure o il [Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations) per acquistare istanze riservate.

## <a name="how-is-a-reserved-instance-purchase-billed"></a>Come viene fatturato l'acquisto delle istanze riservate?
L'acquisto delle istanze riservate viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se tale saldo non copre il costo delle istanze riservate, l'eccedenza verrà fatturata.
Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo verrà addebitato immediatamente sulla carta di credito associata all'account. Se invece l'addebito avviene tramite fattura, il costo risulterà visibile sulla fattura successiva.

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>Come viene applicato lo sconto relativo alle istanze riservate acquistate?
Lo sconto relativo alle istanze riservate viene applicato alle macchine virtuali corrispondenti agli attributi selezionati in fase di acquisto. Gli attributi includono l'ambito in cui vengono eseguite le macchine virtuali corrispondenti. Ad esempio, se si desidera avvalersi dello sconto per istanze riservate per quattro macchine virtuali Standard D2 nell'area Stati Uniti occidentali, selezionare la sottoscrizione in cui le macchine virtuali sono in esecuzione. Se le macchine virtuali sono in esecuzione in diverse sottoscrizioni all'interno della sottoscrizione o dell'account corrente, selezionare l'ambito condiviso. L'ambito condiviso consente l'applicazione dello sconto relativo all'acquisto di istanze riservate tra sottoscrizioni. Dopo l'acquisto di un'istanza riservata è possibile modificare l'ambito. Per modificare l'ambito, vedere la documentazione su come gestire l'acquisto di istanze riservate.

Lo sconto sull'acquisto di istanze riservate viene applicato solo alle macchine virtuali con sottoscrizioni di tipo Enterprise o con pagamento in base al consumo. Le macchine virtuali in esecuzione in una sottoscrizione con altri tipi di offerta non ricevono alcun tipo di sconto per le istanze riservate. Per le iscrizioni Enterprise, le sottoscrizioni di tipo Sviluppo/test Enterprise non sono idonee per i vantaggi validi per le istanze riservate.

Per comprendere meglio in che modo l'istanza riservata influisca sulla fatturazione della macchina virtuale, vedere [Informazioni su come viene applicato lo sconto relativo alle istanze di macchine virtuali riservate](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>Cosa succede quando scade il termine dell'istanza riservata?
La scadenza dell'istanza riservata comporta la scadenza anche dello sconto sulla fatturazione. L'infrastruttura di macchine virtuali verrà pertanto fatturata in base a tariffe con pagamento in base al consumo. Le istanze riservate non vengono rinnovate automaticamente. Per continuare a ottenere lo sconto sulla fatturazione è necessario acquistare una nuova istanza riservata. 

## <a name="sizes-and-regional-availability"></a>Dimensioni e disponibilità internazionale
Le istanze riservate sono disponibili per la maggior parte delle dimensioni delle macchine virtuali con alcune eccezioni:
- Macchine virtuali in anteprima: qualsiasi serie di macchine virtuali o dimensione non disponibile in anteprima per l'acquisto di istanze riservate.
- Cloud: le istanze riservate non sono disponibili per l'acquisto nelle aree Azure Governo degli Stati Uniti, Germania o Cina. 
- Quota insufficiente: un'istanza riservata con ambito riferito a una singola sottoscrizione deve avere una quota di CPU virtuali disponibile nell'istanza riservata. Ad esempio, se la sottoscrizione di destinazione ha un limite di quota pari a 10 vCPU per la serie D, non sarà possibile acquistare un'istanza riservata per 11 istanze Standard_D1. Il controllo della quota per le istanze riservate include le macchine virtuali già distribuite nella sottoscrizione. Ad esempio, se la sottoscrizione include una quota di 10 vCPU per la serie D e distribuisce due istanze standard_D1, sarà possibile acquistare un'istanza riservata per le 10 istanze standard_D1 nella sottoscrizione. 
- Restrizioni della capacità: in situazioni rare Azure limita l'acquisto di nuove istanze riservate per subset di dimensioni di macchine virtuali a causa della bassa capacità in un'area specifica.

## <a name="next-steps"></a>Passaggi successivi
È possibile iniziare subito a risparmiare sui costi delle macchine virtuali con l'acquisto di un'[istanza riservata di Azure](https://go.microsoft.com/fwlink/?linkid=861721). 

Per altre informazioni sulle istanze riservate, vedere gli articoli seguenti:

- [Pagare in anticipo le macchine virtuali tramite le istanze riservate](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gestire le istanze riservate](billing-manage-reserved-vm-instance.md)
- [Informazioni su come viene applicato lo sconto relativo alle istanze riservate](billing-understand-vm-reservation-charges.md)
- [Informazioni su Utilizzo istanze riservate per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni su Utilizzo istanze riservate per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle istanze riservate](billing-reserved-instance-windows-software-costs.md)
- [Istanze riservate nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
