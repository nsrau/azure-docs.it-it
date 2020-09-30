---
title: Completare un ripristino di emergenza di macchine virtuali
description: Questo articolo illustra come completare un ripristino di emergenza di macchine virtuali usando la soluzione VMware di Azure
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 2ccb6546b9b01255e4a28aed79fd0d3ccbc4516c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580183"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Completare un ripristino di emergenza di macchine virtuali con la soluzione VMware di Azure

Questo articolo contiene il processo per completare un ripristino di emergenza delle macchine virtuali (VM) con la soluzione VMware HCX e l'uso di un cloud privato della soluzione VMware di Azure come sito di ripristino o di destinazione.

VMware HCX fornisce varie operazioni che forniscono controllo e granularità precisi nei criteri di replica. Le operazioni disponibili includono:

- **Reverse** : dopo un'emergenza. Reverse consente di rendere il sito B il sito di origine e il sito A in cui si trova la VM protetta.

- **Pausa** : sospende i criteri di replica correnti associati alla macchina virtuale selezionata.

- **Resume** : sospende il criterio di replica corrente associato alla macchina virtuale selezionata.

- **Rimuovi** : rimuovere il criterio di replica corrente associato alla macchina virtuale selezionata.

- **Sincronizza ora** : la macchina virtuale di origine della sincronizzazione non è associata alla VM protetta.

In questa guida vengono descritti gli scenari di replica seguenti:

- Proteggere una macchina virtuale o un gruppo di macchine virtuali.

- Completare un ripristino di prova di una macchina virtuale o di un gruppo di macchine virtuali.

- Ripristinare una macchina virtuale o un gruppo di macchine virtuali.

- Protezione inversa di una macchina virtuale o di un gruppo di macchine virtuali.

## <a name="protect-vms"></a>Proteggere le macchine virtuali

1. Accedere a **vSphere client** nel sito di origine e accedere al plug-in **HCX**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="Opzione HCX in vSphere" border="true":::

1. Immettere l'area **ripristino di emergenza** e selezionare **Proteggi VM**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="Opzione HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Selezionare l'origine e i siti remoti. In questo caso il sito remoto dovrebbe essere il cloud privato della soluzione VMware di Azure.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="Opzione HCX in vSphere" border="true":::

1. Se necessario, selezionare le opzioni di **replica predefinite** :

   - **Abilita compressione:** Consigliato per scenari con velocità effettiva bassa.

   - **Abilita quiescenza:** Sospende la macchina virtuale per garantire che una copia coerente venga sincronizzata con il sito remoto.

   - **Archiviazione di destinazione:** Archivio dati remoto per le macchine virtuali protette e in un cloud privato della soluzione VMware di Azure deve essere l'archivio dati rete VSAN.

   - **Contenitore di calcolo:** Cluster vSphere remoto o pool di risorse.

   - **Cartella di destinazione:** Cartella di destinazione remota, che è facoltativa e se non è selezionata alcuna cartella, le macchine virtuali verranno posizionate direttamente nel cluster selezionato.

   - **RPO:** L'intervallo di sincronizzazione tra la macchina virtuale di origine e la macchina virtuale protetta e può essere compreso tra 5 minuti e 24 ore.

   - **Intervallo snapshot:** Intervallo tra gli snapshot.

   - **Numero di snapshot:** Numero totale di snapshot compresi nell'intervallo di snapshot configurato.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="Opzione HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. Selezionare una o più VM dall'elenco e configurare le opzioni di replica in base alle esigenze.

   Per impostazione predefinita, le macchine virtuali ereditano i criteri delle impostazioni globali configurati nelle opzioni di replica predefinite. Per ogni interfaccia di rete nella macchina virtuale selezionata, configurare il **gruppo di porte di rete** remota e selezionare **fine** per avviare il processo di protezione.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="Opzione HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Monitorare il processo per ognuna delle macchine virtuali selezionate nella stessa area di ripristino di emergenza.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="Opzione HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. Dopo che la macchina virtuale è stata protetta, è possibile visualizzare i diversi snapshot nella scheda **snapshot** .

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="Opzione HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   Il triangolo giallo indica che gli snapshot e il virtuale non sono stati testati in un'operazione di ripristino di test.

   Esistono differenze principali tra una macchina virtuale spenta e una che è accesa. L'immagine mostra il processo di sincronizzazione per una VM accesa. Avvia il processo di sincronizzazione fino a quando non termina il primo snapshot, ovvero una copia completa della macchina virtuale, quindi completa quelli successivi nell'intervallo configurato. Per una macchina virtuale spenta, sincronizza una copia, quindi la macchina virtuale viene visualizzata come inattiva e l'operazione di protezione viene visualizzata come completata.  Quando la macchina virtuale è accesa, avvia il processo di sincronizzazione nel sito remoto.

