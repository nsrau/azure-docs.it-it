---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cb959b94807678187363d3132ece273584f13a0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590715"
---
Risparmia sull'utilizzo di Archiviazione disco di Azure con capacità riservata. Le prenotazioni di Archiviazione disco di Azure combinate con le istanze di macchine virtuali riservate di Azure consentono di ridurre i costi totali delle macchine virtuali (VM). Lo sconto prenotazione viene applicato automaticamente ai dischi corrispondenti nell'ambito di prenotazione selezionato. A causa di questa applicazione automatica, non è necessario assegnare una prenotazione a un disco gestito per ottenere gli sconti.

Gli sconti vengono applicati ogni ora a seconda dell'utilizzo del disco. La capacità riservata inutilizzata non viene ritrasportata. Gli sconti sulla prenotazione di Archiviazione disco di Azure non si applicano ai dischi non gestiti, agli ultradischi o all'utilizzo dei BLOB di pagine.

## <a name="determine-your-storage-needs"></a>Determinare le esigenze di archiviazione

Prima di acquistare una prenotazione, determinare le esigenze di archiviazione. Attualmente, le prenotazioni di Archiviazione disco di Azure sono disponibili solo per alcuni SKU SSD premium di Azure.Currently, Azure Disk Storage reservations are available only for select Azure premium SSD SSD SKUsUsUsUs. Lo SKU di un SSD premium determina le dimensioni e le prestazioni del disco.

Quando si determinano le esigenze di archiviazione, non pensare ai dischi in base solo alla capacità. Ad esempio, non è possibile avere una prenotazione per un disco P40 e utilizzarla per pagare due dischi P30 più piccoli. Quando acquisti una prenotazione, acquisti una prenotazione solo per il numero totale di dischi per SKU.

Viene effettuata una prenotazione del disco per SKU del disco. Di conseguenza, il consumo di prenotazione si basa sull'unità degli SKU del disco anziché sulle dimensioni fornite.

Ad esempio, si supponga di prenotare un disco P40 con 2 TiB di capacità di archiviazione di cui è stato eseguito il provisioning. Si supponga inoltre di allocare solo due dischi P30. La prenotazione P40 in questo caso non tiene conto del consumo di P30 e si paga la tariffa con pagamento in base al consumo sui dischi P30.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Considerazioni sull'acquisto

Quando si considera l'acquisto della prenotazione del disco, si consigliano le seguenti procedure:

- Analizza le informazioni sull'utilizzo per determinare le prenotazioni da acquistare. Assicurarsi di tenere traccia dell'utilizzo negli SKU del disco anziché della capacità del disco di cui è stato eseguito il provisioning o utilizzato.
- Esaminare la prenotazione del disco insieme alla prenotazione della macchina virtuale. È consigliabile effettuare prenotazioni sia per l'utilizzo della macchina virtuale che per l'utilizzo del disco per un risparmio massimo. È possibile iniziare a determinare la prenotazione della macchina virtuale corretta e quindi valutare la prenotazione del disco. In genere, si diploma di una configurazione standard per ogni carico di lavoro. Ad esempio, un server SQL Server potrebbe avere due dischi dati P40 e un disco del sistema operativo P30.
  
  Questo tipo di modello consente di determinare l'importo prenotato che è possibile acquistare. Questo approccio può semplificare il processo di valutazione e assicurarsi di disporre di un piano allineato sia per la macchina virtuale che per i dischi. Il piano contiene considerazioni come sottoscrizioni o aree geografiche.

## <a name="purchase-restrictions"></a>Restrizioni di acquisto

Gli sconti di prenotazione non sono attualmente disponibili per:

- Dischi non gestiti o BLOB di pagine.
- SSD standard o unità disco rigido standard (HDD).
- SKU SSD Premium più piccoli di P30: SKU SSD P1, P2, P3, P4, P6, P10, P15 e P20.
- Dischi nelle aree di Azure per enti pubblici, Azure Germania o Cina di Azure.Disks in Azure Government, Azure Germany, or Azure China regions.

In rari casi, Azure limita l'acquisto di nuove prenotazioni a un sottoinsieme di SKU del disco a causa della bassa capacità in un'area.

## <a name="buy-a-disk-reservation"></a>Acquistare una prenotazione del disco

