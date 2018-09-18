---
title: Ridimensionare i nodi di aggiungere Azure Stack | Microsoft Docs
description: Aggiungere i nodi per unità di scala in Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: thoroet
ms.openlocfilehash: 3ce74cdb610f2902133459b913f53bb7809cb4b7
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982998"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Aggiungere nodi di unità di scalabilità aggiuntiva in Azure Stack

Operatori di Azure Stack possono aumentare la capacità complessiva di un'unità di scalabilità esistente mediante l'aggiunta di un computer fisico aggiuntivo. Il computer fisico è detta anche un nodo di unità di scala. Ciascun nuovo nodo di unità di scala che si aggiunge deve essere omogenea nel tipo di CPU, memoria e numero del disco & dimensioni nei nodi che sono già presenti nell'unità di scala.

> [!NOTE]  
È necessario eseguire la scalabilità aggiuntiva di Azure Stack 1807 o versione successiva per aggiungere nodi di unità.

Per aggiungere un nodo di unità di scala, è agire in Azure Stack ed eseguire gli strumenti dalla casa produttrice dell'hardware (OEM). Strumenti dell'OEM viene eseguita sull'host del ciclo di vita dell'hardware (HLH) per assicurarsi che il nuovo computer fisico corrisponda allo stesso livello del firmware nodi esistenti.

Il diagramma di flusso seguente illustra il processo generale per aggiungere un nodo di unità di scala.

