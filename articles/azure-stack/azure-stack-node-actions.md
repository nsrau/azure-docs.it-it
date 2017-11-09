---
title: "Azioni nodo dell'unità di scala nello Stack di Azure | Documenti Microsoft"
description: Informazioni su come visualizzare lo stato del nodo, scegliere la potenza, spegnimento, svuotamento e riprendere azioni del nodo in un sistema integrato dello Stack di Azure.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: dbb68b10-c721-4188-aa07-584d0cd63138
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: twooley
ms.openlocfilehash: 3696cd0da0859bebf001f7749ac8874efd574046
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Azioni di nodo di unità di scala nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo viene descritto come visualizzare lo stato di un'unità di scala e i relativi nodi associati e come utilizzare le azioni di nodo disponibile. Nodo azioni includono l'accensione, power off, svuotare, riprenderanno e ripristinano. In genere, queste azioni di nodo è utilizzare durante la sostituzione del campo di parti o per scenari di ripristino del nodo.

## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>Visualizza lo stato di un'unità di scala e i relativi nodi

Nel portale di amministrazione, è possibile visualizzare facilmente lo stato di un'unità di scala e i relativi nodi associati.

Per visualizzare lo stato di un'unità di scala:

1. Nel **Gestione area** riquadro, selezionare l'area.
2. A sinistra, sotto **risorse dell'infrastruttura**selezionare **unità di scala**.
3. Nei risultati, selezionare l'unità di scala.
 
In questo caso, è possibile visualizzare le informazioni seguenti:

- nome dell'area
- tipo di sistema
- Totale core logici
- memoria totale
- l'elenco di singoli nodi e il relativo stato; sia in esecuzione o arrestato.

![Riquadro di unità di scala che mostra lo stato di esecuzione per ogni nodo](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>Visualizzare informazioni su un nodo di unità di scala

Se si seleziona un singolo nodo, è possibile visualizzare le informazioni seguenti:

- nome dell'area
- modello di server
- Indirizzo IP del baseboard management controller (BMC)
- Stato operativo
- Numero totale di core
- quantità totale di memoria
 
![Riquadro di unità di scala che mostra lo stato di esecuzione per ogni nodo](media/azure-stack-node-actions/NodeActions.PNG)

È anche possibile eseguire azioni nodo unità di scala da qui.

## <a name="scale-unit-node-actions"></a>Azioni nodo unità di scala

Quando si visualizzano informazioni su un nodo di unità di scala, è possibile eseguire anche azioni del nodo, ad esempio:

- accensione e spegnimento
- svuotamento e resume
- ripristino

Lo stato operativo del nodo determina quali opzioni sono disponibili.

### <a name="power-off"></a>Spegnimento

Il **spegnere** azione disattiva il nodo. Ha lo stesso come se si preme il pulsante di alimentazione. Caso **non** inviare un segnale di arresto del sistema operativo. Per le operazioni di alimentazione pianificato, assicurarsi che si svuotare un nodo di unità di scala prima.

Questa operazione viene in genere utilizzata quando un nodo è in uno stato bloccato e non risponde alle richieste.  

Per eseguire lo spegnimento azione tramite PowerShell:

  ````PowerShell
  Stop-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ```` 

Nel caso improbabile in cui spegnimento azione non funziona, utilizzare l'interfaccia web BMC.

### <a name="power-on"></a>Accendere

Il **accendere** azione attiva nel nodo. Ha lo stesso come se si preme il pulsante di alimentazione. 

Per eseguire la potenza azione tramite PowerShell:

  ````PowerShell
  Start-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

Nel caso improbabile in cui l'azione di accensione non funziona, utilizzare l'interfaccia web BMC.

### <a name="drain"></a>Svuotamento

Il **svuotare** azione evacuates tutti i carichi di lavoro attivi tramite la distribuzione tra i nodi rimanenti in tale unità di scala specifico.

Questa operazione viene in genere utilizzata durante la sostituzione di parti, ad esempio la sostituzione di un intero nodo campo.

> [!IMPORTANT]
> Assicurarsi svuotare un nodo solo durante una finestra di manutenzione pianificata, in cui gli utenti già stati segnalati. In alcune condizioni, i carichi di lavoro attivi può subire interruzioni.

Per eseguire l'azione di svuotamento tramite PowerShell:

  ````PowerShell
  Disable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Riprendi

Il **Resume** azione riprende un nodo svuotato e contrassegna la proprietà attiva per il posizionamento del carico di lavoro. I carichi di lavoro precedenti che erano in esecuzione nel nodo di non eseguire il failback. (Se si svuotare un nodo e spegnimento, quando si accende il nodo indietro, non è contrassegnata come attiva per il posizionamento del carico di lavoro. Quando si è pronti, è necessario utilizzare l'azione di ripresa per contrassegnare il nodo come attiva.)

Per eseguire l'azione di ripresa tramite PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Ripristino

Il **ripristino** azione consente di ripristinare un nodo. Utilizzarlo solo per uno dei seguenti scenari:

- Sostituzione completa di nodo (con o senza nuovi dischi di dati)
- Dopo un errore di componenti hardware e la sostituzione (se consigliato nella documentazione (FRU) di unità sostituibile sul campo).

> [!IMPORTANT]
> Quando è necessario sostituire un nodo o i singoli componenti hardware, vedere documentazione di FRU del produttore dell'hardware OEM per la procedura esatta. La documentazione di FRU specificherà se è necessario eseguire l'azione di ripristino dopo la sostituzione di un componente hardware.  

Quando si esegue l'azione di ripristino, è necessario specificare l'indirizzo IP BMC. 

Per eseguire l'azione di ripristino tramite PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Region <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````


