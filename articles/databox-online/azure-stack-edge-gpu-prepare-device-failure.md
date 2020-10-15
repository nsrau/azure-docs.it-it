---
title: Preparare l'errore del dispositivo Pro Azure Stack Edge
description: Viene descritto come ottenere una sostituzione per un dispositivo Azure Stack Edge Pro con errore.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: 0d36a09b67e110551470f3908800e77c35b1e3be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843880"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Preparare un errore del dispositivo GPU Pro Azure Stack Edge

Questo articolo consente di preparare un errore del dispositivo con informazioni dettagliate su come salvare ed eseguire il backup della configurazione e dei dati del dispositivo nel dispositivo GPU Azure Stack Edge Pro. 

L'articolo non include i passaggi per eseguire il backup dei contenitori Kubernetes e Internet per la distribuzione nel dispositivo GPU Pro Azure Stack Edge. 

## <a name="understand-device-failures"></a>Informazioni sugli errori del dispositivo

Il dispositivo GPU Pro Azure Stack Edge può riscontrare due tipi di errori hardware.

- Errori tollerabili che richiedono la sostituzione di un componente hardware. Questi errori consentiranno di usare il dispositivo in uno stato danneggiato. Esempi di questi errori includono un'unica unità alimentatore (alimentatore) non riuscita o un singolo disco guasto nel dispositivo. In ognuno di questi casi, il dispositivo può continuare a funzionare. Si consiglia di contattare supporto tecnico Microsoft al primo posto per sostituire i componenti non riusciti.

- Errori non tollerabili che richiedono la sostituzione dell'intero dispositivo. Un esempio di questo errore si verifica quando due dischi non sono riusciti nel dispositivo. In questi casi, si contatta supporto tecnico Microsoft e dopo aver determinato che è necessaria una sostituzione del dispositivo, è possibile semplificare la sostituzione del dispositivo Azure Stack Edge.

Per preparare gli errori non tollerabili, è necessario eseguire il backup di quanto segue nel dispositivo:

- Informazioni sulla configurazione del dispositivo.
- Dati che si trovano in condivisioni locali perimetrali e condivisioni cloud perimetrali.
- File e cartelle associati alle VM in esecuzione nel dispositivo.


## <a name="back-up-device-configuration"></a>Eseguire il backup della configurazione del dispositivo

Durante la configurazione iniziale del dispositivo, è importante salvare una copia delle informazioni di configurazione del dispositivo come indicato nell' [elenco di controllo della distribuzione](azure-stack-edge-gpu-deploy-checklist.md). Durante il ripristino, verranno usate le informazioni di configurazione per applicare il nuovo dispositivo sostitutivo. 

## <a name="protect-device-data"></a>Proteggere i dati del dispositivo

I dati del dispositivo possono essere di uno dei tipi seguenti:

- Dati in condivisioni cloud perimetrali
- Dati nelle condivisioni locali
- File e cartelle nelle VM

Le sezioni seguenti illustrano i passaggi e le raccomandazioni per proteggere ognuno di questi tipi di dati nel dispositivo.

## <a name="protect-data-in-edge-cloud-shares"></a>Proteggere i dati nelle condivisioni cloud perimetrali

È possibile creare condivisioni cloud perimetrali per i dati di livello dal dispositivo ad Azure. A seconda della larghezza di banda di rete disponibile, configurare i modelli di larghezza di banda nel dispositivo per ridurre al minimo la perdita di dati in caso di errore non tollerabile.

> [!IMPORTANT] 
> Se il dispositivo ha un errore non tollerabile, i dati locali che non sono suddivisi in livelli dal dispositivo ad Azure potrebbero andare persi. 

## <a name="protect-data-in-edge-local-shares"></a>Proteggere i dati nelle condivisioni locali perimetrali

Se si distribuisce Kubernetes o IoT Edge, configurare per salvare i dati dell'applicazione nel dispositivo localmente e non sincronizzarli con archiviazione di Azure. I dati vengono archiviati in una condivisione nel dispositivo. Potrebbe essere importante eseguire il backup dei dati in queste condivisioni.

Le soluzioni di protezione dei dati di terze parti seguenti possono fornire una soluzione di backup per i dati nelle condivisioni SMB o NFS locali. 

| Software di terze parti           | Riferimento alla soluzione                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | https://www.cohesity.com/solution/cloud/azure/ <br> Per informazioni dettagliate, contattare Cohesity.          |
| Veritas                        | Per informazioni dettagliate, contattare Veritas.   |


## <a name="protect-files-and-folders-on-vms"></a>Proteggere file e cartelle nelle VM

Azure Stack Edge funziona con backup di Azure e altre soluzioni di protezione dei dati di terze parti per fornire una soluzione di backup per proteggere i dati contenuti nelle VM distribuite nel dispositivo. Nella tabella seguente sono elencati i riferimenti alle soluzioni disponibili tra cui è possibile scegliere.


| Soluzioni di backup        | Sistema operativo supportato   | Informazioni di riferimento                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agente di Servizi di ripristino di Microsoft Azure (MARS) per backup di Azure | Windows        | [Informazioni sull'agente MARS](/azure/backup/backup-azure-about-mars)    |
| Cohesity                | Windows, Linux | [Riepilogo della soluzione di integrazione, backup e ripristino Microsoft Azure](https://www.cohesity.com/solution/cloud/azure) <br>Per informazioni dettagliate, contattare Cohesity.                          |
| Veritas                 | Windows, Linux | Per informazioni dettagliate, contattare Veritas.                    |



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire [il ripristino da un dispositivo GPU di Azure stack Edge Pro non riuscito](azure-stack-edge-gpu-recover-device-failure.md).
