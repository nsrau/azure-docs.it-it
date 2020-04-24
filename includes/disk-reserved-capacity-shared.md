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
Risparmia sull'utilizzo del archiviazione su disco di Azure con capacità riservata. Archiviazione su disco di Azure prenotazioni combinate con le istanze di macchina virtuale riservate di Azure consentono di ridurre i costi totali della macchina virtuale (VM). Lo sconto relativo alla prenotazione viene applicato automaticamente ai dischi corrispondenti nell'ambito della prenotazione selezionato. A causa di questa applicazione automatica, non è necessario assegnare una prenotazione a un disco gestito per ottenere gli sconti.

Gli sconti vengono applicati ogni ora a seconda dell'utilizzo del disco. La capacità riservata inutilizzata non viene riportata. Gli sconti per la prenotazione di archiviazione su disco di Azure non sono applicabili a dischi non gestiti, dischi Ultra o utilizzo di BLOB di pagine.

## <a name="determine-your-storage-needs"></a>Determinare le esigenze di archiviazione

Prima di acquistare una prenotazione, determinare le esigenze di archiviazione. Attualmente, le prenotazioni archiviazione su disco di Azure sono disponibili solo per gli SKU di unità SSD Premium di Azure. Lo SKU di un'unità SSD Premium determina le dimensioni e le prestazioni del disco.

Quando si determinano le esigenze di archiviazione, non considerare i dischi basati solo sulla capacità. Ad esempio, non è possibile avere una prenotazione per un disco P40 e usarlo per pagare due dischi P30 più piccoli. Quando si acquista una prenotazione, si sta acquistando solo una prenotazione per il numero totale di dischi per SKU.

Viene effettuata una prenotazione su disco per ogni SKU di disco. Di conseguenza, l'utilizzo della prenotazione si basa sull'unità degli SKU del disco invece che sulle dimensioni specificate.

Si supponga, ad esempio, di riservare un disco P40 con 2 TiB di capacità di archiviazione con provisioning. Si supponga inoltre di allocare solo due dischi P30. La prenotazione di P40 in questo caso non considera il consumo di P30 e si paga la tariffa con pagamento in base al consumo sui dischi P30.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Considerazioni sugli acquisti

Quando si considera l'acquisto di una prenotazione disco, è consigliabile eseguire le procedure seguenti:

- Analizzare le informazioni sull'utilizzo per determinare le prenotazioni da acquistare. Assicurarsi di tenere traccia dell'uso negli SKU del disco invece che nella capacità del disco con provisioning o in uso.
- Esaminare la prenotazione del disco insieme alla prenotazione della macchina virtuale. Si consiglia vivamente di effettuare prenotazioni sia per l'utilizzo delle macchine virtuali sia per l'utilizzo del disco per il massimo risparmio. È possibile iniziare con la determinazione della prenotazione della macchina virtuale corretta, quindi valutare la prenotazione del disco. In genere, si avrà una configurazione standard per ogni carico di lavoro. Ad esempio, un server SQL Server potrebbe avere due dischi dati P40 e un disco del sistema operativo P30.
  
  Questo tipo di modello può essere utile per determinare l'importo riservato che è possibile acquistare. Questo approccio può semplificare il processo di valutazione e assicurarsi di disporre di un piano allineato per la VM e i dischi. Il piano contiene considerazioni quali le sottoscrizioni o le aree geografiche.

## <a name="purchase-restrictions"></a>Restrizioni di acquisto

Gli sconti per le prenotazioni non sono attualmente disponibili per gli elementi seguenti:

- Dischi non gestiti o BLOB di pagine.
- Unità SSD standard o unità disco rigido standard (HDD).
- SSD Premium SKU inferiori a P30: gli SKU di unità SSD P1, P2, P3, P4, P6, P10, P15 e P20.
- Dischi in Azure per enti pubblici, Azure Germania o aree di Azure Cina.

In rari casi, Azure limita l'acquisto di nuove prenotazioni a un sottoinsieme di SKU del disco a causa di una capacità ridotta in un'area.

## <a name="buy-a-disk-reservation"></a>Acquistare una prenotazione su disco

