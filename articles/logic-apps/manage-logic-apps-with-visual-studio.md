---
title: Modificare e gestire le app per la logica usando Visual Studio con Cloud Explorer
description: Modificare, aggiornare, gestire, aggiungere al controllo del codice sorgente e distribuire app per la logica utilizzando Visual Studio con Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 73df5b7f10e038b6894996eb83dec7b6914a4536
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803191"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gestire le app per la logica con Visual Studio

Sebbene sia possibile creare, modificare, gestire e distribuire app per la logica nel portale di [Azure,](https://portal.azure.com)è anche possibile usare Visual Studio quando si desidera aggiungere le app per la logica al controllo del codice sorgente, pubblicare versioni diverse e creare modelli di [Azure Resource Manager](../azure-resource-manager/management/overview.md) per vari ambienti di distribuzione. Con Visual Studio Cloud Explorer, è possibile trovare e gestire app per la logica e altre risorse di Azure. Ad esempio, è possibile aprire, scaricare, modificare, eseguire, visualizzare la cronologia di esecuzione, disabilitare e abilitare app per la logica che sono già state distribuite nel portale di Azure. Se non si ha familiarità con l'uso di App per la logica di Azure in Visual Studio, scoprire come [creare app per la logica con Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

È anche possibile gestire le app per la logica nel portale di Azure.You can also [manage your logic apps in the Azure portal](manage-logic-apps-with-azure-portal.md).

> [!IMPORTANT]
> Se si distribuisce o si pubblica un'app per la logica da Visual Studio, la versione di tale app nel portale di Azure verrà sovrascritta. Pertanto, se si apportano modifiche nel portale di Azure che si vuole mantenere, assicurarsi di [aggiornare l'app per la logica in Visual Studio](#refresh) dal portale di Azure prima di distribuirla o pubblicarla da Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Scaricare e installare questi strumenti, se non sono già disponibili:

  * [Visual Studio 2019, 2017 o 2015 - Community Edition o superiore](https://aka.ms/download-visual-studio). Questa guida introduttiva usa Visual Studio Community 2017, disponibile gratuitamente.

    > [!IMPORTANT]
    > Quando si installa Visual Studio 2019 o 2017, assicurarsi di selezionare il carico di lavoro **Sviluppo di Azure**.
    > Per altre informazioni, vedere Gestire le risorse associate agli account di [Azure in Visual Studio Cloud Explorer.](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view)

    Per installare Cloud Explorer per Visual Studio 2015, [scaricare Cloud Explorer da Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Per altre informazioni, vedere Gestire le risorse associate agli account di [Azure in Visual Studio Cloud Explorer (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 o versione successiva)Azure SDK (2.9.1 or later)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Gli strumenti più recenti per App per la logica di Azure per l'estensione di Visual Studio e la versione desiderata:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    È anche possibile scaricare e installare Strumenti App per la logica di Azure direttamente da Visual Studio Marketplace o [installare questa estensione da Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Assicurarsi di riavviare Visual Studio al termine dell'installazione.

* Accesso al Web mentre si usa la finestra integrata Progettazione app per la logica

  La finestra di progettazione richiede una connessione Internet per creare le risorse in Azure e leggere le proprietà e i dati dai connettori nell'app per la logica. Ad esempio, se si usa il connettore per Dynamics CRM Online, la finestra di progettazione verifica la disponibilità di proprietà predefinite e personalizzate nell'istanza di CRM.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Trovare le app per la logica

In Visual Studio è possibile trovare tutte le app per la logica associate a una sottoscrizione di Azure e distribuite nel portale di Azure usando Cloud Explorer.

1. Aprire Visual Studio. Dal menu **Visualizza** scegliere **Cloud Explorer**.

1. In Cloud Explorer selezionare **Gestione account**. Selezionare la sottoscrizione di Azure associata alle app per la logica e quindi selezionare **Applica**. Ad esempio:

   ![Seleziona "Gestione account"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. In base alla modalità di ricerca per **Gruppi di risorse** o **Tipi di risorsa**, seguire questi passaggi:

   * **Gruppi di risorse**: nella sottoscrizione di Azure, Cloud Explorer mostra tutti i gruppi di risorse associati a tale sottoscrizione. Espandere il gruppo di risorse che contiene l'app per la logica e quindi selezionare l'app per la logica.

   * **Tipi di risorsa**: nella sottoscrizione di Azure, espandere **App per la logica**. Dopo che in Cloud Explorer sono state visualizzate tutte le app per la logica distribuite associate alla sottoscrizione, selezionare l'app per la logica.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Aprire in Visual Studio

In Visual Studio, you can open logic apps previously created and deployed either directly through the Azure portal or as Azure Resource Group projects with Visual Studio.

1. Aprire Cloud Explorer e trovare l'app per la logica.

1. Nel menu di scelta rapida dell'app per la logica selezionare Apri con editor **app per**la logica .

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di aver installato gli aggiornamenti più recenti per Visual Studio.

   Questo esempio mostra le app per la logica in base al tipo di risorsa, pertanto le app per la logica verranno visualizzate nella sezione **App per la logica**.

   ![Aprire l'app per la logica distribuita dal portale di Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Dopo l'apertura dell'app per la logica in Progettazione app per la logica, nella parte inferiore della finestra di progettazione è possibile selezionare **la visualizzazione Codice** in modo da poter esaminare la struttura di definizione dell'app per la logica sottostante. Se non si vuole creare un modello di distribuzione per l'app per la logica, scoprire come [scaricare un modello di Azure Resource Manager](#download-logic-app) per tale app per la logica. Altre informazioni sui [modelli di Resource Manager](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Scaricare da Azure

È possibile scaricare le app per la logica dal [portale di Azure](https://portal.azure.com) e salvarle come modelli di [Azure Resource Manager](../azure-resource-manager/management/overview.md). È quindi possibile modificare i modelli in locale con Visual Studio e personalizzare le app per la logica per ambienti di distribuzione differenti.  Il download di app per la logica ne *parametrizza* automaticamente le definizioni all'interno dei [modelli di Resource Manager](../azure-resource-manager/templates/overview.md), che usano anche JavaScript Object Notation (JSON).

1. In Visual Studio, open Cloud Explorer. Trovare e selezionare l'app per la logica che si vuole scaricare da Azure.Find and select the logic app that you want to download from Azure.

1. Nel menu di scelta rapida dell'app selezionare **Apri con Editor app per la logica**.

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di aver installato gli aggiornamenti più recenti per Visual Studio.

   Si apre Progettazione app per la logica che mostra l'app per la logica. Per esaminare la definizione e la struttura sottostanti dell'app per la logica, nella parte inferiore della finestra di progettazione selezionare **Visualizzazione codice**.

1. Nella barra degli strumenti della finestra di progettazione selezionare **Scarica**.

   ![Scaricare l'app per la logica dal portale di AzureDownload logic app from Azure portal](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Quando viene chiesto di immettere un percorso, selezionare quello desiderato e salvare il modello di Resource Manager per la definizione dell'app per la logica nel formato file JSON (con estensione json).

   La definizione dell'app per la logica viene visualizzata nella sottosezione `resources` all'interno del modello di Resource Manager. È ora possibile modificare la definizione dell'app per la logica e il modello di Resource Manager con Visual Studio. È anche possibile aggiungere il modello come progetto gruppo di risorse di Azure a una soluzione di Visual Studio.You can also add the template as an [Azure Resource Group project](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) to a Visual Studio solution. Informazioni sui [progetti del gruppo di risorse di Azure per le app per la logica in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Collegamento all'account di integrazione

Per creare app per la logica per scenari di integrazione aziendale-business (B2B), è possibile collegare l'app per la logica a un account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) creato in precedenza presente nella stessa area dell'app per la logica. Un account di integrazione contiene elementi B2B, ad esempio partner commerciali, accordi, schemi e mappe, e consente all'app per la logica di usare connettori B2B per la convalida XML e la codifica o la decodifica di file flat. Sebbene sia possibile creare questo collegamento usando il portale di [Azure,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)è anche possibile usare Visual Studio dopo aver rispettato [i prerequisiti](#requirements)e l'app per la logica esiste come file JSON (json) all'interno di un progetto Gruppo di risorse di [Azure.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) Informazioni sui [progetti del gruppo di risorse di Azure per le app per la logica in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. In Visual Studio aprire il progetto Gruppo di risorse di Azure che contiene l'app per la logica.

1. In Esplora soluzioni aprire il<menu di scelta rapida del file **logic-app-name>.json** e selezionare **Apri con Progettazione app per la logica**. (Tastiera: Ctrl e L)

   ![Aprire il file .json dell'app per la logica con Progettazione app per la logicaOpen Logic app's file with Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di disporre degli aggiornamenti più recenti per Visual Studio e l'estensione strumenti di app per la logica di Azure.If you don't have this command in Visual Studio 2019, check that you have the latest updates to Visual Studio and the Azure Logic Apps Tools extension.

1. Assicurarsi che la finestra di progettazione app per la logica abbia lo stato attivo selezionando la scheda o l'area della finestra di progettazione in modo che nella finestra Proprietà venga visualizzata la proprietà Account di **integrazione** per l'app per la logica.

   ![Finestra Proprietà - Proprietà "Account di integrazione"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Se la finestra Proprietà non è già aperta, scegliere **Finestra Proprietà**dal menu **Visualizza** . (Tastiera: premere F4)

1. Aprire l'elenco delle proprietà Account di **integrazione** e selezionare l'account di integrazione che si desidera collegare all'app per la logica, ad esempio:

   ![Aprire l'elenco delle proprietà "Account di integrazione"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Al termine, ricordarsi di salvare la soluzione di Visual Studio.When you're done, remember to save your Visual Studio solution.

Quando si imposta la proprietà Account di **integrazione** in Visual Studio e si salva l'app per la logica come modello di Azure Resource Manager, tale modello include anche una dichiarazione di parametro per l'account di integrazione selezionato. Per altre informazioni sui parametri di modello e sulle app per la logica, vedere [Panoramica: automatizzare](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)la distribuzione di app per la logica.

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Modificare il percorso di distribuzioneChange deployment location

In Visual Studio, se l'app per la logica esiste come file JSON (json) all'interno di un [progetto gruppo](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) di risorse di Azure usato per automatizzare la distribuzione, tale app per la logica viene impostata su un tipo di percorso e un percorso specifico. Questo percorso è un'area di Azure o un ambiente del servizio di integrazione esistente [(ISE).](connect-virtual-network-vnet-isolated-environment.md)

Per modificare il tipo o il percorso dell'app per la logica, è necessario aprire il file di definizione del flusso di lavoro (json) dell'app per la logica da Esplora soluzioni usando Progettazione app per la logica. Non è possibile modificare queste proprietà utilizzando Cloud Explorer.

> [!IMPORTANT]
> La modifica del tipo di posizione da **Regione** a [**Ambiente servizio di integrazione**](connect-virtual-network-vnet-isolated-environment-overview.md) influisce sul modello di [determinazione dei prezzi](logic-apps-pricing.md#fixed-pricing) dell'app per la logica utilizzato per la fatturazione, [i limiti,](logic-apps-limits-and-config.md#integration-account-limits)il [supporto dell'account](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)di integrazione e così via. Prima di selezionare un tipo di posizione diverso, assicurati di comprendere l'impatto risultante sull'app per la logica.

1. In Visual Studio aprire il progetto Gruppo di risorse di Azure che contiene l'app per la logica.

1. In Esplora soluzioni `<logic-app-name>.json` aprire il menu di scelta rapida del file e selezionare **Apri con Progettazione app per**la logica . (Tastiera: Ctrl e L)

   ![Aprire il file .json dell'app per la logica con Progettazione app per la logicaOpen Logic app's file with Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di disporre degli aggiornamenti più recenti per Visual Studio e l'estensione strumenti di app per la logica di Azure.If you don't have this command in Visual Studio 2019, check that you have the latest updates to Visual Studio and the Azure Logic Apps Tools extension.

1. Assicurarsi che la finestra di progettazione app per la logica abbia lo stato attivo selezionando la scheda o l'area della finestra di progettazione in modo che nella finestra Proprietà siano visualizzate le proprietà **Scegli tipo** di posizione e **Posizione** per l'app per la logica. Il tipo di percorso del progetto è impostato su **Area** o **Ambiente servizio di integrazione**.

   ![Finestra Proprietà - "Scegli tipo di posizione" & proprietà "Location"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Se la finestra Proprietà non è già aperta, scegliere **Finestra Proprietà**dal menu **Visualizza** . (Tastiera: premere F4)

1. Per modificare il tipo di posizione, aprire l'elenco di proprietà **Scegli tipo di posizione** e selezionare il tipo di posizione desiderato.

   Ad esempio, se il tipo di posizione è Ambiente servizio di **integrazione**, è possibile selezionare **Area**.

   ![Proprietà "Scegli tipo di posizione" - modifica il tipo di posizione](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Per modificare la posizione specifica, aprire l'elenco delle proprietà **Location.** In base al tipo di posizione, selezionare la posizione desiderata, ad esempio:

   * Selezionare un'area di Azure diversa:Select a different Azure region:

     ![Aprire l'elenco delle proprietà "Posizione", selezionare un'altra area di AzureOpen "Location" property list, select another Azure region](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Selezionare un ISE diverso:

     ![Aprire l'elenco delle proprietà "Posizione", selezionare un'altra ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Al termine, ricordarsi di salvare la soluzione di Visual Studio.When you're done, remember to save your Visual Studio solution.

Quando si modifica il tipo di percorso o il percorso in Visual Studio e si salva l'app per la logica come modello di Azure Resource Manager, tale modello include anche le dichiarazioni di parametro per tale tipo di percorso e percorso. Per altre informazioni sui parametri di modello e sulle app per la logica, vedere [Panoramica: automatizzare](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)la distribuzione di app per la logica.

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Aggiornare da Azure

Se si modifica l'app per la logica nel portale di Azure e si vogliono mantenere tali modifiche, assicurarsi di aggiornare la versione dell'app in Visual Studio in base a tali modifiche.

* Nella barra degli strumenti di Progettazione app per la logica di Visual Studio selezionare **Aggiorna**.

  -oppure-

* In Visual Studio Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e scegliere **Aggiorna**.

![Aggiornare l'app per la logica con aggiornamenti](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Pubblicare aggiornamenti delle app per la logica

Quando si è pronti per distribuire gli aggiornamenti dell'app per la logica da Visual Studio ad Azure, nella barra degli strumenti di Progettazione app per la logica selezionare **Pubblica**.

![Pubblicare l'app per la logica aggiornata nel portale di AzurePublish updated logic app to Azure portal](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Eseguire manualmente l'app per la logica

È possibile attivare manualmente un'app per la logica distribuita in Azure da Visual Studio. Nella barra degli strumenti di Progettazione app per la logica selezionare **Esegui trigger**.

![Eseguire manualmente il trigger per l'app per la logicaManually run trigger for your logic app](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Esaminare la cronologia di esecuzione

Per controllare lo stato e diagnosticare i problemi relativi alle esecuzioni dell'app per la logica, è possibile esaminare i dettagli, ad esempio gli input e gli output, per tali esecuzioni in Visual Studio.

1. In Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e selezionare **Apri cronologia di esecuzione**.

   ![Aprire la cronologia di esecuzione per l'app per la logicaOpen run history for your logic app](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Per visualizzare i dettagli per un'esecuzione specifica, fare doppio clic su di essa. Ad esempio:

   ![Visualizzare informazioni su un'esecuzione specifica](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Per ordinare la tabella in base alla proprietà, selezionare l'intestazione di colonna per tale proprietà.

1. Espandere i passaggi di cui si desidera esaminare gli input e gli output, ad esempio:

   ![Visualizzare gli input e gli output per ogni passaggio](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Disabilitare o abilitare l'app per la logica

Senza eliminare l'app per la logica, è possibile arrestare l'attivazione del trigger la volta successiva in cui viene soddisfatta la condizione di trigger. La disabilitazione dell'app per la logica impedisce al motore di App per la logica di creare ed eseguire istanze di flusso di lavoro future per l'app per la logica. In Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e scegliere **Disabilita**.

![Disabilitare l'app per la logica in Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Quando si disabilita un'app per la logica, non viene eseguita alcuna nuova istanza di esecuzione. Tutte le esecuzioni in corso e in sospeso continuano fino a quando non vengono completate, il che potrebbe richiedere tempo.

Per riattivare l'app per la logica, in Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e selezionare **Abilita**.

![Abilitare l'app per la logica in Cloud ExplorerEnable logic app in Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Eliminare l'app per la logica

Per eliminare l'app per la logica dal portale di Azure, in Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e scegliere **Elimina**.

![Eliminare l'app per la logica dal portale di AzureDelete your logic app from Azure portal](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Quando si elimina un'app per la logica, non viene eseguita alcuna nuova istanza di esecuzione. Tutte le esecuzioni in corso e in sospeso vengono annullate. Se si dispone di migliaia di esecuzioni, l'annullamento potrebbe richiedere molto tempo. 

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando si apre il progetto dell'app per la logica in Progettazione app per la logica, l'opzione per la selezione della sottoscrizione di Azure potrebbe non essere presente. L'app per la logica viene invece aperta con una sottoscrizione di Azure diversa da quella che si vuole usare. Questo comportamento si verifica perché, dopo aver aperto il file JSON di un'app per la logica, Visual Studio memorizza nella cache la prima sottoscrizione selezionata per un uso futuro. Per risolvere il problema, provare a eseguire uno di questi passaggi:

* Rinominare il file JSON dell'app per la logica. La cache della sottoscrizione dipende dal nome del file.

* Per rimuovere le sottoscrizioni selezionate in precedenza per *tutte le* app per la logica nella soluzione, eliminare la cartella delle impostazioni nascosta di Visual Studio (con estensione vs) nella directory della soluzione. In questa posizione sono archiviate le informazioni sulla sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come gestire le app per la logica distribuite con Visual Studio. A questo punto, è necessario imparare come personalizzare le definizioni di app per la logica per la distribuzione:

> [!div class="nextstepaction"]
> [Creare definizioni di app per la logica in JSON](../logic-apps/logic-apps-author-definitions.md)
