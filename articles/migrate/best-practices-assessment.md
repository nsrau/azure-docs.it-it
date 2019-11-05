---
title: Procedure consigliate per la creazione della valutazione con Azure Migrate server Assessment
description: Fornisce suggerimenti per la creazione di valutazioni con Azure Migrate server assessment.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: raynew
ms.openlocfilehash: e235116ab77159a0e2e9c66ad09cdb86ce6da1e9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466940"
---
# <a name="best-practices-for-creating-assessments"></a>Procedure consigliate per la creazione di valutazioni

[Azure Migrate](migrate-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti.

In questo articolo vengono riepilogate le procedure consigliate per la creazione di valutazioni mediante lo strumento Azure Migrate server assessment.

## <a name="about-assessments"></a>Informazioni sulle valutazioni

Le valutazioni create con Azure Migrate server assessment sono uno snapshot temporizzato dei dati. Esistono due tipi di valutazione in Azure Migrate.

**Tipo di valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che fanno raccomandazioni basate sui dati sulle prestazioni raccolti | Le indicazioni sulle dimensioni della macchina virtuale sono basate sui dati di utilizzo della CPU e della memoria.<br/><br/> L'indicazione del tipo di disco (disco rigido/SSD standard o dischi gestiti Premium) si basa sui IOPS e la velocità effettiva dei dischi locali.
**Così come sono in locale** | Valutazioni che non utilizzano dati sulle prestazioni per apportare raccomandazioni. | Le indicazioni sulle dimensioni della macchina virtuale sono basate sulle dimensioni della macchina virtuale locale<br/><br> Il tipo di disco consigliato è basato su quello selezionato nell'impostazione del tipo di archiviazione per la valutazione.

### <a name="example"></a>Esempio
Ad esempio, se si dispone di una macchina virtuale locale con quattro core con utilizzo del 20% e la memoria di 8 GB con utilizzo del 10%, le valutazioni saranno le seguenti:

- **Valutazione basata sulle prestazioni**:
    - Identifica i core e la memoria effettivi basati sull'utilizzo di core (4 x 0,20 = 0,8) e memoria (8 GB x 0,10 = 0,8).
    - Applica il fattore di comfort specificato nelle proprietà della valutazione (si tratta di 1,3 x) per ottenere i valori da utilizzare per il ridimensionamento. 
    - Suggerisce le dimensioni più vicine della macchina virtuale in Azure che possono supportare ~ 1,04 Core (0,8 x 1,3) e ~ 1,04 GB (0,8 x 1,3) di memoria.

- **Valutazione As-is (As on-premises)** :
    -  Consiglia una macchina virtuale con quattro core; 8 GB di memoria.

## <a name="best-practices-for-creating-assessments"></a>Procedure consigliate per la creazione di valutazioni

Il Azure Migrate appliance continua a profilare l'ambiente locale e invia i metadati e i dati sulle prestazioni in Azure. Seguire queste procedure consigliate per le valutazioni dei server individuati usando un'appliance:

- **Crea valutazioni così come sono**: è possibile creare valutazioni così come sono immediatamente dopo la visualizzazione dei computer nel portale di Azure migrate.
- **Creare una valutazione basata sulle prestazioni**: dopo aver configurato l'individuazione, è consigliabile attendere almeno un giorno prima di eseguire una valutazione basata sulle prestazioni:
    - La raccolta dei dati sulle prestazioni richiede tempo. L'attesa di almeno un giorno garantisce che siano presenti sufficienti punti dati sulle prestazioni prima di eseguire la valutazione.
    - Quando si eseguono valutazioni basate sulle prestazioni, assicurarsi di profilare l'ambiente per la durata della valutazione. Se, ad esempio, si crea una valutazione con una durata delle prestazioni impostata su una settimana, è necessario attendere almeno una settimana dopo l'avvio dell'individuazione, per tutti i punti dati da raccogliere. In caso contrario, la valutazione non otterrà una classificazione a cinque stelle.
- **Ricalcola valutazioni**: poiché le valutazioni sono snapshot temporizzati, non vengono aggiornate automaticamente con i dati più recenti. Per aggiornare una valutazione con i dati più recenti, è necessario ricalcolarla.

Seguire queste procedure consigliate per le valutazioni dei server importati in Azure Migrate tramite. File CSV:

- **Crea valutazioni così come sono**: è possibile creare valutazioni così come sono immediatamente dopo la visualizzazione dei computer nel portale di Azure migrate.
- **Creazione di una valutazione basata sulle prestazioni**: consente di ottenere una stima dei costi migliore, soprattutto se si ha un provisioning eccessivo della capacità del server in locale. Tuttavia, l'accuratezza della valutazione basata sulle prestazioni dipende dai dati sulle prestazioni specificati dall'utente per i server. 
- **Ricalcola valutazioni**: poiché le valutazioni sono snapshot temporizzati, non vengono aggiornate automaticamente con i dati più recenti. Per aggiornare una valutazione con i dati importati più recenti, è necessario ricalcolarli.

## <a name="best-practices-for-confidence-ratings"></a>Procedure consigliate per le classificazioni di confidenza

Quando si eseguono valutazioni basate sulle prestazioni, alla valutazione viene assegnata una classificazione di attendibilità compresa tra 1 stella (più bassa) e 5 stelle. Per usare in modo efficace la classificazione delle confidenze:
- Azure Migrate server assessment richiede i dati di utilizzo per la CPU/memoria della macchina virtuale.
- Per ogni disco collegato alla VM locale sono necessari i dati di IOPS/velocità effettiva di lettura/scrittura.
- Per ogni scheda di rete collegata alla macchina virtuale, sono necessari i dati di rete in/out.

A seconda della percentuale di punti dati disponibili per la durata selezionata, la classificazione di attendibilità per una valutazione viene fornita come riepilogata nella tabella seguente.

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0%-20% | 1 stella
   21%-40% | 2 stelle
   41%-60% | 3 stelle
   61%-80% | 4 stelle
   81%-100% | 5 stelle


## <a name="common-assessment-issues"></a>Problemi di valutazione comuni

Di seguito viene illustrato come risolvere alcuni problemi di ambiente comuni che influiscono sulle valutazioni.

###  <a name="out-of-sync-assessments"></a>Valutazioni out-of-Sync

Se si aggiungono o rimuovono computer da un gruppo dopo aver creato una valutazione, la valutazione creata verrà contrassegnata come non **sincronizzata**. Eseguire nuovamente la valutazione (**Ricalcola**) per riflettere le modifiche apportate al gruppo.

### <a name="outdated-assessments"></a>Valutazioni obsolete

Se sono presenti modifiche locali alle macchine virtuali che si trovano in un gruppo valutato, la valutazione viene contrassegnata come **obsoleta**. Per riflettere le modifiche, eseguire nuovamente la valutazione.

### <a name="low-confidence-rating"></a>Classificazione con attendibilità bassa

Una valutazione potrebbe non avere tutti i punti dati per diversi motivi:

- L'ambiente non è stato analizzato per il perioro di tempo per cui si sta creando la valutazione. Se, ad esempio, si sta creando una *valutazione basata sulle prestazioni* con durata delle prestazioni impostata su una settimana, è necessario attendere almeno una settimana dopo l'avvio dell'individuazione per tutti i punti dati da raccogliere. È sempre possibile fare clic su **Ricalcola** per visualizzare l'ultima classificazione di attendibilità applicabile. La classificazione di attendibilità è applicabile solo quando si crea una valutazione *basata sulle prestazioni* .

- Durante il periodo per cui viene calcolata la valutazione sono state arrestate alcune VM. Se alcune macchine virtuali sono state spente per un certo periodo di tempo, Server Assessment non sarà in grado di raccogliere i dati sulle prestazioni per questo periodo.

- Dopo avere avviato l'individuazione in Server Assessment sono state create alcune macchine virtuali. Questa situazione si verifica, ad esempio, se si crea una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma solo una settimana prima sono state create alcune VM nell'ambiente. In questo caso, i dati sulle prestazioni per le nuove macchine virtuali non saranno disponibili per l'intera durata e la classificazione di attendibilità sarà limitata.


## <a name="next-steps"></a>Passaggi successivi

- [Informazioni](concepts-assessment-calculation.md) su come vengono calcolate le valutazioni.
- [Informazioni](how-to-modify-assessment.md) su come personalizzare una valutazione.
