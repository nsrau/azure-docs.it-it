---
title: Unità nodo azioni di ridimensionamento in Azure Stack | Microsoft Docs
description: Informazioni su come visualizzare lo stato del nodo e utilizzare l'accensione, power off, disabilitare e riprendere le azioni di nodo in un sistema integrato Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 90910580fd7fc766376569de3ce43fc5ce297e8b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54469203"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Unità nodo azioni di ridimensionamento in Azure Stack

*Si applica a: Sistemi integrati di Azure Stack*

Questo articolo descrive come visualizzare lo stato di un'unità di scala. È possibile visualizzare i nodi dell'unità. È possibile eseguire le azioni di nodo, ad esempio power on, power off, arrestare, svuotare, riprendere e ripristinare. In genere, si usano queste azioni nodo durante la sostituzione del campo delle parti o per recuperare un nodo.

> [!Important]  
> Tutte le azioni del nodo descritte in questo articolo deve avere come destinazione un nodo alla volta.

## <a name="view-the-node-status"></a>Visualizzare lo stato del nodo

Nel portale di amministrazione, è possibile visualizzare lo stato di un'unità di scala e i relativi nodi associati.

Per visualizzare lo stato di un'unità di scala:

1. Nel **gestione delle aree** riquadro, selezionare l'area.
2. A sinistra, sotto **risorse di infrastruttura**, selezionare **unità di scala**.
3. Nei risultati, selezionare l'unità di scala.
4. A sinistra, sotto **generali**, selezionare **nodi**.

  Visualizzare le informazioni seguenti:

  - L'elenco dei singoli nodi
  - Stato operativo (vedere l'elenco riportato di seguito)
  - Stato di alimentazione (in esecuzione o arrestato)
  - modello di server
  - Indirizzo IP del baseboard management controller (BMC)
  - Numero totale di core
  - quantità totale di memoria

![stato di un'unità di scala](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Stati operativi nodo

| Status | DESCRIZIONE |
|----------------------|-------------------------------------------------------------------|
| In esecuzione | Il nodo partecipa attivamente nell'unità di scala. |
| Arrestato | Il nodo è disponibile. |
| Aggiunta | Il nodo viene attivamente viene aggiunto all'unità di scala. |
| Ripristino | Il nodo è attivamente in fase di manutenzione. |
| Manutenzione  | Il nodo venga sospeso e nessun carico di lavoro utente attivo è in esecuzione. |
| Richiede correzione | È stato rilevato un errore che richiede che il nodo da ripristinare. |

## <a name="scale-unit-node-actions"></a>Nodo azioni unità di scala

Quando si visualizzano informazioni su un nodo di unità di scala, è anche possibile eseguire le azioni di nodo, ad esempio:
 - Avviare e arrestare (a seconda dello stato di alimentazione corrente)
 - Disabilitare e riprendere (a seconda dello stato di operazioni)
 - Ripristina
 - Shutdown

Lo stato operativo del nodo determina quali opzioni sono disponibili.

È necessario installare i moduli di PowerShell per Azure Stack. Questi cmdlet sono nel **Azs.Fabric.Admin** modulo. Per installare o verificare l'installazione di PowerShell per Azure Stack, vedere [installazione di PowerShell per Azure Stack](azure-stack-powershell-install.md).

## <a name="stop"></a>Arresto

Il **arrestare** azione consente di disattivare il nodo. È lo stesso come se si preme il pulsante di alimentazione. Viene inviato un segnale di chiusura al sistema operativo. Per le operazioni di arresto pianificato, provare l'operazione di arresto prima di tutto. 

Questa azione viene in genere usata quando un nodo è bloccata e non risponde più alle richieste.

Per eseguire l'azione di interruzione, aprire un prompt di PowerShell con privilegi elevato ed eseguire il cmdlet seguente:

```PowerShell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Nel caso improbabile che l'azione di interruzione non funziona, quindi ripetere l'operazione e se ha esito negativo una seconda volta usare l'interfaccia web BMC invece.

Per altre informazioni, vedere [Stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Inizia

Il **avviare** azione attiva nel nodo. È lo stesso come se si preme il pulsante di alimentazione. 
 
Per eseguire l'azione di avvio, aprire un prompt di PowerShell con privilegi elevato ed eseguire il cmdlet seguente:

```PowerShell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Nel caso improbabile che l'azione di avvio non funziona, quindi ripetere l'operazione e se ha esito negativo una seconda volta usare l'interfaccia web BMC invece.

Per altre informazioni, vedere [Start-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Svuotamento

Il **svuotare** azione Sposta tutti i carichi di lavoro attivi per i nodi rimanenti di tale unità di scala particolare.

Questa azione viene in genere usata durante la sostituzione del campo di parti, ad esempio la sostituzione di un intero nodo.

> [!Important]
> Assicurarsi di usare un'operazione di svuotamento in un nodo in una finestra di manutenzione pianificata, in cui gli utenti hanno ricevuto notifica. In alcune condizioni, i carichi di lavoro attivi può subire interruzioni.

Per eseguire l'azione di svuotamento, aprire un prompt di PowerShell con privilegi elevato ed eseguire il cmdlet seguente:

```PowerShell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Per altre informazioni, vedere [Disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Riprendi

Il **riprendere** azione riprende un nodo disabilitato e lo contrassegna attivo per il posizionamento del carico di lavoro. I carichi di lavoro precedenti che erano in esecuzione nel nodo di non eseguire il failback. (Se si usa un'operazione di svuotamento in un nodo assicurarsi di alimentazione. Quando si accende il nodo indietro, non è contrassegnata come attiva per il posizionamento del carico di lavoro. Quando si è pronti, è necessario utilizzare l'azione di ripresa per contrassegnare il nodo come attiva.)

Per eseguire l'azione di ripresa, aprire un prompt di PowerShell con privilegi elevato ed eseguire il cmdlet seguente:

```PowerShell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Per altre informazioni, vedere [Enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Ripristina

Il **riparare** azione consente di ripristinare un nodo. Usarla solo per entrambi gli scenari seguenti:
 - Sostituzione nodo completo (con o senza i dischi dati nuovi)
 - Dopo l'errore del componente hardware e sostituzione (se consigliato nella documentazione (FRU) di unità sostituibile sul campo).

> [!Important]  
> Vedere documentazione del fornitore hardware OEM FRU procedura precisa da applicare quando è necessario sostituire i singoli componenti hardware o un nodo. La documentazione FRU specificherà se è necessario eseguire l'azione di ripristino dopo la sostituzione di un componente hardware. 

Quando si esegue l'azione di ripristino, è necessario specificare l'indirizzo IP del BMC. 

Per eseguire l'azione di ripristino, aprire un prompt di PowerShell con privilegi elevato ed eseguire il cmdlet seguente:

  ```PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Shutdown

Il **arresto** fist azione Sposta tutti i carichi di lavoro attivi per i restanti nodi nella stessa unità di scala. Quindi l'azione normalmente per interrompere l'esecuzione del nodo di unità di scala.

Dopo l'avvio di un nodo che è stato arrestato, è necessario eseguire la [riprendere](#resume) azione. I carichi di lavoro precedenti che erano in esecuzione nel nodo di non eseguire il failback.

Se l'operazione di arresto ha esito negativo, provare il [svuotare](#drain) operazione seguita dall'operazione di arresto.

Per eseguire l'azione di arresto, aprire un prompt di PowerShell con privilegi elevato ed eseguire il cmdlet seguente:

  ```PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo amministratore dell'infrastruttura di Azure Stack, vedere [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).
