---
title: Gestire le App per la logica con Visual Studio - App per la logica di Azure
description: Gestire le app per la logica e altri asset di Azure con Visual Studio Cloud Explorer
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 04/02/2019
ms.openlocfilehash: 9654caca5fd4b1f79544ea7303a5d3fff72d22f8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862744"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gestire le app per la logica con Visual Studio

Sebbene sia possibile creare, modificare, gestire e distribuire le App per la logica nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a>, è anche possibile usare Visual Studio quando si desidera aggiungere delle App per la logica per il controllo del codice sorgente, pubblicare versioni diverse e creare [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modelli per diversi ambienti di distribuzione. Con Visual Studio Cloud Explorer, è possibile trovare e gestire app per la logica e altre risorse di Azure. Ad esempio, è possibile aprire, scaricare, modificare, eseguire, visualizzare la cronologia di esecuzione, disabilitare e abilitare app per la logica che sono già state distribuite nel portale di Azure. Se non si ha familiarità con l'uso di App per la logica di Azure in Visual Studio, scoprire come [creare app per la logica con Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Se si distribuisce o si pubblica un'app per la logica da Visual Studio, la versione di tale app nel portale di Azure verrà sovrascritta. Pertanto, se si apportano modifiche nel portale di Azure che si vuole mantenere, assicurarsi di [aggiornare l'app per la logica in Visual Studio](#refresh) dal portale di Azure prima di distribuirla o pubblicarla da Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

* Scaricare e installare questi strumenti, se non sono già disponibili: 

  * <a href="https://aka.ms/download-visual-studio" target="_blank">Visual Studio 2019, 2017 o 2015 - Community Edition o superiore</a>. 
  Questa guida introduttiva usa Visual Studio Community 2017, disponibile gratuitamente.

    > [!IMPORTANT]
    > Quando si installa Visual Studio 2019 o 2017, assicurarsi di selezionare il carico di lavoro **Sviluppo di Azure**.
    > Per altre informazioni, vedere [gestire le risorse associate agli account di Azure in Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).
    >
    > In Visual Studio 2019, Cloud Explorer è possibile aprire la finestra di progettazione di App per la logica nel portale di Azure, ma ancora non è possibile aprire la finestra di progettazione di App per la logica incorporata.

    Per installare Cloud Explorer per Visual Studio 2015 [download di Cloud Explorer da Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). 
    Per altre informazioni, vedere [gestire le risorse associate agli account di Azure in Visual Studio Cloud Explorer (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Azure SDK (2.9.1 o versione successiva)</a> 

  * <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * Strumenti App per la logica di Azure per la versione di Visual Studio desiderata:

    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019" target="_blank">Visual Studio 2019</a>
    
    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017" target="_blank">Visual Studio 2017</a>
    
    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015" target="_blank">Visual Studio 2015</a>

    È anche possibile scaricare e installare Strumenti App per la logica di Azure direttamente da Visual Studio Marketplace o <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">installare questa estensione da Visual Studio</a>. 
    Assicurarsi di riavviare Visual Studio al termine dell'installazione.

* Accesso al Web mentre si usa la finestra integrata Progettazione app per la logica

  La finestra di progettazione richiede una connessione Internet per creare le risorse in Azure e leggere le proprietà e i dati dai connettori nell'app per la logica. 
  Ad esempio, se si usa il connettore di Dynamics CRM Online, la finestra di progettazione verifica le proprietà predefinite e personalizzate disponibili nell'istanza di CRM.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Trovare le app per la logica

In Visual Studio è possibile trovare tutte le app per la logica associate a una sottoscrizione di Azure e distribuite nel portale di Azure usando Cloud Explorer.

1. Aprire Visual Studio. Dal menu **Visualizza** scegliere **Cloud Explorer**.

2. In Cloud Explorer scegliere **Gestione account**. Selezionare la sottoscrizione di Azure associata alle app per la logica e quindi scegliere **Applica**. Ad esempio: 

   ![Scegliere "Gestione account"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. In base alla modalità di ricerca per **Gruppi di risorse** o **Tipi di risorsa**, seguire questi passaggi:

   * **Gruppi di risorse**: nella sottoscrizione di Azure, Cloud Explorer mostra tutti i gruppi di risorse associati a tale sottoscrizione. 
   Espandere il gruppo di risorse contenente l'app per la logica e quindi selezionarla.

   * **Tipi di risorsa**: nella sottoscrizione di Azure, espandere **App per la logica**. Dopo che in Cloud Explorer sono state visualizzate tutte le app per la logica distribuite associate alla sottoscrizione, selezionare l'app per la logica.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Aprire in Visual Studio

In Visual Studio è possibile aprire le app per la logica precedentemente create e distribuite direttamente nel portale di Azure o come progetti di Azure Resource Manager con Visual Studio.

1. Aprire Cloud Explorer e trovare l'app per la logica. 

2. Dal menu di scelta rapida dell'app per la logica selezionare **Open with Logic App Editor** (Apri con Editor app per la logica).

   Questo esempio mostra le app per la logica in base al tipo di risorsa, pertanto le app per la logica verranno visualizzate nella sezione **App per la logica**.

   ![Aprire l'app per la logica distribuita dal portale di Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Dopo che l'app per la logica viene aperta in Progettazione app per la logica, nella parte inferiore della finestra di progettazione è possibile scegliere **visualizzazione Codice** in modo da esaminare la struttura di definizione dell'app per la logica sottostante. 
   Se non si vuole creare un modello di distribuzione per l'app per la logica, scoprire come [scaricare un modello di Azure Resource Manager](#download-logic-app) per tale app per la logica. Altre informazioni sui [modelli di Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Scaricare da Azure

È possibile scaricare le app per la logica dal <a href="https://portal.azure.com" target="_blank">portale di Azure</a> e salvarle come modelli di [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). È quindi possibile modificare i modelli in locale con Visual Studio e personalizzare le app per la logica per ambienti di distribuzione differenti. Il download di app per la logica ne *parametrizza* automaticamente le definizioni all'interno dei [modelli di Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), che usano anche JavaScript Object Notation (JSON).

1. In Visual Studio aprire Cloud Explorer e quindi individuare e selezionare l'app per la logica che si vuole scaricare da Azure.

2. Dal menu di scelta rapida dell'app selezionare **Open with Logic App Editor** (Apri con Editor app per la logica).

   Si apre Progettazione app per la logica che mostra l'app per la logica. 
   Per esaminare la definizione e la struttura sottostanti dell'app per la logica, nella parte inferiore della finestra di progettazione scegliere **Visualizzazione Codice**. 

3. Nella barra degli strumenti della finestra di progettazione scegliere **Scarica**.

   ![Scegliere "Scarica"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Quando viene chiesto di immettere un percorso, selezionare quello desiderato e salvare il modello di Resource Manager per la definizione dell'app per la logica nel formato file JSON (con estensione json). 

La definizione dell'app per la logica viene visualizzata nella sottosezione `resources` all'interno del modello di Resource Manager. È ora possibile modificare la definizione dell'app per la logica e il modello di Resource Manager con Visual Studio. È anche possibile aggiungere il modello come progetto di Azure Resource Manager a una soluzione di Visual Studio. Informazioni sui [progetti di Resource Manager per le app per la logica in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Aggiornare da Azure

Se si modifica l'app per la logica nel portale di Azure e si vogliono mantenere tali modifiche, assicurarsi di aggiornare la versione dell'app in Visual Studio in base a tali modifiche. 

* In Visual Studio, nella barra degli strumenti di Progettazione app per la logica scegliere **Aggiorna**.

  -oppure-

* In Visual Studio Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e scegliere **Aggiorna**. 

![Aggiornare l'app per la logica con aggiornamenti](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Pubblicare aggiornamenti delle app per la logica

Quando si è pronti a distribuire aggiornamenti delle app per la logica da Visual Studio in Azure, nella barra degli strumenti di Progettazione app per la logica scegliere **Pubblica**.

![Pubblicare l'app per la logica aggiornata](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Eseguire manualmente l'app per la logica

È possibile attivare manualmente un'app per la logica distribuita in Azure da Visual Studio. Scegliere **Esegui trigger** nella barra degli strumenti di Progettazione app per la logica.

![Eseguire manualmente l'app per la logica](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Esaminare la cronologia di esecuzione

Per controllare lo stato e diagnosticare i problemi relativi alle esecuzioni dell'app per la logica, è possibile esaminare i dettagli, ad esempio gli input e gli output, per tali esecuzioni in Visual Studio.

1. In Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e selezionare **Apri cronologia di esecuzione**.

   ![Apri cronologia di esecuzione](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. Per visualizzare i dettagli per un'esecuzione specifica, fare doppio clic su di essa. Ad esempio: 

   ![Cronologia di esecuzione dettagliata](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Per ordinare la tabella in base a una proprietà, scegliere l'intestazione di colonna per tale proprietà. 

3. Espandere i passaggi di cui si vogliono esaminare gli input e gli output. Ad esempio: 

   ![Visualizzare gli input e gli output per ogni passaggio](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Disabilitare o abilitare l'app per la logica

Senza eliminare l'app per la logica, è possibile arrestare l'attivazione del trigger la volta successiva in cui viene soddisfatta la condizione di trigger. La disabilitazione dell'app per la logica impedisce al motore di App per la logica di creare ed eseguire istanze di flusso di lavoro future per l'app per la logica.
In Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e scegliere **Disabilita**.

![Disabilitare l'app per la logica](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> Quando si disabilita un'app per la logica, non viene eseguita alcuna nuova istanza di esecuzione. Tutte le esecuzioni in corso e in sospeso continuano fino a quando non vengono completate, il che potrebbe richiedere tempo. 

Quando si è pronti a rimettere in funzione l'app per la logica, è possibile riattivarla. In Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e scegliere **Abilita**.

![Abilitare l'app per la logica](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Eliminare l'app per la logica

Per eliminare l'app per la logica dal portale di Azure, in Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e scegliere **Elimina**.

![Eliminare l'app per la logica](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> Quando si elimina un'app per la logica, non viene eseguita alcuna nuova istanza di esecuzione. Tutte le esecuzioni in corso e in sospeso vengono annullate. Se si dispone di migliaia di esecuzioni, l'annullamento potrebbe richiedere molto tempo. 

## <a name="troubleshooting"></a>risoluzione dei problemi

Quando si apre il progetto dell'app per la logica in Progettazione app per la logica, l'opzione per la selezione della sottoscrizione di Azure potrebbe non essere presente. L'app per la logica viene invece aperta con una sottoscrizione di Azure diversa da quella che si vuole usare. Questo comportamento si verifica perché, dopo aver aperto il file JSON di un'app per la logica, Visual Studio memorizza nella cache la prima sottoscrizione selezionata per un uso futuro. Per risolvere il problema, provare a eseguire uno di questi passaggi:

* Rinominare il file JSON dell'app per la logica. La cache della sottoscrizione dipende dal nome del file.

* Per rimuovere le sottoscrizioni selezionate in precedenza per *tutti* App per la logica nella soluzione, eliminare la cartella di impostazioni Visual Studio di nascosta. (VS) nella directory della soluzione. In questa posizione sono archiviate le informazioni sulla sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come gestire le app per la logica distribuite con Visual Studio. A questo punto, è necessario imparare come personalizzare le definizioni di app per la logica per la distribuzione:

> [!div class="nextstepaction"]
> [Creare definizioni di app per la logica in JSON](../logic-apps/logic-apps-author-definitions.md)
