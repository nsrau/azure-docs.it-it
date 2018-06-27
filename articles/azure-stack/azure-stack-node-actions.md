---
title: Ridimensionare le azioni di nodo unità nello Stack di Azure | Documenti Microsoft
description: Informazioni su come visualizzare lo stato del nodo e utilizzare la potenza on, spegnimento, lo svuotamento e riprendere le azioni di nodo in un sistema integrato dello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 3ecc8885a30a11472fe93bbda60c39131c6b3bd7
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801416"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Azioni di nodo unità di scala nello Stack di Azure

*Si applica a: Azure Stack integrati sistemi*

In questo articolo viene descritto come visualizzare lo stato di un'unità di scala e dei relativi nodi associati e come utilizzare le azioni di nodo disponibile. Nodo azioni includono l'accensione sui power off, svuotare, riprenderanno e ripristinano. In genere, queste azioni di nodo è utilizzare durante la sostituzione del campo delle parti o per scenari di ripristino del nodo.

> [!Important]  
> Tutte le azioni di nodo descritte in questo articolo deve essere solo destinazione un nodo alla volta.


## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>Visualizza lo stato di un'unità di scala e dei relativi nodi

Nel portale di amministrazione, è possibile visualizzare facilmente lo stato di un'unità di scala e dei relativi nodi associati.

Per visualizzare lo stato di un'unità di scala:

1. Nel **Gestione area** riquadro, selezionare l'area.
2. A sinistra, sotto **risorse di infrastruttura**, selezionare **unità di scala**.
3. Nei risultati, selezionare l'unità di scala.
 
In questo caso, è possibile visualizzare le informazioni seguenti:

- nome dell'area. Il nome dell'area viene fatto riferimento con **-percorso** nel modulo di PowerShell.
- tipo di sistema
- Totale core logici
- memoria totale
- l'elenco di singoli nodi e il relativo stato; entrambi **in esecuzione** oppure **arrestato**.

![Riquadro di unità di scala che mostra lo stato di esecuzione per ogni nodo](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>Visualizzare informazioni su un nodo di unità di scala

Se si seleziona un singolo nodo, è possibile visualizzare le informazioni seguenti:

- nome dell'area
- modello di server
- Indirizzo IP del baseboard management controller (BMC)
- Stato operativo
- numero totale di core
- quantità totale di memoria
 
![Riquadro di unità di scala che mostra lo stato di esecuzione per ogni nodo](media/azure-stack-node-actions/NodeActions.PNG)

È anche possibile eseguire azioni di nodo unità di scala da qui.

## <a name="scale-unit-node-actions"></a>Azioni di nodo unità di scala

Quando si visualizzano informazioni su un nodo di unità di scala, è possibile eseguire anche azioni del nodo, ad esempio:

- accensione e spegnimento
- svuotamento e resume
- ripristina

Lo stato operativo del nodo determina quali opzioni sono disponibili.

### <a name="power-off"></a>Spegnimento

Il **spegnere** azione disattiva il nodo. Ha lo stesso come se si preme il pulsante di alimentazione. Avviene **non** inviare un segnale di chiusura per il sistema operativo. Per alimentazione pianificato di operazioni, assicurarsi che si svuotare un nodo di unità di scala prima.

Questa azione viene in genere utilizzata quando un nodo è in uno stato bloccato e non risponde alle richieste.

> [!Important] 
> Questa funzionalità è disponibile solo tramite PowerShell. Saranno disponibile nel portale di amministrazione di Azure Stack nuovamente in un secondo momento.


Per eseguire spegnimento azione tramite PowerShell:

````PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```` 

Nel caso improbabile in cui spegnimento azione non funziona, utilizzare l'interfaccia web BMC.

### <a name="power-on"></a>Accendere

Il **accendere** azione attiva nel nodo. Ha lo stesso come se si preme il pulsante di alimentazione. 

> [!Important] 
> Questa funzionalità è disponibile solo tramite PowerShell. Saranno disponibile nel portale di amministrazione di Azure Stack nuovamente in un secondo momento.

Per eseguire la potenza su azione tramite PowerShell:

````PowerShell
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
````

Nel caso improbabile in cui non viene eseguita l'azione di accensione, usare invece l'interfaccia web BMC.

### <a name="drain"></a>Svuotamento

Il **svuotare** azione evacuates tutti i carichi di lavoro attivi tramite la distribuzione tra i nodi rimanenti in tale unità di scala specifico.

Questa azione è in genere usata durante la sostituzione del campo di parti, ad esempio la sostituzione di un nodo dell'intero.

> [!IMPORTANT]  
> Assicurarsi che svuotare un nodo solo durante una finestra di manutenzione pianificata, in cui gli utenti già stati segnalati. In alcune condizioni, i carichi di lavoro attivi può subire interruzioni.

Per eseguire l'azione di svuotamento tramite PowerShell:

  ````PowerShell
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Riprendi

Il **Resume** azione riprende un nodo svuotato e lo contrassegna attivo per il posizionamento del carico di lavoro. I carichi di lavoro precedenti che erano in esecuzione nel nodo di non eseguire il failback. (Se si svuotare un nodo e quindi power off, quando si accende il nodo indietro, non è contrassegnata come attiva per il posizionamento del carico di lavoro. Quando si è pronti, è necessario utilizzare l'azione di ripresa per contrassegnare il nodo come attiva.)

Per eseguire l'azione di ripresa tramite PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Ripristina

Il **correzione** azione consente di ripristinare un nodo. Utilizzarlo solo per uno dei seguenti scenari:

- Sostituzione completa nodo (con o senza nuovi dischi di dati)
- Dopo un errore di componenti hardware e la sostituzione (se consigliato nella documentazione (FRU) di unità sostituibile sul campo).

> [!IMPORTANT]  
> Quando è necessario sostituire un nodo o singoli componenti hardware, vedere documentazione di FRU del produttore dell'hardware OEM per la procedura esatta. La documentazione di FRU specificherà se è necessario eseguire l'azione di ripristino dopo la sostituzione di un componente hardware.  

Quando si esegue l'azione di ripristino, è necessario specificare l'indirizzo IP BMC. 

Per eseguire l'azione di ripristino tramite PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul modulo amministratore infrastruttura dello Stack di Azure, vedere [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.3.0).