È possibile acquistare archiviazione su disco di Azure prenotazioni tramite il [portale di Azure](https://portal.azure.com/). È possibile pagare per la prenotazione in anticipo o con pagamenti mensili. Per ulteriori informazioni sull'acquisto con pagamenti mensili, vedere [prenotazioni di acquisto con pagamenti mensili](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

Per acquistare la capacità riservata, seguire questa procedura:

1. Passare al riquadro [prenotazioni acquisti](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) nel portale di Azure.

1. Selezionare **Azure Managed disks** per acquistare una prenotazione.

    ![Riquadro per le prenotazioni di acquisto](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Specificare i valori richiesti descritti nella tabella seguente:

   |Elemento  |Descrizione  |
   |---------|---------|
   |**Ambito**   |  Il numero di sottoscrizioni che possono usare il vantaggio di fatturazione associato alla prenotazione. Questo valore specifica anche il modo in cui la prenotazione viene applicata a sottoscrizioni specifiche. <br/><br/> Se si seleziona **condiviso**, lo sconto relativo alla prenotazione viene applicato alla capacità di archiviazione di Azure in ogni sottoscrizione all'interno del contesto di fatturazione. Il contesto di fatturazione si basa sul modo in cui è stata effettuata l'iscrizione ad Azure. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso include tutte le singole sottoscrizioni con tariffe con pagamento in base al consumo create dall'amministratore account.  <br/><br/>  Se si seleziona una **singola sottoscrizione**, lo sconto per la prenotazione viene applicato alla capacità di archiviazione di Azure nella sottoscrizione selezionata. <br/><br/> Se si seleziona **gruppo di risorse singolo**, lo sconto per la prenotazione viene applicato alla capacità di archiviazione di Azure nella sottoscrizione selezionata e nel gruppo di risorse selezionato di tale sottoscrizione. <br/><br/> È possibile modificare l'ambito di prenotazione dopo aver acquistato la prenotazione.  |
   |**Sottoscrizione**  | Sottoscrizione usata per pagare la prenotazione di archiviazione di Azure. Il metodo di pagamento per la sottoscrizione selezionata viene utilizzato per l'addebito dei costi. La sottoscrizione deve essere di uno dei seguenti tipi:<br/><ul><li> Enterprise Agreement (offer Numbers MS-AZR-0017P e MS-AZR-0148P). per una sottoscrizione Enterprise, il costo viene detratto dal saldo dell'impegno monetario della registrazione oppure viene addebitato come eccedenza.</li><br/><li>Sottoscrizione singola con tariffe con pagamento in base al consumo (numeri di offerta MS-AZR-0003P e MS-AZR-0023P). Per una singola sottoscrizione con tariffe con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione.</li></ul>    |
   | **Dischi** | SKU che si vuole creare. |
   | **Area** | Area in cui è attiva la prenotazione. |
   | **Frequenza di fatturazione** | Frequenza con cui l'account viene fatturato per la prenotazione. Le opzioni includono **mensile** e **iniziale**. |

    ![Riquadro per la selezione del prodotto che si desidera acquistare. png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Dopo aver specificato i valori per la prenotazione, il portale di Azure Visualizza il costo. Il portale Mostra anche la percentuale di sconto rispetto alla fatturazione con pagamento in base al consumo. Selezionare **Avanti** per passare al riquadro **prenotazioni acquisti** .

1. Nel riquadro **prenotazioni acquisti** è possibile assegnare un nome alla prenotazione e selezionare la quantità totale di prenotazioni che si desidera effettuare. Il numero di prenotazioni viene mappato al numero di dischi. Se ad esempio si desidera riservare un centinaio di dischi, immettere il valore di **quantità** **100**.

1. Verificare il costo totale della prenotazione.

    ![Riquadro prenotazioni acquisti](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Dopo l'acquisto di una prenotazione, viene applicata automaticamente a tutte le risorse di archiviazione su disco esistenti che corrispondono alle condizioni di prenotazione. Se non sono ancora state create risorse di archiviazione su disco, la prenotazione viene applicata ogni volta che si crea una risorsa che corrisponde alle condizioni di prenotazione. In entrambi i casi, il periodo di prenotazione inizia subito dopo il completamento dell'acquisto.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare, scambiare o rimborsare le prenotazioni entro determinate limitazioni. Per altre informazioni, vedere [scambi e rimborsi self-service per le prenotazioni di Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="expiration-of-a-reservation"></a>Scadenza di una prenotazione

Al termine di una prenotazione, qualsiasi capacità archiviazione su disco di Azure utilizzata in tale prenotazione viene fatturata in base alla tariffa con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente.

Si riceverà una notifica di posta elettronica 30 giorni prima della scadenza della prenotazione e nuovamente alla data di scadenza. Per continuare a sfruttare i vantaggi offerti dalla prenotazione, rinnovarla non più tardi rispetto alla data di scadenza.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle prenotazioni di Azure](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Informazioni su come viene applicato lo sconto per la prenotazione all'archiviazione su disco di Azure](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
