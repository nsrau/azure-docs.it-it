---
title: Guida alla gestione dei costi per Azure Lab Services
description: Informazioni sui diversi modi per visualizzare i costi per i servizi Lab.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 98e04ba6bb1310935c4893a3616dfd68c2e99a55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797633"
---
# <a name="cost-management-for-azure-lab-services"></a>Gestione dei costi per Azure Lab Services

Per Azure Lab Services, gestione costi può essere suddivisa in due aree distinte: stima dei costi e analisi dei costi. La stima dei costi si verifica quando si configura il Lab per assicurarsi che la struttura iniziale del Lab rientri nel budget previsto. L'analisi dei costi in genere si verifica alla fine del mese per determinare le azioni necessarie per il mese successivo.

## <a name="estimate-the-lab-costs"></a>Stimare i costi del Lab

Ogni dashboard del Lab ha un **costo &** sezione relativa alla fatturazione che prevede una stima approssimativa del costo del Lab per il mese. La stima dei costi riepiloga l'utilizzo dell'ora con il numero massimo di utenti per il costo stimato all'ora. Per ottenere una stima più accurata, configurare il Lab, inclusa la [pianificazione](how-to-create-schedules.md). Il dashboard rifletterà il costo stimato. 

Questa stima potrebbe non mostrare tutti i costi possibili. Alcune risorse non sono incluse:

- Costo di preparazione del modello. Può variare in modo significativo nella quantità di tempo necessaria per la creazione del modello. Il costo per l'esecuzione del modello è identico a quello del Lab totale all'ora. 
- Eventuali costi della [raccolta di immagini condivise](how-to-use-shared-image-gallery.md) , perché una raccolta può essere condivisa tra più laboratori. 
- Ore di avvio di una macchina virtuale (VM) da parte del creatore del Lab.

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra la stima dei costi del dashboard.](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>Analizza l'utilizzo del mese precedente

L'analisi dei costi è destinata alla revisione dell'utilizzo del mese precedente, che consente di determinare eventuali rettifiche per il Lab. È possibile trovare la suddivisione dei costi passati nell' [analisi dei costi della sottoscrizione](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis). Nella portale di Azure è possibile immettere le **sottoscrizioni** nella casella di ricerca e quindi selezionare l'opzione **sottoscrizioni** . 

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra la casella di ricerca e l'opzione sottoscrizioni.](./media/cost-management-guide/subscription-search.png)

Selezionare la sottoscrizione specifica che si desidera esaminare.

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra la selezione della sottoscrizione.](./media/cost-management-guide/subscription-select.png)

Selezionare **analisi dei costi** nel riquadro sinistro in **Gestione costi**.

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra un'analisi dei costi della sottoscrizione in un grafico.](./media/cost-management-guide/subscription-cost-analysis.png)

Questo dashboard consente un'analisi approfondita dei costi, inclusa la possibilità di esportare in diversi tipi di file in base a una pianificazione. Per altre informazioni, vedere [gestione dei costi e panoramica della fatturazione](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).

È possibile filtrare per tipo di risorsa. Con `microsoft.labservices/labaccounts` viene visualizzato solo il costo associato a Lab Services.

## <a name="understand-the-usage"></a>Informazioni sull'utilizzo

Lo screenshot seguente è un esempio di analisi dei costi.

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra un'analisi dei costi di esempio per una sottoscrizione.](./media/cost-management-guide/cost-analysis.png)

Per impostazione predefinita, sono presenti sei colonne: **risorsa**, **tipo di risorsa**, **percorso**, nome del **gruppo di risorse**, **tag**e **costo**. La colonna **risorsa** contiene le informazioni relative all'account Lab, al nome del Lab e alla macchina virtuale. Le righe che mostrano l'account Lab, il nome del Lab e il valore predefinito (seconda e terza riga) rappresentano il costo del Lab. Le macchine virtuali usate hanno un costo che è possibile visualizzare per le righe che mostrano l'account Lab, il nome del Lab, il valore predefinito e il nome della macchina virtuale. 

In questo esempio, l'aggiunta della prima e della seconda riga (inizia con **aaalab/dockerlab**) fornirà il costo totale per il Lab "dockerlab" nell'account Lab "aaalab".