![Aggiungere il flusso di unità di scala](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *se il fornitore dell'hardware OEM successivamente vengono eseguiti il posizionamento del rack server fisici e l'aggiornamento del firmware varia in base al contratto di supporto.*

L'operazione di aggiunta di un nuovo nodo può richiedere diverse ore o giorni per il completamento.

> [!Note]  
> Non provare una delle seguente operazioni durante un'operazione di nodo unità di scala aggiunta è già in corso:
>
>  - Aggiornare Azure Stack
>  - Rotazione di certificati
>  - Arrestare Azure Stack
>  - Nodo di unità di scalabilità di ripristino


## <a name="add-scale-unit-nodes"></a>Aggiungere nodi di unità di scala

I passaggi seguenti sono una panoramica generale di come aggiungere un nodo. Non eseguire la procedura senza prima che fa riferimento alla documentazione di espansione della capacità fornita dall'OEM.

1. Posizionare il nuovo server fisico nel rack e cablare, in modo appropriato. 
2. Abilitare porte del commutatore fisico e modificare elenchi di controllo di accesso (ACL), se applicabile.
3. Configurare l'indirizzo IP corretto in baseboard management controller (BMC) e applicare tutte le impostazioni del BIOS per la documentazione fornita dall'OEM.
4. Applicare la linea di base di firmware corrente a tutti i componenti mediante gli strumenti forniti dal produttore dell'hardware eseguirvi il HLH.
5. Eseguire l'operazione di aggiunta nodo nel portale di amministrazione di Azure Stack.
6. Verificare che l'operazione di aggiunta nodo ha esito positivo. A tale scopo, verificare i [ **stato** dell'unità di scala](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Aggiungere il nodo

È possibile usare il portale di amministrazione o PowerShell per aggiungere nuovi nodi. L'operazione di aggiunta nodo innanzitutto aggiunto il nuovo nodo di unità di scala come la capacità di calcolo disponibili e quindi viene esteso automaticamente la capacità di archiviazione. La capacità si espande automaticamente perché Azure Stack è un sistema iperconvergente in cui *compute* e *archiviazione* scalare insieme.

### <a name="use-the-admin-portal"></a>Usare il portale di amministrazione

1. Accedere al portale di amministrazione di Azure Stack come un operatore di Azure Stack.
2. Passare a **+ crea una risorsa** > **capacità** > **nodo di unità di scala**.
   ![Nodo di unità di scala](media/azure-stack-add-scale-node/select-node1.png)
3. Nel **Aggiungi nodo** riquadro, selezionare la *area*e quindi selezionare il *unità di scala* che si desidera aggiungere il nodo a. Specificare anche il *indirizzo IP BMC* per il nodo di unità di scala si sta aggiungendo. È possibile aggiungere solo un nodo alla volta.
   ![Aggiungere i dettagli del nodo](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>Usare PowerShell

Usare la **New-AzsScaleUnitNodeObject** cmdlet per aggiungere un nodo.  

Prima di usare uno degli script di PowerShell di esempio seguenti, sostituire i valori *i nomi dei nodi* e *gli indirizzi IP* con i valori dell'ambiente Azure Stack.

  > [!Note]  
  > Quando un nodo di denominazione è necessario mantenere il nome a meno di 15 caratteri. È anche possibile usare un nome contenente uno spazio o i caratteri seguenti: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`,` }`, `_`.

**Aggiungere un nodo:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>Monitoraggio di aggiungere le operazioni del nodo 
È possibile usare il portale di amministrazione o PowerShell per ottenere lo stato di operazione di aggiunta del nodo. Aggiungere le operazioni del nodo possono richiedere diverse ore a giorni per il completamento.

### <a name="use-the-admin-portal"></a>Usare il portale di amministrazione 
Per monitorare l'aggiunta di un nuovo nodo, esaminare l'unità di scala nel portale di amministrazione o oggetti del nodo unità di scalabilità. A tale scopo, passare a **gestione delle aree** > **unità di scala**. Successivamente, selezionare il nodo di unità di scala da esaminare o unità di scala. 

### <a name="use-powershell"></a>Usare PowerShell
Lo stato per unità di scala e i nodi di unità di scala può essere recuperato usando PowerShell nel modo seguente:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>Stato per l'operazione di aggiunta nodo 
**Per un'unità di scala:**
|Status               |DESCRIZIONE  |
|---------------------|---------|
|In esecuzione              |Tutti i nodi partecipano attivamente l'unità di scala.|
|Arrestato              |Il nodo di unità di scala è attivo o raggiungibile.|
|Espansione            |Uno o più nodi di unità di scala devono essere aggiunti come la capacità di calcolo.|
|Configurazione dell'archiviazione  |È stata ampliata la capacità di calcolo e la configurazione dell'archiviazione è in esecuzione.|
|Richiede correzione |È stato rilevato un errore che richiede uno o più nodi di unità di scala da ripristinare.|


**Per un nodo di unità di scala:**
|Status                |DESCRIZIONE  |
|----------------------|---------|
|In esecuzione               |Il nodo partecipa attivamente nell'unità di scala.|
|Arrestato               |Il nodo è disponibile.|
|Aggiunta in sospeso                |Il nodo viene attivamente viene aggiunto all'unità di scala.|
|Ripristino             |Il nodo è attivamente in fase di manutenzione.|
|Manutenzione            |Il nodo venga sospeso e nessun carico di lavoro utente attivo è in esecuzione. |
|Richiede correzione  |È stato rilevato un errore che richiede che il nodo da ripristinare.|


## <a name="troubleshooting"></a>risoluzione dei problemi
Di seguito sono gli errori comuni riscontrati quando si aggiunge un nodo. 

**Scenario 1:** l'operazione di nodo unità di scala aggiunta ha esito negativo, ma uno o più nodi vengono elencati con stato arrestato.  
- Correzione: Utilizzare l'operazione di ripristino per ripristinare uno o più nodi. Solo un'operazione di ripristino singolo eseguibili in una sola volta.

**Scenario 2:** uno o più nodi di unità di scala sono stati aggiunti ma non è riuscita l'espansione di archiviazione. In questo scenario, l'oggetto nodo unità di scala segnala lo stato di esecuzione, ma non viene avviata l'attività di configurazione dell'archiviazione.  
- Correzione: Usare l'endpoint con privilegi per verificare l'integrità di archiviazione eseguendo il cmdlet di PowerShell seguente:
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**Scenario 3:** è stato ricevuto un avviso che indica il processo di scalabilità orizzontale di archiviazione non è riuscito.  
- Correzione: In questo caso, l'attività di configurazione di archiviazione non è riuscita. Questo problema è necessario contattare il supporto tecnico.


## <a name="next-steps"></a>Passaggi successivi 
[Aggiungere gli indirizzi IP pubblici](azure-stack-add-ips.md) 
