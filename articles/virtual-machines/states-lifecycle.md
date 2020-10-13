---
title: Ciclo di vita e Stati di una macchina virtuale in Azure
description: Panoramica del ciclo di vita di una macchina virtuale in Azure, incluse le descrizioni dei diversi stati in cui una macchina virtuale può trovarsi in qualsiasi momento.
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: 8c9641e722cf3892450bbfbea54f169ac6dc764b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963296"
---
# <a name="virtual-machines-lifecycle-and-states"></a>Stati e ciclo di vita di macchine virtuali

Per Macchine virtuali di Azure (VM) sono previsti vari stati che possono essere categorizzati come stati di *provisioning* e di *alimentazione*. Lo scopo di questo articolo è descrivere questi stati e sottolineare in particolare quando viene addebitato l'utilizzo delle istanze ai clienti. 

## <a name="power-states"></a>Stati di alimentazione

Lo stato di alimentazione rappresenta l'ultimo stato noto della macchina virtuale.

![Diagramma degli stati di alimentazione della macchina virtuale](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
La tabella seguente include una descrizione di ogni stato dell'istanza e indica se tale stato genera o meno un addebito per l'utilizzo dell'istanza.

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="":::

   **Descrizione**

   :::column-end:::
   :::column span="":::

   **Utilizzo dell'istanza fatturato**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Avvio in corso**

   :::column-end:::
   :::column span="":::

   Avvio della macchina virtuale in corso.

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **Non fatturato**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Running**

   :::column-end:::
   :::column span="":::

   Stato di funzionamento normale per una macchina virtuale

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **Fatturato**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Stopping**

   :::column-end:::
   :::column span="":::

   Si tratta di uno stato transitorio. Al termine, verrà visualizzato lo stato **Arrestato**.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Fatturato**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Arrestato**

   :::column-end:::
   :::column span="":::

   La macchina virtuale è stato chiusa dall'interno del sistema operativo guest o tramite le API PowerOff.

   L'hardware è ancora allocata alla macchina virtuale e rimane nell'host.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Fatturato***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Deallocazione**

   :::column-end:::
   :::column span="":::

   Stato transitorio. Al termine, lo stato visualizzato per la macchina virtuale sarà **Deallocato**.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Non fatturato***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Deallocato**

   :::column-end:::
   :::column span="":::

   La macchina virtuale è stata arrestata correttamente e rimossa dall'host.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Non fatturato**

   :::column-end:::
:::row-end:::


&#42; alcune risorse di Azure, ad esempio dischi e rete, comportano addebiti. Le licenze software nell'istanza non comportano l'addebito di costi.

## <a name="provisioning-states"></a>Stati di provisioning

Uno stato di provisioning è lo stato di un'operazione del piano di controllo avviata dall'utente sulla macchina virtuale. Questi stati sono separati dallo stato di alimentazione di una macchina virtuale.

- **Crea**: creazione della macchina virtuale.

- **Aggiorna**: aggiorna il modello per una macchina virtuale esistente. Rientrano nello stato di aggiornamento anche alcune modifiche della macchina virtuale che non riguardano il modello, come avvio/riavvio.

- **Elimina**: eliminazione della macchina virtuale.

- **Dealloca**: quando una macchina virtuale viene arrestata e rimossa dall'host. La deallocazione di una macchina virtuale viene considerata un aggiornamento, quindi verranno visualizzati stati di provisioning correlati all'aggiornamento.



Di seguito sono indicati gli stati operativi transitori dopo che la piattaforma ha accettato un'azione avviata dall'utente:

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="2":::

   **Descrizione**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Creating**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Aggiornamento**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Deleting**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Stati di provisioning del sistema operativo**
   
   :::column-end:::
   :::column span="2":::

   **Descrizione**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   Se viene creata una VM con un'immagine del sistema operativo e non con un'immagine specializzata, possono essere indicati i sottostati seguenti:

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningInprogress**

   :::column-end:::
   :::column span="2":::

   La macchina virtuale è in esecuzione ed è in corso l'installazione del sistema operativo guest.
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningComplete**

   :::column-end:::
   :::column span="2":::
   
   Stato di breve durata. La macchina virtuale passa rapidamente allo stato **Operazione riuscita** a meno che non sia necessario installare estensioni. L'installazione delle estensioni può richiedere tempo.
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **Nota**: il provisioning del sistema operativo può passare allo stato **Operazione non riuscita** in presenza di un errore del sistema operativo o se il sistema operativo non viene installato in tempo. I clienti riceveranno un addebito per la macchina virtuale distribuita nell'infrastruttura.

   :::column-end:::

:::row-end:::

Una volta completata l'operazione, la macchina virtuale passerà a uno degli stati seguenti:

- **Operazione riuscita** - Le azioni avviate dall'utente sono state completate.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Operazione non riuscita** - Rappresenta un'operazione non completata. Vedere i codici di errore per ottenere altre informazioni e possibili soluzioni.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Visualizzazione dell'istanza della macchina virtuale

L'API per la visualizzazione dell'istanza fornisce informazioni sullo stato di esecuzione della macchina virtuale. Per altre informazioni, vedere la documentazione dell'API [Virtual Machines - Instance View](/rest/api/compute/virtualmachines/instanceview) (Macchine virtuali - Visualizzazione istanza).

Azure Resource Explorer offre una semplice interfaccia utente per visualizzare lo stato di esecuzione della macchina virtuale: [Resource Explorer](https://resources.azure.com/).

Gli stati di provisioning sono visibili nelle proprietà della macchina virtuale e nella visualizzazione dell'istanza. Gli stati di alimentazione sono disponibili nella visualizzazione dell'istanza di macchina virtuale.

Per recuperare lo stato di alimentazione di tutte le macchine virtuali nella sottoscrizione, usare l'[API Macchine virtuali - Elenca tutte](/rest/api/compute/virtualmachines/listall) con il parametro **statusOnly** impostato su *true*.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio della VM, vedere [monitorare le macchine virtuali in Azure](../azure-monitor/insights/monitor-vm-azure.md).