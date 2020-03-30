---
title: Ottimizzare i costi per l'archiviazione BLOB con capacità riservata - Archiviazione di AzureOptimize costs for Blob storage with reserved capacity - Azure Storage
description: Informazioni sull'acquisto di capacità riservata di Archiviazione di Azure per risparmiare sui costi del BLOB in blocchi e sulle risorse di Azure Data Lake Storage Gen2.Learn about purchasing Azure Storage reserved capacity to save costs on block blob and Azure Data Lake Storage Gen2 resources.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e73686629de8481f6a37e5bfafc9b723206b4853
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351036"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Ottimizzare i costi per l'archiviazione BLOB con capacità riservata

È possibile risparmiare sui costi di archiviazione per i dati BLOB con la capacità riservata di Archiviazione di Azure.You can save money on storage costs for blob data with Azure Storage reserved capacity. La capacità riservata di Archiviazione di Azure offre uno sconto sulla capacità per i BLOB di blocchi e per i dati di Azure Data Lake Storage Gen2 negli account di archiviazione standard quando si esegue il commit a una prenotazione per un anno o tre anni. Una prenotazione fornisce una quantità fissa di capacità di archiviazione per il periodo di prenotazione.

La capacità riservata di Archiviazione di Azure può ridurre in modo significativo i costi di capacità per i BLOB a blocchi e i dati Gen2 di Archiviazione dati di Azure Data Lake. Il risparmio sui costi ottenuto dipende dalla durata della prenotazione, dalla capacità totale che si sceglie di riservare e dal livello di accesso e dal tipo di ridondanza scelti per l'account di archiviazione. La capacità riservata offre uno sconto sulla fatturazione e non influisce sullo stato delle risorse di Archiviazione di Azure.Reserved capacity provides a billing discount and doesn't affect the state of your Azure Storage resources.

