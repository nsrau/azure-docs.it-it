---
title: Procedure consigliate di valutazione in Azure Migrate server Assessment
description: Suggerimenti per la creazione di valutazioni con Azure Migrate server assessment.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 648ec2d9fea3e4e112e65cec44a0518b653ddbea
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119974"
---
# <a name="best-practices-for-creating-assessments"></a>Procedure consigliate per la creazione di valutazioni

[Azure Migrate](./migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti.

In questo articolo vengono riepilogate le procedure consigliate per la creazione di valutazioni mediante lo strumento Azure Migrate server assessment.

## <a name="about-assessments"></a>Informazioni sulle valutazioni

Le valutazioni create con Azure Migrate server assessment sono uno snapshot temporizzato dei dati. Esistono due tipi di valutazione che è possibile creare usando Azure Migrate: server assessment:

**Tipo di valutazione** | **Dettagli**
--- | --- 
**Macchina virtuale di Azure** | Valutazioni per la migrazione dei server locali in macchine virtuali di Azure. <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md)locali, le [VM Hyper-V](how-to-set-up-appliance-hyper-v.md)e i [server fisici](how-to-set-up-appliance-physical.md) per la migrazione ad Azure usando questo tipo di valutazione. [Scopri di più](concepts-assessment-calculation.md)
**Soluzione Azure VMware (AVS)** | Valutazioni per la migrazione dei server locali alla [soluzione VMware di Azure (AVS)](../azure-vmware/introduction.md). <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali per la migrazione ad Azure VMware Solution (AVS) con questo tipo di valutazione. [Scopri di più](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Criteri di ridimensionamento
Server Assessment offre due opzioni per i criteri di ridimensionamento:

**Criteri di ridimensionamento** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che fanno raccomandazioni basate sui dati sulle prestazioni raccolti | **Azure VM Assessment**: la raccomandazione sulle dimensioni delle macchine virtuali è basata sui dati di utilizzo della CPU e della memoria.<br/><br/> L'indicazione del tipo di disco (disco rigido/SSD standard o dischi gestiti Premium) si basa sui IOPS e la velocità effettiva dei dischi locali.<br/><br/> **Valutazione della soluzione VMware di Azure (AVS)**: la raccomandazione dei nodi AVS si basa sui dati di utilizzo della CPU e della memoria.
**Così come sono in locale** | Valutazioni che non utilizzano dati sulle prestazioni per apportare raccomandazioni. | **Valutazione delle VM di Azure**: le indicazioni sulle dimensioni delle VM sono basate sulle dimensioni della macchina virtuale locale<br/><br> Il tipo di disco consigliato è basato su quello selezionato nell'impostazione del tipo di archiviazione per la valutazione.<br/><br/> **Valutazione della soluzione VMware di Azure (AVS)**: la raccomandazione dei nodi AVS si basa sulle dimensioni della macchina virtuale locale.

#### <a name="example"></a>Esempio
Ad esempio, se si dispone di una macchina virtuale locale con quattro core con utilizzo del 20% e memoria di 8 GB con utilizzo del 10%, la valutazione della VM di Azure sarà la seguente:

- **Valutazione basata sulle prestazioni**:
    - Identifica i core e la memoria effettivi basati sull'utilizzo di core (4 x 0,20 = 0,8) e memoria (8 GB x 0,10 = 0,8).
    - Applica il fattore di comfort specificato nelle proprietà della valutazione (si tratta di 1,3 x) per ottenere i valori da utilizzare per il ridimensionamento. 
    - Suggerisce le dimensioni più vicine della macchina virtuale in Azure che possono supportare ~ 1,04 Core (0,8 x 1,3) e ~ 1,04 GB (0,8 x 1,3) di memoria.

- **Valutazione As-is (As on-premises)**:
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
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>Parametri di ridimensionamento dell'ITF per le valutazioni AVS

Il motore di archiviazione usato in AVS è rete VSAN. i criteri di archiviazione di rete VSAN definiscono i requisiti di archiviazione per le macchine virtuali. Questi criteri garantiscono il livello di servizio necessario per le VM, perché determinano il modo in cui l'archiviazione viene allocata alla macchina virtuale. Queste sono le combinazioni di transazioni di ITF-RAID disponibili: 

**Errori da tollerare (ITF)** | **Configurazione RAID** | **Numero minimo di host richiesti** | **Considerazioni sul dimensionamento**
--- | --- | --- | --- 
1 | RAID-1 (mirroring) | 3 | Una macchina virtuale da 100 GB utilizzerà 200 GB.
1 | RAID-5 (codifica della cancellazione) | 4 | Una macchina virtuale da 100 GB utilizzerà 133.33 GB
2 | RAID-1 (mirroring) | 5 | Una macchina virtuale da 100 GB utilizzerà 300GB.
2 | RAID-6 (codifica della cancellazione) | 6 | Una macchina virtuale da 100 GB utilizzerà 150 GB.
3 | RAID-1 (mirroring) | 7 | Una macchina virtuale da 100 GB utilizzerà 400 GB.


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

Se sono presenti modifiche locali alle macchine virtuali che si trovano in un gruppo valutato, la valutazione viene contrassegnata come **obsoleta**. Una valutazione può essere contrassegnata come "obsoleta" a causa di una o più modifiche nelle proprietà seguenti:

- Numero di core del processore
- Memoria allocata
- Tipo di avvio o firmware
- Nome, versione e architettura del sistema operativo
- Numero di dischi
- Numero di scheda di rete
- Modifica dimensioni disco (GB allocato)
- Aggiornamento delle proprietà nic. Esempio: modifiche all'indirizzo Mac, aggiunta dell'indirizzo IP e così via.

Eseguire di nuovo la valutazione (**Ricalcola**) per riflettere le modifiche.

### <a name="low-confidence-rating"></a>Classificazione con attendibilità bassa

Una valutazione potrebbe non avere tutti i punti dati per diversi motivi:

- L'ambiente non è stato analizzato per il perioro di tempo per cui si sta creando la valutazione. Se, ad esempio, si sta creando una *valutazione basata sulle prestazioni* con durata delle prestazioni impostata su una settimana, è necessario attendere almeno una settimana dopo l'avvio dell'individuazione per tutti i punti dati da raccogliere. È sempre possibile fare clic su **Ricalcola** per visualizzare l'ultima classificazione di attendibilità applicabile. La classificazione di attendibilità è applicabile solo quando si crea una valutazione *basata sulle prestazioni* .

- Durante il periodo per cui viene calcolata la valutazione sono state arrestate alcune VM. Se alcune macchine virtuali sono state spente per un certo periodo di tempo, Server Assessment non sarà in grado di raccogliere i dati sulle prestazioni per questo periodo.

- Dopo avere avviato l'individuazione in Server Assessment sono state create alcune macchine virtuali. Questa situazione si verifica, ad esempio, se si crea una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma solo una settimana prima sono state create alcune VM nell'ambiente. In questo caso, i dati sulle prestazioni per le nuove macchine virtuali non saranno disponibili per l'intera durata e la classificazione di attendibilità sarà limitata.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Guida dello strumento di migrazione per le valutazioni AVS

Nel report di conformità di Azure per la valutazione della soluzione VMware di Azure (AVS) è possibile visualizzare gli strumenti consigliati seguenti: 
- **VMware HCX o Enterprise**: per i computer VMware, la soluzione VMware Hybrid Cloud Extension (HCx) è lo strumento di migrazione suggerito per eseguire la migrazione del carico di lavoro locale al cloud privato della soluzione VMware di Azure (AVS). [Altre informazioni](../azure-vmware/hybrid-cloud-extension-installation.md).
- **Sconosciuto**: per i computer importati tramite un file CSV, lo strumento di migrazione predefinito è sconosciuto. Tuttavia, per i computer VMware è consigliabile usare la soluzione VMWare Hybrid Cloud Extension (HCX).


## <a name="next-steps"></a>Passaggi successivi

- [Informazioni](concepts-assessment-calculation.md) su come vengono calcolate le valutazioni.
- [Informazioni](how-to-modify-assessment.md) su come personalizzare una valutazione.
