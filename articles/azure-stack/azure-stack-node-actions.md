---
title: Unità nodo azioni di ridimensionamento in Azure Stack | Microsoft Docs
description: Informazioni su come visualizzare lo stato del nodo e sfruttare la potenza su, power off, lo svuotamento e riprendere le azioni di nodo in un sistema integrato Azure Stack.
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
ms.date: 08/14/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 1f59f2ce6e3bf8d34ce225aa93da76ad523775e0
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2018
ms.locfileid: "42139562"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Unità nodo azioni di ridimensionamento in Azure Stack

*Si applica a: i sistemi integrati di Azure Stack*

Questo articolo descrive come visualizzare lo stato di un'unità di scala e i relativi nodi associati e come usare le azioni di nodo disponibile. Nodo azioni includono power on, power off, svuotare, riprendono e ripristinano. In genere, si usano queste azioni nodo durante la sostituzione del campo di parti o per scenari di ripristino del nodo.

> [!Important]  
> Tutte le azioni del nodo descritte in questo articolo dovrebbero solo destinazione un nodo alla volta.


## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>Visualizzare lo stato di un'unità di scala e i relativi nodi

Nel portale di amministrazione, è possibile visualizzare facilmente lo stato di un'unità di scala e i relativi nodi associati.

Per visualizzare lo stato di un'unità di scala:

1. Nel **gestione delle aree** riquadro, selezionare l'area.
2. A sinistra, sotto **risorse di infrastruttura**, selezionare **unità di scala**.
3. Nei risultati, selezionare l'unità di scala.
 
In questo caso, è possibile visualizzare le informazioni seguenti:

- nome dell'area. Il nome dell'area viene fatto riferimento con **-posizione** nel modulo di PowerShell.
- tipo di sistema
- Totale core logici
- memoria totale
- l'elenco di singoli nodi e il relativo stato; entrambi **in esecuzione** oppure **arrestato**.

![Riquadro di unità di scalabilità che mostra lo stato di esecuzione per ogni nodo](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>Visualizzare informazioni su un nodo di unità di scala

Se si seleziona un singolo nodo, è possibile visualizzare le informazioni seguenti:

- nome dell'area
- modello di server
- Indirizzo IP del baseboard management controller (BMC)
- stato operativo
- numero totale di core
- quantità totale di memoria
 
![Riquadro di unità di scalabilità che mostra lo stato di esecuzione per ogni nodo](media/azure-stack-node-actions/NodeActions.PNG)

Da qui è anche possibile eseguire azioni di nodo di unità di scala.

## <a name="scale-unit-node-actions"></a>Nodo azioni unità di scala

Quando si visualizzano informazioni su un nodo di unità di scala, è anche possibile eseguire le azioni di nodo, ad esempio:

- Svuotamento e la ripresa
- Ripristina

Lo stato operativo del nodo determina quali opzioni sono disponibili.

### <a name="power-off"></a>Spegnimento

Il **spegnere** azione consente di disattivare il nodo. È lo stesso come se si preme il pulsante di alimentazione. Accade **non** inviare un segnale di chiusura al sistema operativo. Per alimentazione pianificata di operazioni, assicurarsi che svuotare un nodo di unità di scala prima di tutto.

Questa azione viene in genere usata quando un nodo è bloccata e non risponde più alle richieste.

> [!Important] 
> Questa funzionalità è disponibile solo tramite PowerShell. Sarà disponibile nel portale di amministrazione di Azure Stack in un secondo momento.


Per l'esecuzione di spegnimento azione tramite PowerShell:

````PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```` 

Nel caso improbabile che spegnimento azione non funziona, usare invece l'interfaccia web BMC.

### <a name="power-on"></a>Accendere

Il **accendere** azione attiva nel nodo. È lo stesso come se si preme il pulsante di alimentazione. 

> [!Important] 
> Questa funzionalità è disponibile solo tramite PowerShell. Sarà disponibile nel portale di amministrazione di Azure Stack in un secondo momento.

Per eseguire la potenza su azioni tramite PowerShell:

````PowerShell
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
````

Nel caso improbabile che l'azione di accensione non funziona, usare invece l'interfaccia web BMC.

### <a name="drain"></a>Svuotamento

Il **svuotare** azione evacuates tutti i carichi di lavoro attivi tramite la distribuzione tra i nodi rimanenti di tale unità di scala particolare.

Questa azione viene in genere usata durante la sostituzione del campo di parti, ad esempio la sostituzione di un intero nodo.

> [!IMPORTANT]  
> Assicurarsi che svuotare un nodo solo durante una finestra di manutenzione pianificata, in cui gli utenti hanno ricevuto notifica. In alcune condizioni, i carichi di lavoro attivi può subire interruzioni.

Per eseguire l'azione di svuotamento tramite PowerShell:

  ````PowerShell
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Riprendi

Il **Riprendi** azione riprende un nodo svuotato e come contrassegnarlo attivo per il posizionamento del carico di lavoro. I carichi di lavoro precedenti che erano in esecuzione nel nodo di non eseguire il failback. (Se si svuotare un nodo e quindi power off, quando si accende il nodo indietro, non è contrassegnata come attiva per il posizionamento del carico di lavoro. Quando si è pronti, è necessario utilizzare l'azione di ripresa per contrassegnare il nodo come attiva.)

Per eseguire l'azione di ripresa tramite PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Ripristina

Il **Repair** azione consente di ripristinare un nodo. Usarla solo per entrambi gli scenari seguenti:

- Sostituzione nodo completo (con o senza i dischi dati nuovi)
- Dopo l'errore del componente hardware e sostituzione (se consigliato nella documentazione (FRU) di unità sostituibile sul campo).

> [!IMPORTANT]  
> Vedere documentazione del fornitore hardware OEM FRU procedura precisa da applicare quando è necessario sostituire i singoli componenti hardware o un nodo. La documentazione FRU specificherà se è necessario eseguire l'azione di ripristino dopo la sostituzione di un componente hardware.  

Quando si esegue l'azione di ripristino, è necessario specificare l'indirizzo IP del BMC. 

Per eseguire l'azione di ripristino tramite PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo amministratore dell'infrastruttura di Azure Stack, vedere [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.4.0).