È possibile acquistare prenotazioni di Archiviazione disco di Azure tramite il portale di [Azure.](https://portal.azure.com/) Puoi pagare la prenotazione in anticipo o con pagamenti mensili. Per ulteriori informazioni sull'acquisto con pagamenti [mensili, vedere Prenotazioni acquisti con pagamenti mensili](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

Per acquistare la capacità prenotata, attenersi alla seguente procedura:

1. Passare al riquadro [Prenotazioni di acquisto](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) nel portale di Azure.Go to the Purchase reservations pane in the Azure portal.

1. Selezionare **Dischi gestiti di Azure** per acquistare una prenotazione.

    ![Riquadro per le prenotazioni acquisti](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Specificare i valori richiesti descritti nella tabella seguente:

   |Elemento  |Descrizione  |
   |---------|---------|
   |**Scope**   |  Numero di sottoscrizioni che possono utilizzare il vantaggio di fatturazione associato alla prenotazione. Questo valore specifica anche la modalità di applicazione della prenotazione a sottoscrizioni specifiche. <br/><br/> Se si seleziona **Condiviso**, lo sconto sulla prenotazione viene applicato alla capacità di Archiviazione di Azure in ogni sottoscrizione nel contesto di fatturazione. Il contesto di fatturazione si basa sul modo in cui è stata effettuata l'iscrizione ad Azure. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al cliente, l'ambito condiviso include tutte le singole sottoscrizioni con tariffe con pagamento in base al cliente creata dall'amministratore dell'account.  <br/><br/>  Se si seleziona **Sottoscrizione singola,** lo sconto di prenotazione viene applicato alla capacità di Archiviazione di Azure nella sottoscrizione selezionata. <br/><br/> Se si seleziona Gruppo di risorse **singolo,** lo sconto sulla prenotazione viene applicato alla capacità di Archiviazione di Azure nella sottoscrizione selezionata e nel gruppo di risorse selezionato della sottoscrizione. <br/><br/> È possibile modificare l'ambito della prenotazione dopo aver acquistato la prenotazione.  |
   |**Sottoscrizione**  | Sottoscrizione usata per pagare la prenotazione di Archiviazione di Azure.The subscription you use to pay for the Azure Storage reservation. Il metodo di pagamento sull'abbonamento selezionato viene utilizzato per addebitare i costi. La sottoscrizione deve essere di uno dei seguenti tipi:<br/><ul><li> Contratto Enterprise (numero di offerta MS-A-R-0017P e MS-A-R-0148P). per una sottoscrizione Enterprise, il costo viene detratto dal saldo dell'impegno monetario della registrazione oppure viene addebitato come eccedenza.</li><br/><li>Abbonamento individuale con tariffe con pagamento in base al costo (numero di offerta MS-A-R-0003P e MS-A-A-R-0023P). Per un abbonamento individuale con tariffe con pagamento in base al cliente, gli addebiti vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura nell'abbonamento.</li></ul>    |
   | **Dischi** | SKU che si desidera creare. |
   | **Regione** | Regione in cui è attiva la prenotazione. |
   | **Frequenza di fatturazione** | Frequenza con cui l'account viene fatturato per la prenotazione. Le opzioni includono **Mensile** e **Anticipo**. |

    ![Riquadro per la selezione del prodotto che si desidera acquistare.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Dopo aver specificato i valori per la prenotazione, il portale di Azure visualizza il costo. Il portale mostra anche la percentuale di sconto rispetto alla fatturazione con pagamento in base al tuo verso l'alto. Selezionare **Avanti** per continuare con il riquadro **Prenotazioni acquisti.**

1. Nel riquadro **Prenotazioni acquisti** è possibile assegnare un nome alla prenotazione e selezionare la quantità totale di prenotazioni che si desidera effettuare. Il numero di prenotazioni viene mappato al numero di dischi. Ad esempio, se si desidera impegnare un centinaio di dischi, immettere il valore **Quantità** **100**.

1. Esaminare il costo totale della prenotazione.

    ![Riquadro Prenotazioni acquisti](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Dopo aver acquistato una prenotazione, questa viene applicata automaticamente a tutte le risorse di Archiviazione disco esistenti che corrispondono ai termini di prenotazione. Se non hai ancora creato risorse di archiviazione su disco, la prenotazione viene applicata ogni volta che crei una risorsa che corrisponde ai termini di prenotazione. In entrambi i casi, il termine di prenotazione inizia immediatamente dopo un acquisto riuscito.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare, scambiare o rimborsare le prenotazioni entro determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="expiration-of-a-reservation"></a>Scadenza di una prenotazione

Quando una prenotazione scade, qualsiasi capacità di archiviazione su disco di Azure usata in tale prenotazione viene fatturata alla tariffa con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente.

Riceverai una notifica via email 30 giorni prima della scadenza della prenotazione e di nuovo alla data di scadenza. Per continuare a sfruttare i risparmi sui costi offerti da una prenotazione, rinnovarla non oltre la data di scadenza.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Se hai domande o hai bisogno di aiuto, [crea una richiesta](https://go.microsoft.com/fwlink/?linkid=2083458)di supporto .

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle prenotazioni di Azure](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Informazioni su come viene applicato lo sconto per la prenotazione all'archiviazione su disco di Azure](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
