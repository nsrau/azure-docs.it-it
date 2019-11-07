---
title: Ottimizzare i costi per l'archiviazione BLOB con capacità riservata-archiviazione di Azure
description: Scopri come acquistare la capacità riservata di archiviazione di Azure per risparmiare sui costi per le risorse di BLOB in blocchi e Azure Data Lake Storage Gen2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cb21291d4beb9fbba27a56089f13bd0363604eab
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686710"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Ottimizzare i costi per l'archiviazione BLOB con capacità riservata

È possibile risparmiare sui costi di archiviazione per i dati BLOB con la capacità riservata di archiviazione di Azure. La capacità riservata di archiviazione di Azure offre uno sconto sulla capacità per i BLOB in blocchi e per Azure Data Lake Storage Gen2 dati negli account di archiviazione standard quando si esegue il commit di una prenotazione per uno o tre anni. Una prenotazione fornisce una quantità fissa di capacità di archiviazione per il periodo di prenotazione.

La capacità riservata di archiviazione di Azure può ridurre significativamente i costi di capacità per i BLOB in blocchi e i dati Azure Data Lake Storage Gen2. I risparmi ottenuti dipendono dalla durata della prenotazione, dalla capacità totale che si sceglie di riservare e dal livello di accesso e dal tipo di ridondanza scelti per l'account di archiviazione. La capacità riservata fornisce uno sconto per la fatturazione e non influisce sullo stato delle risorse di archiviazione di Azure.

Per informazioni sui prezzi di prenotazione per archiviazione di Azure, vedere prezzi dei [BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/) e [prezzi Azure Data Lake storage generazione 2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Condizioni di prenotazione per archiviazione di Azure

Le sezioni seguenti descrivono i termini di una prenotazione di archiviazione di Azure.

### <a name="reservation-capacity"></a>Capacità prenotazione

È possibile acquistare capacità riservata di archiviazione di Azure in unità di 100 TB e 1 PB al mese per un periodo di un anno o di tre anni.

### <a name="reservation-scope"></a>Ambito prenotazione

La capacità riservata di archiviazione di Azure è disponibile per una singola sottoscrizione o per un gruppo di risorse condivise. Quando si acquista la capacità riservata di archiviazione di Azure, è possibile usare la prenotazione per i dati di BLOB in blocchi e Azure Data Lake Storage Gen2. Una prenotazione viene applicata all'uso nell'ambito acquistato e non può essere limitata a un account di archiviazione, un contenitore o un oggetto specifico all'interno della sottoscrizione. Impossibile suddividere una prenotazione tra più sottoscrizioni.

Una prenotazione di archiviazione di Azure copre solo la quantità di dati archiviati in una sottoscrizione o in un gruppo di risorse condivise. L'eliminazione anticipata, le operazioni, la larghezza di banda e gli addebiti per il trasferimento dati non sono inclusi nella prenotazione. Non appena si acquista una prenotazione, i costi di capacità corrispondenti agli attributi di prenotazione vengono addebitati in base alle tariffe di sconto anziché alle tariffe a consumo. Per altre informazioni sulle prenotazioni di Azure, vedere [che cosa sono le prenotazioni di Azure?](/azure/billing/billing-save-compute-costs-reservations).

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Tipi di account, livelli e opzioni di ridondanza supportati

La capacità riservata di archiviazione di Azure è disponibile per le risorse negli account di archiviazione standard, inclusi gli account di archiviazione BLOB (GPv2) e per utilizzo generico V2.

Tutti i livelli di accesso (ad accesso frequente, ad accesso sporadico e archivio) sono supportati per le prenotazioni. Per altre informazioni sui livelli di accesso, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](storage-blob-storage-tiers.md).

Tutti i tipi di ridondanza sono supportati per le prenotazioni. Per altre informazioni sulle opzioni di ridondanza, vedere [ridondanza di archiviazione di Azure](../common/storage-redundancy.md).

> [!NOTE]
> La capacità riservata di archiviazione di Azure non è disponibile per gli account di archiviazione Premium, per gli account di archiviazione per utilizzo generico V1 (utilizzo generico V1), Azure Data Lake Storage Gen1, BLOB di pagine, archiviazione code di Azure, archiviazione tabelle di Azure o File di Azure.  

### <a name="security-requirements-for-purchase"></a>Requisiti di sicurezza per l'acquisto

Per acquistare la capacità riservata:

- È necessario avere il ruolo di **proprietario** per almeno una sottoscrizione Enterprise o singola con tariffe con pagamento in base al consumo.
- Per le sottoscrizioni aziendali, è necessario abilitare l' **aggiunta di istanze riservate** nel portale EA. In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare capacità riservata di Azure Cosmos DB.

