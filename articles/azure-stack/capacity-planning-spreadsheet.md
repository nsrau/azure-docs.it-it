---
title: Pianificazione della capacità del foglio di calcolo per Azure Stack | Microsoft Docs
description: Scopri il foglio di calcolo per le distribuzioni di Azure Stack di pianificazione della capacità.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.openlocfilehash: 6f6e680f7ce613d7a6d9f227765ce599c537e2d3
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106512"
---
# <a name="azure-stack-capacity-planner"></a>Pianificazione della capacità di Azure Stack
Lo strumento Capacity Planner dello Stack di Azure è un foglio di calcolo usato per la pianificazione della capacità di risorse di Azure Stack. Lo strumento capacity planner offre la possibilità di progettare le allocazioni di varie risorse di calcolo e vedere come si può rientrare tra una gamma di offerte di hardware. Di seguito vengono fornite istruzioni dettagliate per l'uso del calcolatore Azure Stack.

## <a name="worksheet-descriptions"></a>Descrizioni di foglio di lavoro
Di seguito è riportato un breve riepilogo dei fogli di lavoro contenuti nel foglio di calcolo Capacity Planner di Azure Stack che può essere scaricato dal [ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner):

|Nome della scheda|DESCRIZIONE|
|-----|-----|
|Esclusione di versione|Breve panoramica dello scopo del calcolatore, numero di versione e data di rilascio.|
|Istruzioni|Vengono fornite istruzioni dettagliate per l'uso di Azure Stack Capacity Planner.|
|DefinedSolutionSKUs|Tabella a più colonne che contiene le definizioni di hardware fino a cinque. Le voci di questa finestra sono esempi. Lo scopo è che l'utente modificherà i dettagli in base a configurazioni di sistema viene considerate per l'uso o acquistare.|
|DefineByVMFootprint|Creazione di una raccolta di diverse dimensioni e le quantità delle macchine virtuali per trovare l'hardware appropriato dello SKU.|
|DefineByWorkloadFootprint|Creazione di una raccolta di carichi di lavoro di Azure Stack per trovare l'hardware appropriato dello SKU.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>Istruzioni DefinedSolutionSKUs
Questo foglio di lavoro contiene esempi di definizioni di hardware fino a cinque. Acquisto o modificare i dettagli in modo che corrisponda le configurazioni di sistema viene considerate per l'uso.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Selezioni per l'hardware fornite dai partner Hardware autorizzato
Azure Stack viene fornito come sistema integrato con il software installato da partner di soluzioni. Questi partner di soluzioni avrà le proprie, versioni autorevole di strumenti e gli strumenti di pianificazione della capacità deve essere usata per finalizzare le discussioni della capacità della soluzione di Azure Stack.

### <a name="multiple-ways-to-model-computing-resources"></a>Diversi modi per modellare le risorse di calcolo
Blocchi predefiniti di base usati per la risorsa di modellazione entro lo strumento di pianificazione di Azure Stack sono le diverse dimensioni delle macchine virtuali di Azure Stack (VM). Queste vanno macchine virtuali di dimensioni dal più piccolo, "0 Basic", la macchina virtuale più grande corrente, "Standard_Fsv2." In base alle esigenze, quantità diverse di macchine virtuali diverse è utilizzabile per creare computing le allocazioni di risorse usando questo strumento in due modi diversi.

1. L'utente di Microsoft planner consente di selezionare un'offerta di hardware specifico e vede le combinazioni di varie risorse rientrano in questa risorsa di hardware. 

2. L'utente di Microsoft planner consente di creare una combinazione specifica di allocazioni della macchina virtuale e consente quindi di Mostra il calcolatore di risorse di Azure quali SKU hardware disponibili sono in grado di supportare questa configurazione della macchina virtuale.

Questo strumento fornisce due metodi per l'allocazione di risorse della macchina virtuale. come un'unica raccolta di allocazioni di risorse della macchina virtuale oppure come una raccolta di fino a sei diverse configurazioni di carico di lavoro. Ogni configurazione del carico di lavoro può contenere un'allocazione diversi delle risorse della macchina virtuale disponibili. Di seguito vengono fornite informazioni dettagliate per creare e utilizzare ognuno di questi modelli di allocazione. Solo i valori contenuti non sfondo ombreggiato celle o all'interno di elenchi a discesa lo SKU del foglio di lavoro devono essere modificati. Le modifiche apportate all'interno delle celle ombreggiate possono comportare l'interruzione di calcoli di risorse.


## <a name="definebyvmfootprint-instructions"></a>Istruzioni DefineByVMFootprint
Per creare un modello usando un'unica raccolta di diverse dimensioni e la quantità di macchine virtuali, selezionare la scheda "DefineByVMFootprint" e seguire questa sequenza di passaggi.

