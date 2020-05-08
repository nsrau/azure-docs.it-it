---
title: Completare un ripristino di emergenza di macchine virtuali
description: Questo articolo illustra come completare un ripristino di emergenza di macchine virtuali tramite AVS
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 86f823444d4fff3edf8651f4d949c71d2c981ec7
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740551"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Completare un ripristino di emergenza di macchine virtuali con la soluzione VMWare di Azure

Questo articolo contiene il processo per completare un ripristino di emergenza delle macchine virtuali con la soluzione VMWare Hybrid Cloud Extension (HCX) e l'uso di un cloud privato della soluzione VMWare di Azure come sito di ripristino o di destinazione.

VMWare HCX fornisce varie operazioni che forniscono controllo e granularità precisi nei criteri di replica. Le operazioni disponibili includono:

- Reverse: dopo un'emergenza. Reverse consente di rendere il sito B il sito di origine e il sito A in cui si trova la VM protetta.

- Pausa: sospende i criteri di replica correnti associati alla macchina virtuale selezionata.

- Resume: sospende il criterio di replica corrente associato alla macchina virtuale selezionata.

- Rimuovi: rimuovere il criterio di replica corrente associato alla macchina virtuale selezionata.

- Sincronizza ora: la macchina virtuale di origine della sincronizzazione non è associata alla VM protetta.

In questa guida vengono descritti gli scenari di replica seguenti:

- Proteggere una macchina virtuale o un gruppo di macchine virtuali.

- Completare un ripristino di prova di una macchina virtuale o di un gruppo di macchine virtuali.

- Ripristinare una macchina virtuale o un gruppo di macchine virtuali.

- Protezione inversa di una macchina virtuale o di un gruppo di macchine virtuali.

## <a name="protect-virtual-machines"></a>Proteggi le macchine virtuali

Accedere a **vSphere client** nel sito di origine e accedere al plug-in **HCX**.

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="Opzione HCX in vSphere" border="true":::

Immettere l'area **ripristino di emergenza** e fare clic su **Proteggi VM**.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="Selezionare Proteggi VM" border="true":::

Nella finestra che viene visualizzata selezionare l'origine e i siti remoti, in questo caso il sito remoto dovrebbe essere il cloud privato AVS.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="finestra Proteggi VM" border="true":::

Se necessario, selezionare le opzioni di replica predefinite:

- **Abilita compressione:** Consigliato per scenari con velocità effettiva bassa.

- **Abilita quiescenza:** Sospende la macchina virtuale per garantire che una copia coerente venga sincronizzata con il sito remoto.

- **Archiviazione di destinazione:** Selezionare l'archivio dati remoto per le macchine virtuali protette. In un cloud privato AVS questa selezione deve essere l'archivio dati rete VSAN.

- **Contenitore di calcolo:** Il cluster vSphere remoto o il pool di risorse.

- **Cartella di destinazione:** La cartella di destinazione remota, questa impostazione è facoltativa e se non è selezionata alcuna cartella, le macchine virtuali verranno riavviate direttamente nel cluster selezionato.

- **RPO:** Questo valore è l'intervallo di sincronizzazione tra la macchina virtuale di origine e la macchina virtuale protetta e può essere compreso tra 5 minuti e 24 ore.

- **Intervallo snapshot:** Intervallo tra gli snapshot.

- **Numero di snapshot:** Numero totale di snapshot compresi nell'intervallo di snapshot configurato.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="opzioni di protezione delle macchine virtuali" border="true":::

Selezionare una o più macchine virtuali dall'elenco e configurare le opzioni di replica per la macchina virtuale in base alle esigenze.

Per impostazione predefinita, le macchine virtuali erediteranno il criterio delle impostazioni globali configurato nelle opzioni di replica predefinite. Per ogni interfaccia di rete nella macchina virtuale selezionata, configurare il **gruppo di porte di rete** remota e selezionare **fine** per avviare il processo di protezione.

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="opzioni dell'interfaccia di rete" border="true":::

Come illustrato nell'immagine seguente, è possibile monitorare il processo per ognuna delle macchine virtuali selezionate nella stessa area di ripristino di emergenza.

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="monitorare lo stato di protezione" border="true":::