## <a name="determine-required-capacity-before-purchase"></a>Determinare la capacità richiesta prima dell'acquisto

Quando si acquista una prenotazione di archiviazione di Azure, è necessario scegliere l'opzione area, livello di accesso e ridondanza per la prenotazione. La prenotazione è valida solo per i dati archiviati in tale area, livello di accesso e livello di ridondanza. Si supponga, ad esempio, di acquistare una prenotazione per i dati negli Stati Uniti occidentali per il livello di accesso frequente tramite l'archiviazione con ridondanza della zona (ZRS). Non è possibile usare la stessa prenotazione per i dati negli Stati Uniti orientali, i dati nel livello archivio o i dati nell'archiviazione con ridondanza geografica (GRS). Tuttavia, è possibile acquistare un'altra prenotazione per le esigenze aggiuntive.  

Sono attualmente disponibili prenotazioni per 100 TB o 1 blocchi PB, con sconti più elevati per 1 blocchi PB. Quando si acquista una prenotazione nel portale di Azure, Microsoft può fornire consigli sulla base dell'utilizzo precedente per determinare la prenotazione da acquistare.

## <a name="purchase-azure-storage-reserved-capacity"></a>Acquistare la capacità riservata di archiviazione di Azure

È possibile acquistare la capacità riservata di archiviazione di Azure tramite la [portale di Azure](https://portal.azure.com). Pagare per la prenotazione prima o con pagamenti mensili. Per altre informazioni sull'acquisto con pagamenti mensili, vedere [acquistare prenotazioni di Azure con pagamenti anticipati o mensili](/azure/billing/billing-monthly-payments-reservations).

Per informazioni sull'identificazione dei termini di prenotazione appropriati per il proprio scenario, vedere [informazioni sullo sconto sulla capacità riservata di archiviazione di Azure](../../billing/billing-understand-storage-charges.md).

Per acquistare la capacità riservata, seguire questa procedura:

1. Passare al riquadro [prenotazioni acquisti](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) nel portale di Azure.  
1. Selezionare **archiviazione BLOB di Azure** per acquistare una nuova prenotazione.  
1. Compilare i campi obbligatori come descritto nella tabella seguente:

    ![Screenshot che illustra come acquistare la capacità riservata](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Campo  |Descrizione  |
   |---------|---------|
   |**Ambito**   |  Indica il numero di sottoscrizioni che possono utilizzare il vantaggio di fatturazione associato alla prenotazione. Controlla anche il modo in cui la prenotazione viene applicata alle sottoscrizioni specifiche. <br/><br/> Se si seleziona **condiviso**, lo sconto relativo alla prenotazione viene applicato alla capacità di archiviazione di Azure in qualsiasi sottoscrizione all'interno del contesto di fatturazione. Il contesto di fatturazione si basa sul modo in cui è stata effettuata l'iscrizione ad Azure. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso include tutte le singole sottoscrizioni con tariffe con pagamento in base al consumo create dall'amministratore account.  <br/><br/>  Se si seleziona una **singola sottoscrizione**, lo sconto per la prenotazione viene applicato alla capacità di archiviazione di Azure nella sottoscrizione selezionata. <br/><br/> Se si seleziona **gruppo di risorse singolo**, lo sconto per la prenotazione viene applicato alla capacità di archiviazione di Azure nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione. <br/><br/> È possibile modificare l'ambito di prenotazione dopo aver acquistato la prenotazione.  |
   |**Sottoscrizione**  | Sottoscrizione usata per pagare la prenotazione di archiviazione di Azure. Il metodo di pagamento per la sottoscrizione selezionata viene utilizzato per l'addebito dei costi. La sottoscrizione deve essere di uno dei seguenti tipi: <br/><br/>  Enterprise Agreement (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P): per una sottoscrizione Enterprise, gli addebiti vengono dedotti dal saldo dell'impegno monetario di registrazione o addebitato come eccedenza. <br/><br/> Sottoscrizione singola con tariffe con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P): per una sottoscrizione singola con tariffe con pagamento in base al consumo, i costi vengono addebitati sul metodo di pagamento con carta di credito o fatturazione per la sottoscrizione.    |
   | **Area** | Area in cui è attiva la prenotazione. |
   | **Livello di accesso** | Livello di accesso in cui è attiva la prenotazione. Le opzioni *includono accesso*frequente, *ad*accesso sporadico o *Archivio*. Per altre informazioni sui livelli di accesso, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](storage-blob-storage-tiers.md). |
   | **Ridondanza** | Opzione di ridondanza per la prenotazione. Le opzioni includono *con ridondanza locale*, *ZRS*, *GRS*e *ra-GZRS*. Per altre informazioni sulle opzioni di ridondanza, vedere [ridondanza di archiviazione di Azure](../common/storage-redundancy.md). |
   | **Frequenza di fatturazione** | Indica la frequenza con cui l'account viene fatturato per la prenotazione. Le opzioni includono *mensile* o *iniziale*. |
   | **Dimensione** | Area in cui è attiva la prenotazione. |
   |**Termine**  | Un anno o tre anni.   |