1. In alto a destra della cartella di lavoro, usare i controlli casella di elenco a discesa fornito di selezionare il numero iniziale di server (tra 4 e 16) che si desidera installare in ogni sistema hardware (SKU). Questo numero di server può essere modificato in qualsiasi momento durante il processo di modellazione per vedere quali effetti complessivamente le risorse disponibili per il modello di allocazione di risorse.
2. Se si desidera modellare varie le allocazioni di risorse della macchina virtuale con una configurazione di hardware specifico, individuare la casella di riepilogo a discesa blu direttamente sotto l'etichetta "SKU correnti" nell'angolo superiore destro della pagina. Questa casella di riepilogo a discesa e selezionare l'hardware desiderato dello SKU.
3. A questo punto si è pronti iniziare ad aggiungere macchine virtuali di dimensioni diverse per il modello. Per includere un determinato tipo di macchina virtuale, immettere un valore della quantità nella casella di contorno blu a sinistra della voce della macchina virtuale.

  > [!NOTE]
  > Spazio di archiviazione totale VM si intende la capacità totale del disco dati della macchina virtuale (numero di dischi supportati * la capacità massima di un singolo disco (1 TB)). Gli indicatori di configurazione in base è stato popolato la tabella di configurazioni di archiviazione disponibile, in modo che è possibile scegliere il livello di risorsa di archiviazione desiderato per ogni macchina virtuale di Azure Stack. Tuttavia, è importante notare che è possibile aggiungere o modificare la tabella di configurazioni di archiviazione disponibili in base alle esigenze.<br><br>Ogni macchina virtuale inizia con un'archiviazione temporanea locale inizialmente assegnata. In modo da riflettere il thin provisioning di archiviazione temporanea il numero locale temporanea può essere modificato a qualsiasi elemento nell'elenco a discesa di menu tra cui la quantità massima di archiviazione temporanea consentiti.

4. Quando si aggiungono macchine virtuali, verranno visualizzati i grafici che mostrano le risorse SKU disponibili la modifica. In questo modo è possibile osservare gli effetti dell'aggiunta di diverse dimensioni e la quantità di macchine virtuali durante il processo di modellazione. Un altro modo per visualizzare l'effetto delle modifiche è guardare i numeri di consumata e ancora disponibili elencati direttamente sotto l'elenco delle macchine virtuali disponibili. Questi numeri riflettono i valori stimati in base all'hardware attualmente selezionato lo SKU.
5. Dopo aver creato il set di macchine virtuali, è possibile trovare l'hardware consigliato SKU facendo clic sul pulsante "SKU suggerito" trovato nell'angolo superiore destro della pagina, direttamente sotto l'etichetta "SKU corrente". Tramite questo pulsante, è possibile quindi modificare le configurazioni di macchina virtuale e verificare quale componente hardware supporta ogni configurazione.


## <a name="definebyworkloadfootprint-instructions"></a>Istruzioni DefineByWorkloadFootprint
Per creare un modello utilizzando una raccolta di carichi di lavoro di Azure Stack, selezionare la scheda "DefineByWorkloadFootprint" e seguire questa sequenza di passaggi. I carichi di lavoro di Azure Stack vengono creati utilizzando le risorse della macchina virtuale disponibili.   

> [!TIP]
> Per modificare le dimensioni di archiviazione specificato per una macchina virtuale di Azure Stack, vedere la nota nel passaggio 3 nella sezione precedente.

1. Nell'angolo superiore destro della pagina, utilizzare i controlli di finestra fornito elenco a discesa per selezionare un numero iniziale di server (tra 4 e 16) che si desidera installare in ogni sistema hardware (SKU).
2. Se si desidera modellare varie le allocazioni di risorse della macchina virtuale con una configurazione di hardware specifico, individuare la casella di riepilogo a discesa blu direttamente sotto l'etichetta "SKU correnti" nell'angolo superiore destro della pagina. Questa casella di riepilogo a discesa e selezionare l'hardware desiderato dello SKU.
3. Selezionare le dimensioni di archiviazione appropriata per tutte le macchine virtuali dello Stack di Azure desiderato nella pagina DefineByVMFootprint come descritto in precedenza nel passaggio 3 di DefineByVMFootprint istruzioni. Le dimensioni di archiviazione per ogni macchina virtuale sono definita nel foglio DefineByVMFootprint.
4. Avvio in alto a sinistra della pagina DefineByWorkloadFootprint, creare configurazioni per un massimo di sei diversi tipi di carico di lavoro, immettere la quantità di ogni tipo di macchina virtuale contenuto all'interno di tale carico di lavoro. Questa operazione viene eseguita inserendo i valori numerici nella colonna direttamente sotto il nome del carico di lavoro specifico. È possibile modificare i nomi di carico di lavoro in modo da riflettere il tipo di carichi di lavoro che sarà supportato da questa configurazione specifica.
5. È possibile includere una quantità specifica di ogni tipo di carico di lavoro immettendo un valore nella parte inferiore di quella colonna direttamente sotto l'etichetta "Quantity".
6. Dopo avere create le quantità e tipi di carico di lavoro, fare clic su "Lo SKU suggerito" pulsante nell'angolo superiore destro della pagina, direttamente sotto l'etichetta "SKU corrente", causerà lo SKU più piccolo con risorse sufficienti per supportare questa complessiva configurazione dei carichi di lavoro da visualizzare.
7. Ulteriormente modellazione può essere realizzato modificando il numero di server selezionato per un hardware SKU, o modificare le allocazioni di macchina virtuale o le quantità all'interno delle configurazioni di carico di lavoro. I grafici associati verranno visualizzato un riscontro immediato che mostra come le modifiche interessano il consumo complessivo risorse.
8. Quando si è soddisfatti con le modifiche, fare nuovamente clic sul pulsante "SKU suggerito" visualizzerà lo SKU suggerito per la nuova configurazione.


## <a name="next-steps"></a>Passaggi successivi
Scopri il [considerazioni relative all'integrazione di Data Center per Azure Stack](azure-stack-datacenter-integration.md)