## <a name="complete-a-test-recover-of-vms"></a>Completare un ripristino di test delle macchine virtuali

1. Accedere a **vSphere client** nel sito remoto, che è il cloud privato della soluzione VMware di Azure. 
1. Nel plug-in **HCX**, nell'area ripristino di emergenza selezionare i puntini di sospensione verticali in una macchina virtuale per visualizzare il menu operazioni e quindi selezionare **Verifica ripristino macchina virtuale**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="Opzione HCX in vSphere" border="true":::

1. Selezionare le opzioni per il test e lo snapshot che si vuole usare per testare diversi Stati della macchina virtuale.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="Opzione HCX in vSphere" border="true":::

1. Dopo aver selezionato **test**, viene avviata l'operazione di ripristino.

1. Al termine, è possibile controllare la nuova macchina virtuale nel cloud privato della soluzione VMware di Azure.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="Opzione HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. Una volta eseguito il test sulla macchina virtuale o su qualsiasi applicazione in esecuzione, eseguire una pulizia per eliminare l'istanza di test.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="Opzione HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Ripristinare le macchine virtuali

1. Accedere a **vSphere client** nel sito remoto, che è il cloud privato della soluzione VMware di Azure, e accedere al plug-in **HCX**.

   Per lo scenario di ripristino, un gruppo di macchine virtuali usate per questo esempio.

1. Selezionare la macchina virtuale da ripristinare nell'elenco, aprire il menu **azioni** e selezionare **Ripristina macchine virtuali**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="Opzione HCX in vSphere" border="true":::

1. Configurare le opzioni di ripristino per ogni istanza e selezionare **Ripristina** per avviare l'operazione di ripristino.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="Opzione HCX in vSphere" border="true":::

1. Al termine dell'operazione di ripristino, le nuove macchine virtuali vengono visualizzate nell'inventario server vCenter remoto.

## <a name="complete-a-reverse-replication-on-vms"></a>Completare una replica inversa nelle VM

1. Accedere a **vSphere client** nel cloud privato della soluzione VMware di Azure e accedere al plug-in **HCX**.
   
   >[!NOTE]
   >Verificare che le macchine virtuali originali nel sito di origine siano spente prima di avviare la replica inversa. L'operazione ha esito negativo se le macchine virtuali non sono spente.

1. Nell'elenco selezionare le VM da replicare nel sito di origine, aprire il menu **azioni** e selezionare **inverso**. 
1. Selezionare **Inverti** per avviare la replica.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="Opzione HCX in vSphere" border="true":::

1. Monitorare la sezione dei dettagli di ogni macchina virtuale.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="Opzione HCX in vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Automazione del piano di ripristino di emergenza

VMware HCX attualmente non dispone di un meccanismo incorporato per creare e automatizzare un piano di ripristino di emergenza. Tuttavia, VMware HCX fornisce un set di API REST, incluse le API per l'operazione di ripristino di emergenza. È possibile accedere alla specifica API all'interno di VMware HCX Manager nell'URL.

Le seguenti operazioni nel ripristino di emergenza sono coperte da queste API.

- Protezione

- Recupera

- Ripristino del test

- Ripristino pianificato

- Reverse

- Query

- Pulizia del test

- Sospendi

- Riprendi

- Rimuovi protezione

- Riconfigurare

Di seguito è riportato un esempio di payload di un'operazione di ripristino in JSON.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Usando queste API, un cliente può creare un meccanismo personalizzato per automatizzare la creazione e l'esecuzione di un piano di ripristino di emergenza.
