---
title: Procedure consigliate per la creazione di valutazione con Azure eseguire la migrazione di Server Assessment | Microsoft Docs
description: Vengono forniti suggerimenti per la creazione delle valutazioni con valutazione Server eseguire la migrazione di Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: d417efd4abf14247af171ea77b479f590e14fe76
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812973"
---
# <a name="best-practices-for-creating-assessments"></a>Le procedure consigliate per la creazione delle valutazioni

[Azure Migrate](migrate-overview.md) fornisce un hub di strumenti che consentono di individuare, valutare e la migrazione di App, infrastruttura e i carichi di lavoro in Microsoft Azure. L'hub include strumenti di Azure Migrate e le offerte di fornitori di software indipendenti di terze parti. 

Questo articolo riepiloga le procedure consigliate quando si crea valutazioni con lo strumento di valutazione di Azure Migrate Server. 

## <a name="about-assessments"></a>Sulle valutazioni

Le valutazioni create con la valutazione di Azure eseguire la migrazione di Server sono un punto nel tempo di snapshot dei dati. Esistono due tipi di valutazioni in Azure Migrate.

**Tipo di valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basato sulle prestazioni** | Verifiche che generano indicazioni basate sui dati sulle prestazioni raccolti | Indicazioni sulle dimensioni della macchina virtuale si basa sui dati di utilizzo della CPU e memoria.<br/><br/> Consiglio di tipo disco (dischi gestiti di standard o premium) si basa sulla IOPS e velocità effettiva dei dischi in locale.
**Come-locale** | Valutazioni che non usano i dati sulle prestazioni per le raccomandazioni. | Indicazioni sulle dimensioni della macchina virtuale si basa sulle dimensioni della macchina virtuale in locale<br/><br> Il tipo di disco consigliate si basa su ciò che si seleziona l'impostazione per la valutazione del tipo di archiviazione.

### <a name="example"></a>Esempio
Ad esempio, se si dispone di una macchina virtuale da sito locale con quattro core al 20% di utilizzo e della memoria di 8 GB con 10% di utilizzo, le valutazioni sarà come segue:

- **Basato sulle prestazioni valutazione**:
    - Consiglia di core e memoria basato su core (0,8 core) e l'utilizzo della memoria (0,8 GB).
    - La valutazione si applica un fattore di comfort predefinito pari al 30%.
    - Raccomandazione sulle macchine Virtuali: ~1.4 GB memoria e core ~1.4 (0,8 x1.3).
- **Come-(locale) sulla valutazione**:
    -  Si consiglia di una macchina virtuale con quattro core; 8 GB di memoria.

## <a name="best-practices-for-creating-assessments"></a>Le procedure consigliate per la creazione delle valutazioni

L'appliance Azure Migrate in modo continuo profila l'ambiente locale e invia i dati dei metadati e le prestazioni in Azure. Seguire queste procedure consigliate per la creazione di valutazioni:

- **Creare come-sia le valutazioni**: È possibile creare come-valutazioni si trova subito dopo l'individuazione.
- **Crea valutazione basato sulle prestazioni**: Dopo aver configurato il rilevamento, è consigliabile attendere almeno un giorno prima di eseguire una valutazione basata sulle prestazioni:
    - La raccolta dati sulle prestazioni di tempo. In attesa di almeno un giorno garantisce che esistono sufficienti punti dati delle prestazioni prima di eseguire la valutazione.
    - Per i dati sulle prestazioni, l'appliance raccoglie i punti dati in tempo reale ogni 20 secondi per ogni metrica di prestazioni e ne esegue il rollback fino a un solo cinque minuti punto dati. L'appliance invia cinque minuti del punto dati in Azure ogni ora per il calcolo della valutazione.  
