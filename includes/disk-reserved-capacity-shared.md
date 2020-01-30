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
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847134"
---
Risparmia sull'utilizzo dell'unità SSD Premium con capacità riservata, in combinazione con le istanze di macchina virtuale riservate puoi ridurre i costi totali della VM. Lo sconto relativo alla prenotazione viene applicato automaticamente ai dischi corrispondenti nell'ambito della prenotazione selezionato e non è necessario assegnare una prenotazione a un disco gestito per ottenere gli sconti. Gli sconti vengono applicati ogni ora per l'utilizzo del disco e le capacità riservate inutilizzate non vengono riportate. Lo sconto per la prenotazione di dischi gestiti non si applica a dischi non gestiti, dischi Ultra o utilizzo di BLOB di pagine.

## <a name="determine-your-storage-needs"></a>Determinare le esigenze di archiviazione

Prima di acquistare una prenotazione, è necessario determinare le esigenze di archiviazione. Attualmente, la prenotazione del disco è disponibile solo per gli SKU SSD Select Premium. Lo SKU di un'unità SSD Premium determina le dimensioni e le prestazioni del disco. Quando si determinano le esigenze di archiviazione, non è consigliabile considerare i dischi come una capacità totale, non è possibile usare una prenotazione per un disco più grande (ad esempio P40) e usarla per pagare due dischi più piccoli (P30). Quando si acquista una prenotazione, viene acquistato solo il numero totale di dischi per SKU.

La prenotazione del disco viene effettuata per ogni SKU del disco, di conseguenza l'utilizzo della prenotazione si basa sull'unità degli SKU del disco anziché sulle dimensioni specificate. Se, ad esempio, è stato riservato un P40 di 2 capacità con provisioning di 2 TiB ma sono stati allocati solo 2 dischi P30, il consumo di due P30 non sarà contabilizzato per la prenotazione di P40 e verrà addebitata la tariffa con pagamento in base al consumo.



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Considerazioni sugli acquisti

Quando si considera l'acquisto di una prenotazione disco, è consigliabile seguire le procedure consigliate seguenti:

- Analizzare le informazioni sull'utilizzo per determinare le prenotazioni da acquistare. Assicurarsi di tenere traccia dell'uso negli SKU del disco invece che nella capacità del disco con provisioning o in uso. 
- Esaminare la prenotazione del disco insieme alla prenotazione della macchina virtuale. Si consiglia vivamente di effettuare la prenotazione per l'utilizzo della macchina virtuale e del disco per il salvataggio massimo. È possibile iniziare con la determinazione della prenotazione della macchina virtuale corretta, quindi valutare la prenotazione del disco di conseguenza. In genere, si avrà una configurazione standard per ogni carico di lavoro. ad esempio, un server SQL potrebbe avere due dischi dati P40 e un disco del sistema operativo P30. Questo tipo di modello può essere utile per determinare la quantità di prenotazioni che è possibile acquistare. Questo approccio può semplificare il processo di valutazione e anche assicurarsi di disporre di un piano allineato per la VM e i dischi in termini di sottoscrizioni, aree e altro. 

## <a name="purchase-restrictions"></a>Restrizioni di acquisto

Gli sconti per le prenotazioni non sono attualmente disponibili per i dischi seguenti:
- Dischi non gestiti/BLOB di pagine
- HDD SDD Standard o standard
- SSD Premium SKU inferiori a P30: le prenotazioni non sono disponibili per gli SKU P1/P2/P3/P4/P6/P10/P15/P20 SSD Premium.
- Cloud: le prenotazioni non sono disponibili per l'acquisto in aree di Azure gov, Germania o Cina.
- Restrizioni di capacità: in rari casi, Azure limita l'acquisto di nuove prenotazioni per subset di SKU di dischi, a causa della capacità ridotta in un'area.

## <a name="buy-a-disk-reservation"></a>Acquistare una prenotazione su disco

