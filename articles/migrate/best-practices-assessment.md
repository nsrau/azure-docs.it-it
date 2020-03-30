---
title: Procedure consigliate di valutazione nella valutazione dei server di Azure Migrate
description: Suggerimenti per la creazione di valutazioni con Azure Migrate Server Assessment.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: de6953b6648613595bc9975b17941b3a453a6d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185972"
---
# <a name="best-practices-for-creating-assessments"></a>Procedure consigliate per la creazione di valutazioni

[Azure Migrate](migrate-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti.

Questo articolo riepiloga le procedure consigliate per la creazione di valutazioni con lo strumento di valutazione del server di Azure Migrate.This article summarizes best practices when creating assessments using the Azure Migrate Server Assessment tool.

## <a name="about-assessments"></a>Informazioni sulle valutazioni

Le valutazioni create con la valutazione del server di Azure migrate sono uno snapshot dei dati temporizzato. Esistono due tipi di valutazioni in Azure Migrate.There are two types of assessments in Azure Migrate.

**Tipo di valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che formulano raccomandazioni in base ai dati sulle prestazioni raccolti | La raccomandazione sulle dimensioni della macchina virtuale si basa sui dati di utilizzo della CPU e della memoria.<br/><br/> Il tipo di disco (disco rigido/SSD standard o dischi gestiti da Premium) si basa sulle operazioni di I/O al secondo e sulla velocità effettiva dei dischi locali.
**Così com'è locale** | Valutazioni che non utilizzano i dati sulle prestazioni per formulare raccomandazioni. | Il consiglio per le dimensioni delle macchine virtuali si basa sulle dimensioni della macchina virtuale locale<br/><br> Il tipo di disco consigliato si basa su ciò che si seleziona nell'impostazione del tipo di archiviazione per la valutazione.

### <a name="example"></a>Esempio
Ad esempio, se si dispone di una macchina virtuale locale con quattro core con un utilizzo del 20% e memoria di 8 GB con utilizzo del 10%, le valutazioni saranno le seguenti:

- **Valutazione basata sulle prestazioni**:
    - Identifica core e memoria effettivi in base all'utilizzo di core (4 x 0,20 x 0,8) e di memoria (8 GB x 0,10 x 0,8).
    - Applica il fattore di comfort specificato nelle proprietà di valutazione (ad esempio 1,3x) per ottenere i valori da utilizzare per il dimensionamento. 
    - Consiglia la dimensione della macchina virtuale più vicina in Azure in grado di supportare 1,04 core (0,8 x 1,3) e 1,04 GB di memoria (0,8 x 1,3).

- **Valutazione così com'è (come locale):**
    -  Consiglia una macchina virtuale con quattro core.Recommends a VM with four cores; 8 GB di memoria.

## <a name="best-practices-for-creating-assessments"></a>Procedure consigliate per la creazione di valutazioni

L'appliance di Azure Migrate profila continuamente l'ambiente locale e invia metadati e dati sulle prestazioni ad Azure.The Azure Migrate appliance continuously profiles your on-premises environment, and sends metadata and performance data to Azure. Seguire queste procedure consigliate per le valutazioni dei server individuati tramite un'appliance:

- **Creare valutazioni così come-is:** è possibile creare valutazioni così come sono immediatamente una volta che le macchine vengono visualizzate nel portale di Azure Migrate.Create as-is assessments : You can create as-is assessments immediately once your machines show up in the Azure Migrate portal.
- **Creare una valutazione basata**sulle prestazioni: dopo aver impostato l'individuazione, è consigliabile attendere almeno un giorno prima di eseguire una valutazione basata sulle prestazioni:
    - La raccolta dei dati sulle prestazioni richiede tempo. L'attesa di almeno un giorno garantisce che vi siano punti dati sulle prestazioni sufficienti prima di eseguire la valutazione.
    - Quando si eseguono valutazioni basate sulle prestazioni, assicurarsi di profilare l'ambiente per la durata della valutazione. Ad esempio, se si crea una valutazione con una durata delle prestazioni impostata su una settimana, è necessario attendere almeno una settimana dopo l'avvio dell'individuazione, per tutti i punti dati da raccogliere. In caso contrario, la valutazione non otterrà una valutazione a cinque stelle.
- **Ricalcola le valutazioni:** poiché le valutazioni sono snapshot temporizzato, non vengono aggiornate automaticamente con i dati più recenti. Per aggiornare una valutazione con i dati più recenti, è necessario ricalcolarla.

Seguire queste procedure consigliate per le valutazioni dei server importati in Azure Migrate tramite . File CSV:

- **Creare valutazioni così come-is:** è possibile creare valutazioni così come sono immediatamente una volta che le macchine vengono visualizzate nel portale di Azure Migrate.Create as-is assessments : You can create as-is assessments immediately once your machines show up in the Azure Migrate portal.
- **Crea valutazione basata sulle prestazioni:** consente di ottenere una migliore stima dei costi, soprattutto se è stato eseguito l'overprovisioning della capacità del server in locale. Tuttavia, l'accuratezza della valutazione basata sulle prestazioni dipende dai dati sulle prestazioni specificati dall'utente per i server. 
- **Ricalcola le valutazioni:** poiché le valutazioni sono snapshot temporizzato, non vengono aggiornate automaticamente con i dati più recenti. Per aggiornare una valutazione con i dati importati più recenti, è necessario ricalcolarla.

## <a name="best-practices-for-confidence-ratings"></a>Procedure consigliate per le valutazioni di fiduciaBest practices for confidence ratings

Quando si eseguono valutazioni basate sulle prestazioni, alla valutazione viene assegnato un punteggio di confidenza compreso tra 1 a e il più basso (il più alto). Per utilizzare le valutazioni di confidenza in modo efficace:
- Azure Migrate Server Assessment richiede i dati di utilizzo per la CPU/memoria della macchina virtuale.
- Per ogni disco collegato alla macchina virtuale locale, sono necessari i dati di IOPS/velocità effettiva di lettura/scrittura.
- Per ogni scheda di rete collegata alla macchina virtuale, sono necessari i dati di rete in/out.

A seconda della percentuale di punti dati disponibili per la durata selezionata, il confidenza per una valutazione viene fornito come riepilogato nella tabella seguente.

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0%-20% | 1 stella
   21%-40% | 2 stelle
   41%-60% | 3 stelle
   61%-80% | 4 stelle
   81%-100% | 5 stelle


## <a name="common-assessment-issues"></a>Problemi comuni di valutazione

Ecco come risolvere alcuni problemi comuni dell'ambiente che influiscono sulle valutazioni.

###  <a name="out-of-sync-assessments"></a>Valutazioni non sincronizzate

Se si aggiungono o rimuovono computer da un gruppo dopo aver creato una valutazione, la valutazione creata verrà contrassegnata come **non sincronizzata.** Eseguire nuovamente la valutazione (**Ricalcola**) per riflettere le modifiche del gruppo.

### <a name="outdated-assessments"></a>Valutazioni obsolete

Se sono presenti modifiche locali alle macchine virtuali in un gruppo che è stato valutato, la valutazione è contrassegnata come **obsoleta.** Per riflettere le modifiche, eseguire nuovamente la valutazione.

### <a name="low-confidence-rating"></a>Basso livello di confidenza

Una valutazione potrebbe non avere tutti i punti dati per una serie di motivi:

- L'ambiente non è stato analizzato per il perioro di tempo per cui si sta creando la valutazione. Ad esempio, se si sta creando una *valutazione basata sulle prestazioni* con la durata delle prestazioni impostata su una settimana, è necessario attendere almeno una settimana dopo aver avviato l'individuazione per tutti i punti dati da raccogliere. È sempre possibile fare clic su **Ricalcola** per visualizzare l'ultima valutazione di confidenza applicabile. La classificazione di confidenza è applicabile solo quando si crea una valutazione *basata sulle prestazioni.*

- Durante il periodo per cui viene calcolata la valutazione sono state arrestate alcune VM. Se alcune macchine virtuali sono state spente per un certo periodo di tempo, Server Assessment non sarà in grado di raccogliere i dati sulle prestazioni per questo periodo.

- Dopo avere avviato l'individuazione in Server Assessment sono state create alcune macchine virtuali. Questa situazione si verifica, ad esempio, se si crea una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma solo una settimana prima sono state create alcune VM nell'ambiente. In questo caso, i dati sulle prestazioni per le nuove macchine virtuali non saranno disponibili per l'intera durata e la classificazione di attendibilità sarà limitata.


## <a name="next-steps"></a>Passaggi successivi

- [Scopri](concepts-assessment-calculation.md) come vengono calcolate le valutazioni.
- [Scopri](how-to-modify-assessment.md) come personalizzare una valutazione.