Per ottenere il costo complessivo per la raccolta immagini, modificare il tipo di risorsa in `Microsoft.Compute/Galleries` . Una raccolta di immagini condivise potrebbe non essere visualizzata nei costi, a seconda della posizione in cui è archiviata la raccolta.

> [!NOTE]
> Una raccolta di immagini condivise è connessa all'account Lab. Ciò significa che più laboratori possono usare la stessa immagine.

## <a name="separate-the-costs"></a>Separare i costi

Alcune università hanno usato l'account Lab e il gruppo di risorse come modo per separare le classi. Ogni classe dispone di un proprio account Lab e di un gruppo di risorse. 

Nel riquadro analisi costi aggiungere un filtro in base al nome del gruppo di risorse con il nome del gruppo di risorse appropriato per la classe. Quindi, saranno visibili solo i costi per tale classe. Ciò consente una delineatura più chiara tra le classi quando si visualizzano i costi. È possibile utilizzare la funzionalità di [esportazione pianificata](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) dell'analisi dei costi per scaricare i costi di ogni classe in file distinti.

## <a name="manage-costs"></a>Gestire i costi

A seconda del tipo di classe, esistono modi per gestire i costi per ridurre le istanze di macchine virtuali in esecuzione senza uno studente che le utilizza.

### <a name="automatic-shutdown-settings-for-cost-control"></a>Impostazioni di arresto automatico per il controllo dei costi

Le funzionalità di arresto automatico consentono di evitare la perdita di ore di utilizzo delle macchine virtuali nei Lab. Le impostazioni seguenti intercettano la maggior parte dei casi in cui gli utenti lasciano accidentalmente le macchine virtuali in esecuzione:

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra le tre impostazioni di arresto automatico.](./media/cost-management-guide/auto-shutdown-disconnect.png)

È possibile configurare queste impostazioni a livello di account Lab e di Lab. Se vengono abilitati a livello di account Lab, vengono applicati a tutti i Lab nell'account Lab. Per tutti i nuovi account Lab, queste impostazioni sono attivate per impostazione predefinita. 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>Disconnettere automaticamente gli utenti dalle macchine virtuali ritenute inattive dal sistema operativo

> [!NOTE]
> Questa impostazione è disponibile solo per le macchine virtuali Windows.

Quando l'impostazione **Disconnetti utenti quando macchine virtuali è inattiva** è attivata, l'utente viene disconnesso da qualsiasi computer nel Lab quando il sistema operativo Windows ritiene che la sessione sia inattiva (incluse le macchine virtuali modello). La [definizione del sistema operativo Windows di Idle](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) usa due criteri: 

* Assenza dell'utente: nessun input della tastiera o del mouse.
* Mancanza di consumo di risorse: tutti i processori e tutti i dischi erano inattivi per una determinata percentuale di tempo.

Gli utenti visualizzeranno un messaggio simile al seguente nella macchina virtuale prima di essere disconnessi: 

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra un messaggio di avviso indicante che una sessione è rimasta inattiva per il limite di tempo e verrà disconnessa.](./media/cost-management-guide/idle-timer-expired.png)
 
La macchina virtuale è ancora in esecuzione quando l'utente è disconnesso. Se l'utente si riconnette alla macchina virtuale eseguendo l'accesso, Windows o i file aperti o che non sono stati salvati prima che la disconnessione sia ancora disponibile. In questo stato, poiché la macchina virtuale è in esecuzione, viene comunque conteggiata come attiva e il costo viene accumulato. 
 
Per arrestare automaticamente le macchine virtuali Windows inattive disconnesse, utilizzare la combinazione di **Disconnetti utenti quando le macchine virtuali sono inattive** e **arrestano le macchine virtuali quando gli utenti disconnettono** le impostazioni.

Ad esempio, se si configurano le impostazioni come segue:
 
* **Disconnettere gli utenti quando le macchine virtuali sono inattive**: 15 minuti dopo il rilevamento dello stato di inattività.
* **Arrestare le macchine virtuali quando gli utenti si disconnettono**: 5 minuti dopo la disconnessione dell'utente.
 
Le macchine virtuali Windows verranno arrestate automaticamente 20 minuti dopo l'arresto dell'utilizzo da parte dell'utente. 
 
