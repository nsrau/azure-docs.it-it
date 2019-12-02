---
title: 'Esercitazione: Gestire le query nel portale di Azure'
description: In questa esercitazione viene creata una nuova query di Resource Graph, che viene condivisa con altre persone nel portale di Azure.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: 00cb3f95112804c81beb6bce6fc35891e6197e60
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74303946"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Esercitazione: Creare e condividere una query di Azure Resource Graph nel portale di Azure

Azure Resource Graph Explorer consente di salvare le query di Resource Graph direttamente nel portale di Azure. Esistono due tipi di query: _privata_ e _condivisa_. Una query privata viene salvata nelle impostazioni del portale di Azure. Una query condivisa invece è una risorsa di Resource Manager che può essere gestita con i controlli degli accessi in base al ruolo (RBAC) e protetta con i blocchi di risorse. Entrambi i tipi di query vengono crittografati quando inattive.

Salvare le query nel portale di Azure consente di risparmiare tempo e di avere a disposizione le query preferite o usate più di frequente. Condividendo le query si offre un valido aiuto al proprio team nel realizzare obiettivi di coerenza ed efficienza grazie alla ripetizione.

In questa esercitazione verranno completate le attività seguenti:

> [!div class="checklist"]
> - Creare ed eliminare una query privata
> - Creare una query condivisa
> - Individuare query condivise
> - Eliminare una query condivisa

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-and-delete-a-private-query"></a>Creare ed eliminare una query privata

Le query private sono accessibili e visualizzabili solo dall'account che le crea. Quando vengono salvate nelle impostazioni del portale di Azure di un account, possono essere create, usate ed eliminate solo all'interno del portale di Azure. Una query privata non è una risorsa di Resource Manager. Per creare una nuova query privata, seguire questa procedura:

1. Dal menu del portale selezionare **Tutti i servizi** oppure usare la casella di ricerca di Azure nella parte superiore di tutte le pagine. Cercare e selezionare **Resource Graph Explorer**.