Dopo che la macchina virtuale è stata protetta, i diversi snapshot possono essere visualizzati nella scheda **snapshot** .

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="elenco di snapshot" border="true":::

Il triangolo giallo indica che gli snapshot e il virtuale non sono stati testati in un'operazione di ripristino di test.

Esistono differenze principali tra una macchina virtuale spenta e una che è accesa.
La schermata precedente Mostra il processo di sincronizzazione per una macchina virtuale accesa. Il processo di sincronizzazione è stato avviato fino a quando non viene completato il primo snapshot, ovvero una copia completa della macchina virtuale, quindi vengono completati quelli successivi nell'intervallo configurato.

Per una macchina virtuale spenta, viene sincronizzata una copia, quindi la macchina virtuale viene visualizzata come inattiva e l'operazione di protezione viene visualizzata come completata.

Quando la macchina virtuale è accesa, avvierà il processo di sincronizzazione nel sito remoto.

## <a name="complete-a-test-recover-of-virtual-machines"></a>Completare un ripristino di test delle macchine virtuali

Accedere a **vSphere client** nel sito remoto, che è il cloud privato AVS. Nel plug-in **HCX**, nell'area ripristino di emergenza selezionare i puntini di sospensione verticali in qualsiasi macchina virtuale per visualizzare il menu operazioni. Selezionare **Verifica ripristino macchina virtuale**.

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="Selezionare il ripristino del test della macchina virtuale" border="true":::

Nella nuova finestra selezionare le opzioni per il test. Selezionare lo snapshot che si vuole usare per testare diversi Stati della macchina virtuale.

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="scegliere uno snapshot e fare clic su test" border="true":::

Dopo aver fatto clic su **test**, viene avviata l'operazione di ripristino.

Al termine dell'operazione di ripristino di test, è possibile archiviare la nuova VM in AVS private cloud vCenter.

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="Controlla operazione di ripristino" border="true":::

Infine, dopo aver eseguito il test nella VM o in qualsiasi applicazione in esecuzione, eseguire una pulizia per eliminare l'istanza di test.

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="Pulisci istanza di test" border="true":::

## <a name="recover-virtual-machines"></a>Ripristinare le macchine virtuali

Accedere a **vSphere client** nel sito remoto, che è il cloud privato AVS, e accedere al plug-in **HCX**.

Per lo scenario di ripristino, un gruppo di macchine virtuali usate per questo esempio.

Selezionare la macchina virtuale da ripristinare nell'elenco, aprire il menu **azioni** e selezionare **Ripristina macchine virtuali**.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="ripristinare le macchine virtuali" border="true":::

Configurare le opzioni di ripristino per ogni istanza e fare clic su **Ripristina** per avviare l'operazione di ripristino.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="Conferma ripristino macchine virtuali" border="true":::

Al termine dell'operazione di ripristino, le nuove macchine virtuali verranno visualizzate nell'inventario server vCenter remoto.

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>Completare una replica inversa nelle macchine virtuali

Accedere a **vSphere client** nel cloud privato AVS e accedere al plug-in **HCX**.
È necessario che le macchine virtuali originali nel sito di origine vengano spente prima di avviare la replica inversa. L'operazione ha esito negativo se le macchine virtuali non sono spente.

Selezionare le macchine virtuali da replicare nuovamente nel sito di origine dall'elenco, aprire il menu **azioni** e selezionare **Inverti**. Nella finestra popup fare clic su **Inverti** per avviare la replica.

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="Selezionare azione inversa in Proteggi operazioni" border="true":::

La replica può essere monitorata nella sezione dei dettagli di ogni macchina virtuale.

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="esaminare i risultati dell'azione inversa" border="true":::

## <a name="disaster-recovery-plan-automation"></a>Automazione del piano di ripristino di emergenza

VMWare HCX attualmente non dispone di un meccanismo incorporato per creare e automatizzare un piano di ripristino di emergenza. Questa funzionalità non esiste in HCX. Tuttavia, fornisce un set di API REST, incluse le API per l'operazione di ripristino di emergenza.

La specifica API può accedere all'interno di HCX Manager nell'URL.

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