> [!div class="mx-imgBorder"]
> ![Diagramma che illustra la combinazione di impostazioni che determinano l'arresto automatico della macchina virtuale.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>Arresta automaticamente le macchine virtuali quando gli utenti si disconnettono
 
L'impostazione **Arresta macchine virtuali quando gli utenti si disconnette** supporta le macchine virtuali Windows e Linux. Quando questa impostazione è impostata su on, l'arresto automatico si verifica nei casi seguenti:
 
* Per Windows, una connessione di Desktop remoto (RDP) è disconnessa.
* Per Linux, una connessione SSH è disconnessa.
 
> [!NOTE]
> Sono supportate solo [le distribuzioni e le versioni specifiche di Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) .
 
È possibile specificare per quanto tempo le macchine virtuali devono attendere che l'utente si riconnetta prima di arrestarsi automaticamente. 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>Arresta automaticamente le macchine virtuali avviate, ma gli utenti non si connettono
 
In un Lab, un utente potrebbe avviare una macchina virtuale ma non connettersi mai. Ad esempio:
 
* Una pianificazione nel Lab avvia tutte le macchine virtuali per una sessione di classe, ma alcuni studenti non vengono visualizzati e non si connettono ai computer. 
* Un utente avvia una macchina virtuale ma dimentica di connettersi. 
 
L'impostazione **Arresta macchine virtuali quando gli utenti non si connette** rileva questi casi e arresta automaticamente le macchine virtuali. 
 
Per informazioni su come configurare e abilitare l'arresto automatico delle macchine virtuali alla disconnessione, vedere questi articoli:

* [Configurare l'arresto automatico delle macchine virtuali per un account Lab](how-to-configure-lab-accounts.md)
* [Configurare l'arresto automatico delle macchine virtuali per un Lab](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>Tempo pianificato rispetto alla quota

La comprensione del tempo [pianificato](classroom-labs-concepts.md#schedules) e del [tempo di quota](classroom-labs-concepts.md#quota) consentirà di configurare un Lab per soddisfare al meglio le esigenze del professore e degli studenti. 

L'ora pianificata è un set di tempo in cui tutte le macchine virtuali degli studenti sono state avviate e sono disponibili per la connessione. Il tempo pianificato viene comunemente usato quando tutti gli studenti hanno le proprie macchine virtuali e seguono le direzioni del professore in un momento specifico durante il giorno (ad esempio, le ore di classe). Lo svantaggio è che tutte le macchine virtuali degli studenti vengono avviate e stanno accumulando costi, anche se uno studente non accede a una macchina virtuale. 

Il tempo di quota è il tempo allocato a ogni studente per l'uso a propria discrezione e viene spesso usato per studi indipendenti. Le macchine virtuali non vengono avviate fino a quando lo studente non avvia la macchina virtuale. 

Un Lab può usare la durata della quota o l'orario pianificato oppure una combinazione di entrambi. Se una classe non necessita di un'ora pianificata, usare solo il tempo di quota per l'uso più efficace delle macchine virtuali.

### <a name="scheduled-event-stop-only"></a>Evento pianificato: solo arresto

Nella pianificazione è possibile aggiungere un tipo di evento di sola interruzione che arresterà tutti i computer in un momento specifico. Alcuni proprietari di Lab hanno impostato un evento di solo arresto per ogni giorno a mezzanotte per ridurre i costi e l'utilizzo delle quote quando uno studente dimentica di arrestare la macchina virtuale in uso. Lo svantaggio di questo tipo di evento è che tutte le macchine virtuali verranno arrestate, anche se uno studente usa una macchina virtuale.

### <a name="other-costs-related-to-labs"></a>Altri costi correlati ai Lab 

Alcuni costi non vengono sottoposti a rollup nei servizi Lab, ma possono essere collegati a un servizio lab. È possibile connettere una raccolta di immagini condivise a un Lab, ma non verrà visualizzata in base ai costi dei servizi Lab e i costi. Per ridurre i costi complessivi, è necessario rimuovere le immagini non usate dalla raccolta perché le immagini hanno un costo di archiviazione intrinseco. 

I Lab possono avere connessioni ad altre risorse di Azure tramite una rete virtuale. Quando si rimuove un Lab, è necessario rimuovere la rete virtuale e le altre risorse.

## <a name="conclusion"></a>Conclusione

Le informazioni contenute in questo articolo offrono una migliore comprensione degli strumenti che consentono di ridurre i costi di utilizzo.