1. Nella scheda **Query 1** della pagina Azure Resource Graph Explorer immettere la query seguente:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Selezionare **Esegui query** per visualizzare i risultati della query nel riquadro inferiore.

   Per altre informazioni su questa query, vedere [Esempi - Contare le macchine virtuali per tipo di sistema operativo](../samples/starter.md#count-virtual-machines-by-os-type).


1. Selezionare **Salva** o **Salva con come**, immettere **Numero di macchine virtuali per tipo di sistema operativo** come nome, lasciare il tipo **Query privata** e selezionare **Salva** nella parte inferiore del riquadro **Salva query**. Il titolo della scheda cambia da **Query 1** a **Numero di macchine virtuali per tipo di sistema operativo**.

1. Uscire e rientrare in Azure Resource Graph Explorer nel portale di Azure. Si noti che la query salvata non viene più visualizzata e che invece è tornata la scheda **Query 1**.

1. Selezionare **Apri una query**. Assicurarsi che il tipo sia **Query privata**. Il nome salvato **Numero di macchine virtuali per tipo di sistema operativo** ora viene visualizzato nell'elenco **Nome query**. Quando si seleziona il collegamento del titolo della query salvata, questo viene caricato in una nuova scheda con il nome della query.

   > [!NOTE] 
   > Quando una query salvata è aperta nella scheda con lo stesso nome, il pulsante **Salva** la aggiorna con le modifiche apportate. Per creare una nuova query salvata da questa query aperta, selezionare **Salva con nome** e procedere come se si stesse salvando una nuova query.

1. Per eliminare la query salvata, selezionare nuovamente **Apri una query** e verificare che il campo **Tipo** sia impostato su **Query privata**. Nella riga della query salvata `Count VMs by OS` selezionare **Elimina** (icona del cestino). Nella finestra di dialogo di conferma selezionare **Sì** per completare l'eliminazione della query.
   Chiudere quindi il riquadro **Apri una query**.

## <a name="create-a-shared-query"></a>Creare una query condivisa

A differenza di una query privata, una query condivisa è una risorsa di Resource Manager. Ciò significa che la query viene salvata in un gruppo di risorse, può essere gestita e controllata con il controllo degli accessi in base al ruolo e può anche essere protetta con i blocchi di risorse. In quanto risorsa, tutti gli utenti che hanno le autorizzazioni appropriate possono visualizzarla e usarla.
Per creare una nuova query condivisa, seguire questa procedura:

1. Dal menu del portale selezionare **Tutti i servizi** oppure usare la casella di ricerca di Azure nella parte superiore di tutte le pagine e selezionare **Resource Graph Explorer**.

1. Nella scheda **Query 1** della pagina Azure Resource Graph Explorer immettere la query seguente:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Selezionare **Esegui query** per visualizzare i risultati della query nel riquadro inferiore.

   Per altre informazioni su questa query, vedere [Esempi - Contare le macchine virtuali per tipo di sistema operativo](../samples/starter.md#count-virtual-machines-by-os-type).

1. Selezionare **Salva** o **Salva con nome**.

   
   ![Salvataggio della nuova query con il pulsante Salva](../media/create-share-query/save-shared-query-buttons.png)

1. Nel riquadro **Salva query** immettere **Numero di macchine virtuali per tipo di sistema operativo** per il nome.

1. Modificare il tipo in **Query condivisa**, impostare la descrizione su **Numero di macchine virtuali per tipo di sistema operativo** e impostare **Sottoscrizione** per specificare dove viene creata la risorsa della query.

1. Lasciare selezionata la casella di controllo **Pubblica nel gruppo di risorse 'resource-graph-queries'** e **Località del gruppo di risorse** impostato su **(Stati Uniti) Stati Uniti centro-occidentali**.

1. Selezionare **Salva** nella parte inferiore del riquadro **Salva query**. Il titolo della scheda cambia da **Query 1** a **Numero di macchine virtuali per tipo di sistema operativo**. La prima volta che si usa il gruppo di risorse **resource-graph-queries**, il salvataggio richiede più tempo del previsto in quanto il gruppo di risorse deve essere creato.
   
   ![Salvataggio della nuova query come query condivisa](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE] 
   > È possibile deselezionare la casella di controllo **Pubblica nel gruppo di risorse 'resource-graph-queries'** se si vuole specificare il nome di un gruppo di risorse esistente in cui salvare la query condivisa. L'uso del gruppo di risorse denominato predefinito per le query rende più semplice l'individuazione delle query condivise e più evidente lo scopo del gruppo di risorse. Tuttavia, è possibile scegliere di selezionare un gruppo di risorse esistente per motivi di sicurezza in base alle autorizzazioni esistenti.

1. Uscire e rientrare in Azure Resource Graph Explorer nel portale di Azure. Si noti che la query salvata non viene più visualizzata e che invece è tornata la scheda **Query 1**.

1. Selezionare **Apri una query**. Verificare che il tipo sia impostato su **Query condivisa** e che la combinazione di **Sottoscrizione** e **Gruppo di risorse** corrisponda al percorso in cui è stata salvata la query. La voce **Numero di macchine virtuali per tipo di sistema operativo** salvata viene ora visualizzata nell'elenco **Nome query**. Selezionare il collegamento del titolo della query salvata per caricarlo in una nuova scheda con il nome della query. Dal momento che la query è condivisa, viene visualizzata un'icona nella scheda accanto al titolo, che la indica come condivisa.

   ![Visualizzazione della query condivisa con icona](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE] 
   > Quando una query salvata è aperta nella scheda con lo stesso nome, il pulsante **Salva** la aggiorna con le modifiche apportate. Per creare una nuova query salvata, selezionare **Salva con nome** e procedere come se si stesse salvando una nuova query.

## <a name="discover-shared-queries"></a>Individuare query condivise

Poiché una query condivisa è una risorsa di Resource Manager, esistono diversi modi per individuarne una:

- Da Resource Graph Explorer selezionare **Apri una query** e impostare il tipo su **Query condivisa**.
- Dalla pagina del portale delle query di Resource Graph.
- Dal gruppo di risorse in cui è stata salvata la query condivisa.
- Tramite una query in Resource Graph.

### <a name="view-resource-graph-queries"></a>Visualizzare le query in Resource Graph

Nel portale di Azure, nella pagina delle query di Resource Graph vengono visualizzate le query condivise a cui l'account connesso può avere accesso. Questa pagina consente di filtrare in base al nome, alla sottoscrizione, al gruppo di risorse e ad altre proprietà della query di Resource Graph. È anche possibile contrassegnare, esportare ed eliminare query di Resource Graph usando questa interfaccia.

Se si seleziona una delle query, viene visualizzata la pagina delle query di Resource Graph. Analogamente ad altre risorse di Resource Manager, questa pagina offre una panoramica interattiva insieme al log attività, al controllo di accesso e ai tag. È anche possibile applicare un blocco di risorsa direttamente da questa pagina.

Per visualizzare la pagina delle query di Resource Graph dal menu del portale, selezionare **Tutti i servizi** oppure usare la casella di ricerca di Azure nella parte superiore di tutte le pagine. Cercare e selezionare **Resource Graph Explorer**.

### <a name="list-resource-groups-resources"></a>Elencare le risorse dei gruppi di risorse

La query di Resource Graph è elencata insieme ad altre risorse che fanno parte di un gruppo di risorse.
Quando si seleziona la query di Resource Graph si apre la pagina della query stessa. I puntini di sospensione e le opzioni del menu di scelta rapida, attivati facendo clic con il pulsante destro del mouse, funzionano allo stesso modo della pagina della query di Resource Graph.

### <a name="query-resource-graph"></a>Eseguire una query in Resource Graph

È possibile trovare le query eseguendo una query in Resource Graph. La query di Resource Graph seguente limita per tipo `Microsoft.ResourceGraph/queries` e usa `project` per elencare solo il nome, l'ora della modifica e la query stessa:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Eliminare una query condivisa

Se una query condivisa non è più necessaria, eliminarla. Eliminando una query condivisa, viene rimossa la risorsa di Resource Manager corrispondente. Tutti i dashboard a cui è stato aggiunto il grafico dei risultati ora visualizzano un messaggio di errore. Quando viene visualizzato il messaggio di errore, usare il pulsante **Rimuovi dal dashboard** per pulire il dashboard.

È possibile eliminare una query condivisa tramite le interfacce seguenti:
- Pagina Query di Resource Graph
- Pagina Query di Resource Graph
- Pagina **Apri una query** in Resource Graph Explorer
- Pagina Gruppi di risorse

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa esercitazione, eliminare le query private e condivise create che non si vuole mantenere.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state create query private e condivise. Per altre informazioni sul linguaggio di Resource Graph, passare alla pagina dei dettagli del linguaggio di query.

> [!div class="nextstepaction"]
> [Altre informazioni sul linguaggio di query](../concepts/query-language.md)