- **Ottenere i dati più recenti**: Valutazioni non vengono aggiornate automaticamente con i dati più recenti. Per aggiornare una valutazione con i dati più recenti, è necessario eseguirlo di nuovo. 
- **Assicurarsi che corrispondano a durate**: Quando si eseguono valutazioni basate su prestazioni, assicurarsi che il profilo dell'ambiente per la durata della valutazione. Ad esempio, se si crea una valutazione con una durata di prestazioni impostata su una settimana, è necessario attendere almeno una settimana dopo l'avvio di individuazione, per tutti i punti dati da raccogliere. In caso contrario, la valutazione non otterrà una classificazione di alto livello. 
- **Evitare di punti dati mancanti**: I problemi seguenti potrebbero comportare punti dati mancanti in una valutazione basata sulle prestazioni:
    - Le macchine virtuali sono spenti durante la valutazione e non vengono raccolti i dati sulle prestazioni. 
    - Se si creano le macchine virtuali durante il mese in cui si basa la cronologia delle prestazioni. i dati per tali macchine virtuali saranno meno di un mese. 
    - La valutazione viene creata immediatamente dopo l'individuazione o l'ora della valutazione non corrisponde l'ora di raccolta dati sulle prestazioni.

## <a name="best-practices-for-confidence-ratings"></a>Le procedure consigliate per le classificazioni di attendibilità

Quando si eseguono valutazioni basate su prestazioni, per la valutazione viene affidato una confidenza classificazione da 1 stelle (minima) a 5 stelle (più alta). Usare le classificazioni di attendibilità in modo efficace:
- Azure eseguire la migrazione di Server Assessment richiede i dati di utilizzo per CPU/memoria della macchina virtuale e il disco dati IOPS/velocità effettiva.
- Per ogni scheda di rete collegata a una macchina virtuale, Azure Migrate deve disporre della rete dati in/out.
- Se i dati di utilizzo non sono disponibili nel Server vCenter, l'indicazione di dimensioni dovuto Azure Migrate potrebbe non essere affidabile. 

In base alla percentuale dei punti dati disponibili, nella tabella seguente sono riepilogate le classificazioni di confidenza per una valutazione.

   **Disponibilità del punto dati** | **Classificazione di attendibilità**
   --- | ---
   0%-20% | 1 stella
   21%-40% | 2 stelle
   41%-60% | 3 stelle
   61%-80% | 4 stelle
   81%-100% | 5 stelle

- Se si riceve una classificazione di attendibilità per una valutazione che è inferiore a cinque stelle, attendere almeno un giorno e quindi di ricalcolare la valutazione.
- Un livello di gravità basso significa che consigli sul dimensionamento potrebbero non essere affidabile. In questo caso, è consigliabile modificare le proprietà di valutazione da utilizzare come-sulla valutazione di un'istanza locale.

## <a name="common-assessment-issues"></a>Problemi comuni di valutazione

Di seguito viene illustrato come risolvere alcuni problemi comuni di ambiente che interessano le valutazioni.

###  <a name="out-of-sync-assessments"></a>Valutazioni di sincronizzata

Se si aggiunge o rimuove le macchine da un gruppo dopo aver creato una valutazione, la valutazione è stato creato verrà contrassegnata **sincronizzata di**. Eseguire nuovamente la valutazione (**ricalcolare**) in modo da riflettere le modifiche di gruppo.

### <a name="outdated-assessments"></a>Valutazioni non aggiornate

Se sono presenti modifiche in locale alle macchine virtuali presenti in un gruppo che è stato valutato, la valutazione viene contrassegnata **obsoleti**. Per riflettere le modifiche, eseguire nuovamente la valutazione.

### <a name="missing-data-points"></a>Punti dati mancanti

Una valutazione potrebbe non essere tutti i punti dati per una serie di motivi:

- Le macchine virtuali potrebbero essere spenta durante la valutazione e non vengono raccolti i dati sulle prestazioni. 
- Le macchine virtuali potrebbero essere create durante il mese in cui le prestazioni della cronologia si basa, quindi i dati sulle prestazioni sono minore di un mese. 
- La valutazione è stata creata immediatamente dopo l'individuazione. Per raccogliere dati sulle prestazioni per un periodo di tempo specificato, è necessario attendere la quantità di tempo prima di eseguire una valutazione specificata. Ad esempio, se si desidera valutare i dati sulle prestazioni per una settimana, è necessario attendere circa una settimana dopo l'individuazione. In caso contrario la valutazione non otterrà una classificazione di alto livello. 


## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su](concepts-assessment-calculation.md) come vengono calcolate le valutazioni.
- [Informazioni su](how-to-modify-assessment.md) come personalizzare una valutazione.