È possibile acquistare prenotazioni dischi di Azure tramite il [portale di Azure](https://portal.azure.com/). È possibile pagare per la prenotazione prima o con pagamenti mensili. Per ulteriori informazioni sull'acquisto con pagamenti mensili, vedere [prenotazioni di acquisto con pagamenti mensili](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

Per acquistare la capacità riservata, seguire questa procedura:

1. Passare al pannello [prenotazioni acquisti](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) nel portale di Azure.
1. Selezionare **Azure Managed disks** per acquistare una prenotazione.

    ![Disks-reserved-Purchase-reservation. png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. Compilare i campi obbligatori come descritto nella tabella seguente:

   |Campo  |Description  |
   |---------|---------|
   |**Ambito**   |  Indica il numero di sottoscrizioni che possono utilizzare il vantaggio di fatturazione associato alla prenotazione. Controlla anche il modo in cui la prenotazione viene applicata alle sottoscrizioni specifiche. <br/><br/> Se si seleziona **condiviso**, lo sconto relativo alla prenotazione viene applicato alla capacità di archiviazione di Azure in qualsiasi sottoscrizione all'interno del contesto di fatturazione. Il contesto di fatturazione si basa sul modo in cui è stata effettuata l'iscrizione ad Azure. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso include tutte le singole sottoscrizioni con tariffe con pagamento in base al consumo create dall'amministratore account.  <br/><br/>  Se si seleziona una **singola sottoscrizione**, lo sconto per la prenotazione viene applicato alla capacità di archiviazione di Azure nella sottoscrizione selezionata. <br/><br/> Se si seleziona **gruppo di risorse singolo**, lo sconto per la prenotazione viene applicato alla capacità di archiviazione di Azure nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione. <br/><br/> È possibile modificare l'ambito di prenotazione dopo aver acquistato la prenotazione.  |
   |**Sottoscrizione**  | Sottoscrizione usata per pagare la prenotazione di archiviazione di Azure. Il metodo di pagamento per la sottoscrizione selezionata viene utilizzato per l'addebito dei costi. La sottoscrizione deve essere di uno dei seguenti tipi: <br/><br/>  Enterprise Agreement (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P): per una sottoscrizione Enterprise, gli addebiti vengono dedotti dal saldo dell'impegno monetario di registrazione o addebitato come eccedenza. <br/><br/> Sottoscrizione singola con tariffe con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P): per una sottoscrizione singola con tariffe con pagamento in base al consumo, i costi vengono addebitati sul metodo di pagamento con carta di credito o fatturazione per la sottoscrizione.    |
   | **Dischi** | Lo SKU che si sta cercando di creare. |
   | **Area** | Area in cui è attiva la prenotazione. |
   | **Frequenza di fatturazione** | Indica la frequenza con cui l'account viene fatturato per la prenotazione. Le opzioni includono *mensile* o *iniziale*. |

    ![Premium-SSD-reserved-Purchase-Selection. png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Dopo aver selezionato i parametri per la prenotazione, il portale di Azure Visualizza il costo. Il portale Mostra anche la percentuale di sconto rispetto alla fatturazione con pagamento in base al consumo. Selezionare **Avanti** per passare al pannello **prenotazioni di acquisto** .

1. Nel riquadro **prenotazioni acquisti** è possibile assegnare un nome alla prenotazione e selezionare la quantità totale di prenotazioni che si desidera effettuare. Il numero di prenotazioni viene mappato al numero di dischi. Se ad esempio si desidera riservare un centinaio di dischi, è necessario modificare la **quantità** in 100.
1. Verificare il costo totale della prenotazione.

    ![Premium-SSD-reserved-Selecting-SKU-Total-purchase. png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Dopo l'acquisto di una prenotazione, questa viene applicata automaticamente alle risorse di archiviazione su disco di Azure esistenti che corrispondono alle condizioni della prenotazione. Se non sono ancora state create risorse di archiviazione su disco di Azure, la prenotazione viene applicata ogni volta che si crea una risorsa che corrisponde alle condizioni della prenotazione. In entrambi i casi, il termine della prenotazione inizia immediatamente dopo un acquisto riuscito.

## <a name="exchange-or-refund-a-reservation"></a>Scambiare o rimborsare una prenotazione

Con determinate limitazioni, è possibile scambiare o rimborsare una prenotazione.

Per scambiare o rimborsare una prenotazione, accedere ai dettagli della prenotazione nella portale di Azure. Selezionare **scambio o rimborso**e seguire le istruzioni per inviare una richiesta di supporto. Quando la richiesta è stata elaborata, Microsoft invierà un messaggio di posta elettronica per confermare il completamento della richiesta.

Per altre informazioni sui criteri di prenotazione di Azure, vedere la pagina relativa agli [scambi self-service e ai rimborsi per le prenotazioni di Azure](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Scambiare una prenotazione

Lo scambio di una prenotazione consente di ricevere un rimborso ripartito proporzionalmente in base alla parte inutilizzata della prenotazione. È quindi possibile applicare il rimborso al prezzo di acquisto di una nuova prenotazione dischi di Azure.
Non sono previsti limiti per il numero di scambi, né tariffe di scambio. La nuova prenotazione acquistata deve avere un valore uguale o maggiore rispetto al credito riprodotto dalla prenotazione originale. Una prenotazione di dischi di Azure può essere scambiata solo per un'altra prenotazione del disco di Azure e non per una prenotazione per qualsiasi altro servizio di Azure.

### <a name="refund-a-reservation"></a>Rimborsare una prenotazione

È possibile annullare una prenotazione di dischi di Azure in qualsiasi momento. Se si Annulla, si riceverà un rimborso proporzionale in base al periodo rimanente della prenotazione, meno una tariffa per la terminazione anticipata del 12%. Il rimborso massimo all'anno è $50.000.
L'annullamento di una prenotazione interrompe immediatamente la prenotazione e restituisce i mesi rimanenti a Microsoft. Il saldo rivalutato rimanente, meno la tariffa, verrà rimborsato alla forma originale di acquisto.

## <a name="expiration-of-a-reservation"></a>Scadenza di una prenotazione

Al termine di una prenotazione, le capacità del disco di Azure usate con tale prenotazione vengono fatturate in base alla tariffa con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente.
Si riceverà una notifica tramite posta elettronica 30 giorni prima della scadenza della prenotazione e nuovamente alla data di scadenza. Per continuare a sfruttare i vantaggi offerti dalla prenotazione, rinnovarla non più tardi rispetto alla data di scadenza.

## <a name="need-help-contact-us"></a>Opzioni per Contattaci

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle prenotazioni di Azure](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Informazioni sul modo in cui viene applicato lo sconto per la prenotazione archiviazione su disco di Azure](../articles/cost-management-billing/reservations/understand-disk-reservations.md)