Per informazioni sui prezzi di prenotazione per Archiviazione di Azure, vedere [Prezzi per i BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/) e [Prezzi di Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Termini di prenotazione per Archiviazione di AzureReservation terms for Azure Storage

Le sezioni seguenti descrivono i termini di una prenotazione di Archiviazione di Azure.The following sections describe the terms of an Azure Storage reservation.

### <a name="reservation-capacity"></a>Capacità di prenotazione

È possibile acquistare la capacità riservata di Archiviazione di Azure in unità di 100 TB e 1 PB al mese per un periodo di un anno o di tre anni.

### <a name="reservation-scope"></a>Ambito di prenotazione

La capacità riservata di Archiviazione di Azure è disponibile per una singola sottoscrizione o per più sottoscrizioni (ambito condiviso). Quando l'ambito viene applicato a una singola sottoscrizione, lo sconto prenotazione viene applicato solo alla sottoscrizione selezionata. Quando viene limitato a più sottoscrizioni, lo sconto sulla prenotazione viene condiviso tra tali sottoscrizioni nel contesto di fatturazione del cliente.

Quando si acquista la capacità riservata di Archiviazione di Azure, è possibile usare la prenotazione sia per i dati BLOB in blocchi che per i dati Gen2 di Archiviazione dati di Azure Data Lake. Una prenotazione viene applicata all'utilizzo all'interno dell'ambito acquistato e non può essere limitata a un account di archiviazione, un contenitore o un oggetto specifico all'interno della sottoscrizione. Una prenotazione non può essere suddivisa tra più sottoscrizioni.

Una prenotazione di Archiviazione di Azure copre solo la quantità di dati archiviati in una sottoscrizione o in un gruppo di risorse condivise. Le spese di eliminazione anticipata, operazioni, larghezza di banda e trasferimento dati non sono incluse nella prenotazione. Non appena si acquista una prenotazione, gli addebiti di capacità che corrispondono agli attributi della prenotazione vengono addebitati alle tariffe di sconto anziché alle tariffe con pagamento in base al costo. Per altre informazioni sulle prenotazioni di Azure, vedere Che cosa sono le prenotazioni di [Azure?.](/azure/billing/billing-save-compute-costs-reservations)

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Tipi di account, livelli e opzioni di ridondanza supportati

La capacità riservata di Archiviazione di Azure è disponibile per le risorse negli account di archiviazione standard, inclusi gli account di archiviazione generici v2 (GPv2) e BLOB.

Tutti i livelli di accesso (hot, cool e archive) sono supportati per le prenotazioni. Per altre informazioni sui livelli di accesso, vedere Archiviazione BLOB di Azure: livelli di [accesso hot, cool e archive.](storage-blob-storage-tiers.md)

Tutti i tipi di ridondanza sono supportati per le prenotazioni. Per altre informazioni sulle opzioni di ridondanza, vedere [Ridondanza](../common/storage-redundancy.md)di Archiviazione di Azure.For more information about redundancy options, see Azure Storage redundancy .

> [!NOTE]
> Azure Storage reserved capacity is not available for premium storage accounts, general-purpose v1 (GPv1) storage accounts, Azure Data Lake Storage Gen1, page blobs, Azure Queue storage, Azure Table storage, or Azure Files.  

### <a name="security-requirements-for-purchase"></a>Requisiti di sicurezza per l'acquisto

Per acquistare capacità prenotata:

- È necessario essere nel ruolo **Proprietario** per almeno un Enterprise o una singola sottoscrizione con tariffe con pagamento in base al costo.
- Per le sottoscrizioni Enterprise, **l'opzione Aggiungi istanze riservate** deve essere abilitata nel portale EA. In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare la capacità riservata di Archiviazione BLOB di Azure.For the Cloud Solution Provider (CSP) program, only admin agents or sales agents can buy Azure Blob Storage reserved capacity.

## <a name="determine-required-capacity-before-purchase"></a>Determinare la capacità richiesta prima dell'acquisto

Quando si acquista una prenotazione di Archiviazione di Azure, è necessario scegliere l'area, il livello di accesso e l'opzione di ridondanza per la prenotazione. La prenotazione è valida solo per i dati archiviati in tale area, il livello di accesso e il livello di ridondanza. Si supponga, ad esempio, di acquistare una prenotazione per i dati negli Stati Uniti occidentali per il livello di scelta rapida utilizzando l'archiviazione con ridondanza di zona. Non è possibile utilizzare la stessa prenotazione per i dati negli Stati Uniti orientali, i dati nel livello di archiviazione o i dati in archiviazione con ridondanza geografica (GRS). Tuttavia, è possibile acquistare un'altra prenotazione per le vostre esigenze aggiuntive.  

Le prenotazioni sono disponibili da oggi per 100 TB o 1 PB, con sconti più elevati per 1 blocco PB. Quando si acquista una prenotazione nel portale di Azure, Microsoft potrebbe fornire consigli basati sull'utilizzo precedente per determinare la prenotazione da acquistare.

## <a name="purchase-azure-storage-reserved-capacity"></a>Acquistare la capacità riservata di Archiviazione di AzurePurchase Azure Storage reserved capacity

È possibile acquistare la capacità riservata di Archiviazione di Azure tramite il portale di Azure.You can purchase Azure Storage reserved capacity through the [Azure portal](https://portal.azure.com). Usare pagamenti anticipati o mensili per acquistare la prenotazione. Per altre informazioni sull'acquisto con pagamenti [mensili, vedere Acquistare prenotazioni di Azure con pagamenti anticipati o mensili.](/azure/billing/billing-monthly-payments-reservations)

Per informazioni sull'identificazione dei termini di prenotazione adatti allo scenario, vedere [Informazioni sullo sconto sulla capacità riservata](../../cost-management-billing/reservations/understand-storage-charges.md)di Archiviazione di Azure.

Per acquistare la capacità prenotata, attenersi alla seguente procedura:

1. Passare al riquadro [Prenotazioni di acquisto](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) nel portale di Azure.Navigate to the Purchase reservations pane in the Azure portal.  
1. Selezionare **Archiviazione BLOB** di Azure per acquistare una nuova prenotazione.  
1. Compilare i campi obbligatori come descritto nella tabella seguente:

    ![Screenshot che mostra come acquistare capacità prenotata](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Campo  |Descrizione  |
   |---------|---------|
   |**Scope**   |  Indica quante sottoscrizioni possono utilizzare il vantaggio di fatturazione associato alla prenotazione. Controlla anche il modo in cui la prenotazione viene applicata alle sottoscrizioni specifiche. <br/><br/> Se si seleziona **Condiviso**, lo sconto sulla prenotazione viene applicato alla capacità di Archiviazione di Azure in qualsiasi sottoscrizione nel contesto di fatturazione. Il contesto di fatturazione si basa sul modo in cui è stata effettuata l'iscrizione ad Azure. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al cliente, l'ambito condiviso include tutte le singole sottoscrizioni con tariffe con pagamento in base al cliente creata dall'amministratore dell'account.  <br/><br/>  Se si seleziona **Sottoscrizione singola,** lo sconto di prenotazione viene applicato alla capacità di Archiviazione di Azure nella sottoscrizione selezionata. <br/><br/> Se si seleziona Gruppo di risorse **singolo,** lo sconto sulla prenotazione viene applicato alla capacità di Archiviazione di Azure nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno della sottoscrizione. <br/><br/> È possibile modificare l'ambito della prenotazione dopo aver acquistato la prenotazione.  |
   |**Sottoscrizione**  | Sottoscrizione usata per pagare la prenotazione di Archiviazione di Azure.The subscription that's used to pay for the Azure Storage reservation. Il metodo di pagamento sull'abbonamento selezionato viene utilizzato per addebitare i costi. La sottoscrizione deve essere di uno dei seguenti tipi: <br/><br/>  Contratto Enterprise Agreement (numeri di offerta: MS-A-R-0017P o MS-A-R-0148P): per una sottoscrizione Enterprise, gli addebiti vengono detratti dal saldo dell'impegno monetario dell'iscrizione o addebitati come eccessivamente. <br/><br/> Abbonamento individuale con tariffe con pagamento in base al costo (numeri di offerta: MS-A-R-0003P o MS-A-R-0023P): per un abbonamento individuale con tariffe con pagamento in base al costo, gli addebiti vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura sull'abbonamento.    |
   | **Regione** | Regione in cui è attiva la prenotazione. |
   | **Livello di accesso** | Livello di accesso in cui è attiva la prenotazione. Le opzioni includono *Hot*, *Cool*o *Archive*. Per altre informazioni sui livelli di accesso, vedere Archiviazione BLOB di Azure: livelli di [accesso hot, cool e archiviazione.](storage-blob-storage-tiers.md) |
   | **Ridondanza** | L'opzione di ridondanza per la prenotazione. Le opzioni includono *LRS*, *RS*, *GRS*, e *RA-G-RS*. Per altre informazioni sulle opzioni di ridondanza, vedere [Ridondanza](../common/storage-redundancy.md)di Archiviazione di Azure.For more information about redundancy options, see Azure Storage redundancy . |
   | **Frequenza di fatturazione** | Indica la frequenza con cui l'account viene fatturato per la prenotazione. Le opzioni includono *Mensile* o *Anticipo*. |
   | **Dimensione** | Regione in cui è attiva la prenotazione. |
   |**Termine**  | Un anno o tre anni.   |

1. Dopo aver selezionato i parametri per la prenotazione, il portale di Azure visualizza il costo. Il portale mostra anche la percentuale di sconto rispetto alla fatturazione con pagamento in base al tuo verso l'alto.

1. Nel riquadro **Prenotazioni acquisti** esaminare il costo totale della prenotazione. È inoltre possibile specificare un nome per la prenotazione.

    ![Screenshot che mostra come acquistare una prenotazione](media/storage-blob-reserved-capacity/purchase-reservations.png)

Dopo aver acquistato una prenotazione, questa viene applicata automaticamente a qualsiasi BLOB di blocchi di Archiviazione di Azure esistente o alle risorse Gen2 di Archiviazione dati di Azure che corrisponde ai termini della prenotazione. Se non sono ancora state create risorse di Archiviazione di Azure, la prenotazione verrà applicata ogni volta che si crea una risorsa che corrisponde ai termini della prenotazione. In entrambi i casi, il termine della prenotazione inizia immediatamente dopo un acquisto riuscito.

## <a name="exchange-or-refund-a-reservation"></a>Scambiare o rimborsare una prenotazione

È possibile scambiare o rimborsare una prenotazione, con alcune limitazioni. Queste limitazioni sono descritte nelle sezioni seguenti.

Per scambiare o rimborsare una prenotazione, passare ai dettagli della prenotazione nel portale di Azure.To exchange or refund a reservation, navigate to the reservation details in the Azure portal. Selezionare **Scambio** o **Rimborso**e seguire le istruzioni per inviare una richiesta di supporto. Una volta elaborata la richiesta, Microsoft invierà un messaggio di posta elettronica per confermare il completamento della richiesta.

Per altre informazioni sui criteri di prenotazione di Azure, vedere Scambi e rimborsi in modalità self-service per le prenotazioni di Azure.For more information about Azure Reservations policies, see [Self-service exchanges and refunds for Azure Reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Scambiare una prenotazione

Lo scambio di una prenotazione consente di ricevere un rimborso proporzionale in base alla parte inutilizzata della prenotazione. È quindi possibile applicare il rimborso al prezzo di acquisto di una nuova prenotazione di Archiviazione di Azure.You can then apply the refund to the purchase price of a new Azure Storage reservation.

Non c'è limite al numero di scambi che puoi fare. Inoltre, non c'è nessun costo associato a uno scambio. La nuova prenotazione acquistata deve avere un valore uguale o superiore a quello della prenotazione originale. Una prenotazione di Archiviazione di Azure può essere scambiata solo per un'altra prenotazione di Archiviazione di Azure e non per una prenotazione per altri servizi di Azure.An Azure Storage reservation can be exchanged only for another Azure Storage reservation, and not for a reservation for any other Azure service.

### <a name="refund-a-reservation"></a>Rimborso di una prenotazione

È possibile annullare una prenotazione di Archiviazione di Azure in qualsiasi momento. Quando annulli, riceverai un rimborso proporzionale basato sul periodo rimanente della prenotazione, meno una commissione di risoluzione anticipata del 12%. Il rimborso massimo all'anno è di 50.000 dollari.

L'annullamento di una prenotazione termina immediatamente la prenotazione e restituisce i mesi rimanenti a Microsoft. Il saldo proporzionale rimanente, meno la commissione, verrà rimborsato alla forma originale di acquisto.

## <a name="expiration-of-a-reservation"></a>Scadenza di una prenotazione

Quando una prenotazione scade, qualsiasi capacità di Archiviazione di Azure in uso in tale prenotazione viene fatturata alla tariffa con pagamento in base all'utilizzo. Le prenotazioni non vengono rinnovate automaticamente.

Riceverai una notifica via email 30 giorni prima della scadenza della prenotazione e di nuovo alla data di scadenza. Per continuare a sfruttare i risparmi sui costi offerti da una prenotazione, rinnovarla non oltre la data di scadenza.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Se hai domande o hai bisogno di aiuto, [crea una richiesta](https://go.microsoft.com/fwlink/?linkid=2083458)di supporto .

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle prenotazioni di Azure](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Informazioni su come viene applicato lo sconto per la prenotazione ad Archiviazione di Azure](../../cost-management-billing/reservations/understand-storage-charges.md)