1. Dopo aver selezionato i parametri per la prenotazione, il portale di Azure Visualizza il costo. Il portale Mostra anche la percentuale di sconto rispetto alla fatturazione con pagamento in base al consumo.

1. Nel riquadro **prenotazioni acquisti** esaminare il costo totale della prenotazione. È anche possibile specificare un nome per la prenotazione.

    ![Screenshot che illustra come acquistare una prenotazione](media/storage-blob-reserved-capacity/purchase-reservations.png)

Dopo l'acquisto di una prenotazione, questa viene applicata automaticamente a qualsiasi BLOB in blocchi di archiviazione di Azure esistente o a risorse Azure Data Lake Storage Gen2 che corrispondano alle condizioni della prenotazione. Se non sono ancora state create risorse di archiviazione di Azure, la prenotazione viene applicata ogni volta che si crea una risorsa che corrisponde alle condizioni della prenotazione. In entrambi i casi, il termine della prenotazione inizia immediatamente dopo un acquisto riuscito.

## <a name="exchange-or-refund-a-reservation"></a>Scambiare o rimborsare una prenotazione

È possibile scambiare o rimborsare una prenotazione, con determinate limitazioni. Queste limitazioni sono descritte nelle sezioni seguenti.

Per scambiare o rimborsare una prenotazione, accedere ai dettagli della prenotazione nella portale di Azure. Selezionare **scambio** o **rimborso**e seguire le istruzioni per inviare una richiesta di supporto. Quando la richiesta è stata elaborata, Microsoft invierà un messaggio di posta elettronica per confermare il completamento della richiesta.

Per altre informazioni sui criteri di prenotazione di Azure, vedere la pagina relativa agli [scambi self-service e ai rimborsi per le prenotazioni di Azure](../../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

### <a name="exchange-a-reservation"></a>Scambiare una prenotazione

Lo scambio di una prenotazione consente di ricevere un rimborso ripartito proporzionalmente in base alla parte inutilizzata della prenotazione. È quindi possibile applicare il rimborso al prezzo di acquisto di una nuova prenotazione di archiviazione di Azure.

Non sono previsti limiti per il numero di scambi, né tariffe di scambio. La nuova prenotazione acquistata deve avere un valore uguale o maggiore rispetto al credito riprodotto dalla prenotazione originale. Una prenotazione di archiviazione di Azure può essere scambiata solo per un'altra prenotazione di archiviazione di Azure e non per una prenotazione per nessun altro servizio di Azure.

### <a name="refund-a-reservation"></a>Rimborsare una prenotazione

È possibile annullare una prenotazione di archiviazione di Azure in qualsiasi momento. Quando si Annulla, si riceverà un rimborso proporzionale in base al periodo rimanente della prenotazione, meno una tariffa per la terminazione anticipata del 12%. Il rimborso massimo all'anno è $50.000.

L'annullamento di una prenotazione interrompe immediatamente la prenotazione e restituisce i mesi rimanenti a Microsoft. Il saldo rivalutato rimanente, meno la tariffa, verrà rimborsato alla forma originale di acquisto.

## <a name="expiration-of-a-reservation"></a>Scadenza di una prenotazione

Al termine di una prenotazione, le capacità di archiviazione di Azure in uso in tale prenotazione vengono fatturate in base alla tariffa con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente.

Si riceverà una notifica tramite posta elettronica 30 giorni prima della scadenza della prenotazione e nuovamente alla data di scadenza. Per continuare a sfruttare i vantaggi offerti dalla prenotazione, rinnovarla non più tardi rispetto alla data di scadenza.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande, creare una [Richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458) per assistenza.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle prenotazioni di Azure](../../billing/billing-save-compute-costs-reservations.md)
- [Informazioni su come viene applicato lo sconto di prenotazione ad archiviazione di Azure](../../billing/billing-understand-storage-charges.md)
