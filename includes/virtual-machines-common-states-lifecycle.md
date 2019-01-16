---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: b48de6a6eeed997fe162cabe4d57e6770e016971
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54122803"
---
Per Macchine virtuali di Azure (VM) sono previsti vari stati che possono essere categorizzati come stati di *provisioning* e di *alimentazione*. Lo scopo di questo articolo è descrivere questi stati e sottolineare in particolare quando viene addebitato l'utilizzo delle istanze ai clienti. 

## <a name="power-states"></a>Stati di alimentazione

Lo stato di alimentazione rappresenta l'ultimo stato noto della macchina virtuale.

![Diagramma degli stati di alimentazione della macchina virtuale](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
La tabella seguente include una descrizione di ogni stato dell'istanza e indica se tale stato genera o meno un addebito per l'utilizzo dell'istanza.

<table>
<tr>
<th>
Stato
</th>
<th>
Descrizione
</th>
<th>
Fatturazione dell'utilizzo dell'istanza
</th>
</tr>
<tr>
<td>
<p><b>Avvio</b></p>
</td>
<td>
<p>Avvio della macchina virtuale in corso.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Non fatturato</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Running</b></p>
</td>
<td>
<p>Stato di funzionamento normale per una macchina virtuale</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Fatturato</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Arresto</b></p>
</td>
<td>
<p>Si tratta di uno stato transitorio. Al termine, verrà visualizzato lo stato **Arrestato**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Fatturato</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Stopped</b></p>
</td>
<td>
<p>La macchina virtuale è stato chiusa dall'interno del sistema operativo guest o tramite le API PowerOff.</p>
<p>L'hardware è ancora allocata alla macchina virtuale e rimane nell'host. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Fatturato&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Deallocazione</b></p>
</td>
<td>
<p>Stato transitorio. Al termine, lo stato visualizzato per la macchina virtuale sarà **Deallocato**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Non fatturato&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Deallocato</b></p>
</td>
<td>
<p>La macchina virtuale è stata arrestata correttamente e rimossa dall'host. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Non fatturato</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Alcune risorse di Azure, ad esempio dischi e rete, comportano l'addebito di costi. Le licenze software nell'istanza non comportano l'addebito di costi.

## <a name="provisioning-states"></a>Stati di provisioning

Uno stato di provisioning è lo stato di un'operazione del piano di controllo avviata dall'utente sulla macchina virtuale. Questi stati sono separati dallo stato di alimentazione di una macchina virtuale.

- **Crea**: creazione della macchina virtuale.

- **Aggiorna**: aggiorna il modello per una macchina virtuale esistente. Rientrano nello stato di aggiornamento anche alcune modifiche della macchina virtuale che non riguardano il modello, come avvio/riavvio.

- **Elimina**: eliminazione della macchina virtuale.

- **Dealloca**: quando una macchina virtuale viene arrestata e rimossa dall'host. La deallocazione di una macchina virtuale viene considerata un aggiornamento, quindi verranno visualizzati stati di provisioning correlati all'aggiornamento.



Di seguito sono indicati gli stati operativi transitori dopo che la piattaforma ha accettato un'azione avviata dall'utente:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Stati</b></p>
</td>
<td width="366">
<p>Descrizione</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Creazione</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Aggiornamento</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Eliminazione in corso</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Stati di provisioning del sistema operativo</b></p>
</td>
<td width="366">
<p>Se viene creata una VM con un'immagine del sistema operativo e non con un'immagine specializzata, possono essere indicati i sottostati seguenti:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; La macchina virtuale è in esecuzione e l'installazione del sistema operativo guest è in corso. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; Stato di breve durata. La macchina virtuale passa rapidamente allo stato **Operazione riuscita** a meno che non sia necessario installare estensioni. L'installazione delle estensioni può richiedere tempo. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Nota</b>: il provisioning del sistema operativo può passare allo stato **Operazione non riuscita** in presenza di un errore del sistema operativo o se il sistema operativo non viene installato in tempo. I clienti riceveranno un addebito per la macchina virtuale distribuita nell'infrastruttura.</p>
</td>
</tr>
</table>


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

L'API per la visualizzazione dell'istanza fornisce informazioni sullo stato di esecuzione della macchina virtuale. Per altre informazioni, vedere la documentazione dell'API [Virtual Machines - Instance View](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) (Macchine virtuali - Visualizzazione istanza).

Azure Resource Explorer offre una semplice interfaccia utente per visualizzare lo stato di esecuzione della macchina virtuale: [Resource Explorer](https://resources.azure.com/).

Gli stati di provisioning sono visibili nelle proprietà della macchina virtuale e nella visualizzazione dell'istanza. Gli stati di alimentazione sono disponibili nella visualizzazione dell'istanza di macchina virtuale. 

