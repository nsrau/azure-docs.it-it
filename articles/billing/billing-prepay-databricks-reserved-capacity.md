---
title: Ottimizzare i costi di Azure Databricks con un acquisto anticipato di
description: Informazioni su come è possibile pagare in anticipo per gli addebiti di Azure Databricks con capacità riservata per risparmiare denaro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 99eb4de86aa227d558bec54d011a0b1548d27cf0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811258"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Ottimizzare i costi di Azure Databricks con un acquisto anticipato di

È possibile salvare in di Azure Databricks costi unitari (DBU) quando si pre-acquista Azure Databricks commit unità (DBCU) per uno o tre anni. È possibile usare il DBCUs pre-acquistate in qualsiasi momento durante il periodo di validità di acquisto. A differenza delle macchine virtuali, le unità di pre-acquistate non scadono su base oraria e usarle in qualsiasi momento durante il periodo di validità dell'acquisto.

Qualsiasi utilizzo di Azure Databricks detrae automaticamente dalle dBu pre-acquistate. Non è necessario ridistribuire o assegnare un piano di pre-acquistato per le aree di lavoro Azure Databricks per l'utilizzo DBU ottenere sconti preacquisto.

Lo sconto preacquisto si applica solo all'uso di DBU. Altri costi, ad esempio calcolo, archiviazione e rete vengono addebitati separatamente.

## <a name="determine-the-right-size-to-buy"></a>Determinare le dimensioni corrette per l'acquisto

Preacquisto Databricks si applica a tutti i livelli e i carichi di lavoro di Databricks. È possibile considerare il preacquisto come un pool di unità di commit Databricks prepagato. Utilizzo viene dedotto dal pool, indipendentemente dal carico di lavoro o a livelli. Utilizzo viene dedotto il rapporto tra i seguenti:

| **Carico di lavoro** | **Rapporto tra applicazioni le DBU - livello Standard** | **Rapporto tra applicazioni DBU-livello Premium** |
| --- | --- | --- |
| Analisi dei dati | 0,4 | 0.55 |
| Ingegneria dei dati | 0,15 | 0.30 |
| Data Engineering Light | 0,07 | 0.22 |

Ad esempio, quando una quantità di dati Analitica: livello Standard viene utilizzato, le unità di commit Databricks pre-acquistate vengono dedotti dal 0,4 unità di misura.

Prima di acquistare, calcolare la quantità DBU totale usata per i livelli e carichi di lavoro diversi. Utilizzare i rapporti precedenti la denormalizzazione in DBCU e quindi eseguire una proiezione di uso totale accanto a uno o tre anni.

## <a name="purchase-databricks-commit-units"></a>Acquistare unità di Databricks con commit

È possibile acquistare piani di Databricks nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Per acquistare capacità riservata, è necessario il ruolo di proprietario per enterprise almeno una sottoscrizione.

- Attualmente, preacquistare è disponibile solo per i clienti con contratto Enterprise.
- È necessario essere un ruolo di proprietario per almeno una sottoscrizione Enterprise.
- Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). In alternativa, se tale impostazione è disabilitata, è necessario essere amministratore della sottoscrizione con contratto Enterprise.

**Per l'acquisto:**

1. Accedere al [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Selezionare una sottoscrizione. Usare la **sottoscrizione** elenco per selezionare la sottoscrizione che viene usata per pagare la capacità riservata. Il metodo di pagamento della sottoscrizione viene addebitato il costo iniziale per la capacità riservata. Gli addebiti sono detratto dal saldo dell'impegno monetario di registrazione o addebitati come eccedenze.
1. Selezionare un ambito. Usare la **ambito** elenco per selezionare un ambito della sottoscrizione:
    - **Singolo ambito di gruppo di risorse** , applica lo sconto di prenotazione per le risorse corrispondente nel solo il gruppo di risorse selezionato.
    - **Ambito della sottoscrizione singola** , applica lo sconto di prenotazione per le risorse corrispondente nella sottoscrizione selezionata.
    - **Ambito condiviso** : si applica lo sconto di prenotazione per le risorse nelle sottoscrizioni idonee che sono nel contesto di fatturazione. Per i clienti con contratto Enterprise, il contesto di fatturazione è la registrazione.
1. Selezionare il numero di unità Azure Databricks commit si vuole acquistare e completare l'acquisto.


![Esempio di acquisto di Azure Databricks nel portale di Azure](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Cambia ambito e la proprietà

È possibile apportare le seguenti modifiche a una prenotazione dopo l'acquisto:

- Aggiornare l'ambito della prenotazione
- Accesso basato sui ruoli

Non è possibile suddividere o unire di acquisto anticipato di commit unità Databricks. Per altre informazioni sulla gestione delle prenotazioni, vedere [gestire le prenotazioni dopo l'acquisto](billing-manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Annullamenti e scambi

Annullamento ed exchange non è supportata per i piani di acquisto anticipato di Databricks. Tutti gli acquisti sono finali.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
  - [Informazioni su come viene applicato uno sconto DBCU preacquisto Azure Databricks](billing-reservation-discount-databricks.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
