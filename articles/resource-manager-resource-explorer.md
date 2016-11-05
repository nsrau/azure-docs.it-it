---
title: Esplora risorse di Azure | Microsoft Docs
description: Descrive Esplora risorse di Azure e come può essere usato per visualizzare e aggiornare le distribuzioni tramite Azure Resource Manager
services: azure-resource-manager
documentationcenter: na
author: stuartleeks
manager: ankodu
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: stuartle;tomfitz

---
# Usare Esplora risorse di Azure per visualizzare e modificare le risorse
[Esplora risorse di Azure](https://resources.azure.com) è uno straordinario strumento per osservare le risorse già create nella sottoscrizione. Tramite questo strumento, è possibile comprendere la struttura delle risorse e vedere le proprietà assegnate a ogni risorsa. Sono fornite informazioni sulle operazioni dell'API REST e sui cmdlet PowerShell disponibili per un tipo di risorsa ed è possibile eseguire comandi tramite l'interfaccia. Esplora risorse può essere particolarmente utile quando si creano modelli di Resource Manager in quanto consente di visualizzare le proprietà per le risorse esistenti.

L'origine per lo strumento Esplora risorse è disponibile su [github](https://github.com/projectkudu/ARMExplorer), che offre un riferimento molto utile per chi desidera implementare un comportamento simile nelle proprie applicazioni.

## Visualizzazione delle risorse
Passare a [https://resources.azure.com](https://resources.azure.com) e accedere con le stesse credenziali usate per il [portale di Azure](https://portal.azure.com).

Dopo il caricamento, la visualizzazione albero a sinistra consente di esaminare le sottoscrizioni e i gruppi di risorse:

![treeview](./media/resource-manager-resource-explorer/are-01-treeview.png)

Quando si analizza un gruppo di risorse è possibile vedere i provider per i quali sono presenti risorse all'interno di tale gruppo:

![provider](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

Da qui è possibile iniziare a esaminare le istanze di risorse. La schermata riportata di seguito mostra l'istanza di SQL Server `sltest` nella visualizzazione albero. Sul lato destro è possibile visualizzare informazioni sulle richieste API REST utilizzabili con tale risorsa. Passando al nodo di una risorsa, Esplora risorse formula automaticamente la richiesta GET per recuperare informazioni sulla risorsa. Nell'ampia area di testo sotto l'URL verrà visualizzata la risposta dell'API.

Dopo avere acquisito familiarità con i modelli di Resource Manager, anche il contenuto del corpo inizierà a sembrare familiare. La sezione **properties** della risposta corrisponde ai valori che è possibile fornire nella sezione **properties** del modello.

![sql server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

Esplora risorse consente di continuare a esaminare le risorse figlio. Nel caso di server di database SQL, sono disponibili risorse figlio per elementi come database e regole del firewall.

L'esplorazione del database ne mostra le relative proprietà. Nella schermata seguente si noti che il database `edition` è `Standard` e `serviceLevelObjective` (o livello database) è `S1`.

![sql database](./media/resource-manager-resource-explorer/are-04-database-get.png)

## Modifica delle risorse
Dopo essere passati a una risorsa, è possibile selezionare il pulsante Modifica per rendere modificabile il contenuto JSON. È quindi possibile utilizzare Esplora risorse per modificare il codice JSON e inviare una richiesta PUT per cambiare la risorsa. Ad esempio, la figura seguente mostra il livello database modificato in `S0`:

![database - PUT request](./media/resource-manager-resource-explorer/are-05-database-put.png)

Selezionando **PUT** si invia la richiesta.

Dopo l'invio della richiesta, Esplora risorse genera nuovamente la richiesta GET per aggiornare lo stato. In questo caso è possibile vedere che `requestedServiceObjectiveId` è stato aggiornato e che differisce da `currentServiceObjectiveId`, indicando un'operazione di ridimensionamento in corso. È possibile fare clic sul pulsante GET per aggiornare lo stato manualmente.

![database - GET request2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## Esecuzione di operazioni sulle risorse
La scheda delle **azioni** consente di visualizzare ed eseguire altre operazioni REST. Ad esempio, quando è stata selezionata una risorsa del sito Web, questa scheda presenta un lungo elenco di operazioni disponibili, alcune delle quali sono illustrate di seguito.

![web - POST request](./media/resource-manager-resource-explorer/are-web-post.png)

## Richiamo dell'API tramite PowerShell
La scheda PowerShell in Esplora risorse mostra i cmdlet da utilizzare per interagire con le risorse che si stanno esplorando. A seconda del tipo di risorsa selezionata, lo script di PowerShell visualizzato può variare da semplici cmdlet (ad esempio `Get-AzureRmResource` e `Set-AzureRmResource`) a cmdlet più complessi (ad esempio lo scambio di slot in un sito web).

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Per altre informazioni sui cmdlet di Azure PowerShell vedere [Uso di Azure PowerShell con Azure Resource Manager](powershell-azure-resource-manager.md)

## Riepilogo
Quando si lavora con Resource Manager, Esplora risorse può rivelarsi uno strumento estremamente utile. È una preziosa soluzione per utilizzare PowerShell per eseguire query e apportare modifiche. Se si utilizza l'API REST, è un ottimo modo per iniziare a utilizzare e testare rapidamente le chiamate API prima di avviare la scrittura di codice. Se si scrivono modelli è un prezioso strumento per comprendere la gerarchia delle risorse e trovare la posizione in cui inserire la configurazione. È possibile apportare una modifica nel portale per ritrovare poi le voci corrispondenti in Esplora risorse.

Per altre informazioni vedere il [video di Channel 9 con Scott Hanselman e David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)

<!---HONumber=AcomDC_0803_2016-->