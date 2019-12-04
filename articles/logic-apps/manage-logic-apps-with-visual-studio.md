---
title: Gestire app per la logica con Visual Studio
description: Gestire app per la logica e altre risorse di Azure usando Visual Studio con Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 29b1235ee319567e103267b9054b8c6b244e1ca7
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790843"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gestire le app per la logica con Visual Studio

Sebbene sia possibile creare, modificare, gestire e distribuire app per la logica nella [portale di Azure](https://portal.azure.com), è anche possibile usare Visual Studio quando si desidera aggiungere app per la logica al controllo del codice sorgente, pubblicare versioni diverse e creare modelli [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) per vari ambienti di distribuzione. Con Visual Studio Cloud Explorer, è possibile trovare e gestire app per la logica e altre risorse di Azure. Ad esempio, è possibile aprire, scaricare, modificare, eseguire, visualizzare la cronologia di esecuzione, disabilitare e abilitare app per la logica che sono già state distribuite nel portale di Azure. Se non si ha familiarità con l'uso di App per la logica di Azure in Visual Studio, scoprire come [creare app per la logica con Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Se si distribuisce o si pubblica un'app per la logica da Visual Studio, la versione di tale app nel portale di Azure verrà sovrascritta. Pertanto, se si apportano modifiche nel portale di Azure che si vuole mantenere, assicurarsi di [aggiornare l'app per la logica in Visual Studio](#refresh) dal portale di Azure prima di distribuirla o pubblicarla da Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Scaricare e installare questi strumenti, se non sono già disponibili:

  * [Visual Studio 2019, 2017 o 2015 - Community Edition o superiore](https://aka.ms/download-visual-studio). Questa guida introduttiva usa Visual Studio Community 2017, disponibile gratuitamente.

    > [!IMPORTANT]
    > Quando si installa Visual Studio 2019 o 2017, assicurarsi di selezionare il carico di lavoro **Sviluppo di Azure**.
    > Per altre informazioni, vedere [gestire le risorse associate agli account di Azure in Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Per installare Cloud Explorer per Visual Studio 2015, [scaricare Cloud Explorer dal Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Per altre informazioni, vedere [gestire le risorse associate agli account di Azure in Visual Studio Cloud Explorer (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 o versione successiva)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Gli strumenti più recenti per App per la logica di Azure per l'estensione di Visual Studio e la versione desiderata:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    È anche possibile scaricare e installare Strumenti App per la logica di Azure direttamente da Visual Studio Marketplace o [installare questa estensione da Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Assicurarsi di riavviare Visual Studio dopo avere completato l'installazione.

* Accesso al Web mentre si usa la finestra integrata Progettazione app per la logica

  La finestra di progettazione richiede una connessione Internet per creare le risorse in Azure e leggere le proprietà e i dati dai connettori nell'app per la logica. Ad esempio, se si usa il connettore di Dynamics CRM Online, la finestra di progettazione verifica le proprietà predefinite e personalizzate disponibili nell'istanza di CRM.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Trovare le app per la logica

In Visual Studio è possibile trovare tutte le app per la logica associate a una sottoscrizione di Azure e distribuite nel portale di Azure usando Cloud Explorer.

1. Aprire Visual Studio. Dal menu **Visualizza** scegliere **Cloud Explorer**.

1. In Cloud Explorer selezionare **Gestione account**. Selezionare la sottoscrizione di Azure associata alle app per la logica e quindi selezionare **applica**. ad esempio:

   ![Selezionare "Gestione account"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. In base alla modalità di ricerca per **Gruppi di risorse** o **Tipi di risorsa**, seguire questi passaggi:

   * **Gruppi di risorse**: nella sottoscrizione di Azure, Cloud Explorer mostra tutti i gruppi di risorse associati a tale sottoscrizione. Espandere il gruppo di risorse che contiene l'app per la logica e quindi selezionare l'app per la logica.

   * **Tipi di risorsa**: nella sottoscrizione di Azure, espandere **App per la logica**. Dopo che in Cloud Explorer sono state visualizzate tutte le app per la logica distribuite associate alla sottoscrizione, selezionare l'app per la logica.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Aprire in Visual Studio

In Visual Studio è possibile aprire app per la logica create in precedenza e distribuite direttamente tramite il portale di Azure o come progetti di gruppi di risorse di Azure con Visual Studio.

1. Aprire Cloud Explorer e trovare l'app per la logica.

1. Dal menu di scelta rapida dell'app per la logica selezionare **Open with Logic App Editor** (Apri con Editor app per la logica).

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di aver installato gli aggiornamenti più recenti per Visual Studio.

   Questo esempio mostra le app per la logica in base al tipo di risorsa, pertanto le app per la logica verranno visualizzate nella sezione **App per la logica**.

   ![Aprire l'app per la logica distribuita dal portale di Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Dopo l'apertura dell'app per la logica nella finestra di progettazione di app per la logica, nella parte inferiore della finestra di progettazione è possibile selezionare **visualizzazione codice** per poter esaminare la struttura della definizione di app per la logica sottostante. Se non si vuole creare un modello di distribuzione per l'app per la logica, scoprire come [scaricare un modello di Azure Resource Manager](#download-logic-app) per tale app per la logica. Altre informazioni sui [modelli di Resource Manager](../azure-resource-manager/template-deployment-overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Scaricare da Azure

È possibile scaricare le app per la logica dal [portale di Azure](https://portal.azure.com) e salvarle come modelli di [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). È quindi possibile modificare i modelli in locale con Visual Studio e personalizzare le app per la logica per ambienti di distribuzione differenti.  Il download di app per la logica ne *parametrizza* automaticamente le definizioni all'interno dei [modelli di Resource Manager](../azure-resource-manager/template-deployment-overview.md), che usano anche JavaScript Object Notation (JSON).

1. In Visual Studio aprire Cloud Explorer. Trovare e selezionare l'app per la logica che si vuole scaricare da Azure.

1. Dal menu di scelta rapida dell'app selezionare **Open with Logic App Editor** (Apri con Editor app per la logica).

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di aver installato gli aggiornamenti più recenti per Visual Studio.

   Si apre Progettazione app per la logica che mostra l'app per la logica. Per esaminare la definizione e la struttura sottostanti dell'app per la logica, nella parte inferiore della finestra di progettazione selezionare **visualizzazione codice**.

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Scarica**.

   ![Scaricare l'app per la logica da portale di Azure](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Quando viene chiesto di immettere un percorso, selezionare quello desiderato e salvare il modello di Resource Manager per la definizione dell'app per la logica nel formato file JSON (con estensione json).

   La definizione dell'app per la logica viene visualizzata nella sottosezione `resources` all'interno del modello di Resource Manager. È ora possibile modificare la definizione dell'app per la logica e il modello di Resource Manager con Visual Studio. È anche possibile aggiungere il modello come [progetto del gruppo di risorse di Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) a una soluzione di Visual Studio. Informazioni sui [progetti gruppo di risorse di Azure per app per la logica in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Collegamento all'account di integrazione

Per creare app per la logica per scenari di integrazione aziendale B2B (business to business), è possibile collegare l'app per la logica a un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) creato in precedenza esistente nella stessa area dell'app per la logica. Un account di integrazione contiene elementi B2B, quali partner commerciali, contratti, schemi e mappe, e consente all'app per la logica di usare i connettori B2B per la convalida XML e la codifica o la decodifica dei file flat. Sebbene sia possibile [creare questo collegamento usando il portale di Azure](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), è anche possibile usare Visual Studio dopo aver soddisfatto i [prerequisiti](#requirements)e l'app per la logica esiste come file JSON (. Json) all'interno di un [progetto di gruppo di risorse di Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Informazioni sui [progetti gruppo di risorse di Azure per app per la logica in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. In Visual Studio aprire il progetto gruppo di risorse di Azure che contiene l'app per la logica.

1. In Esplora soluzioni aprire il menu di scelta rapida **< nome-app-logica >. JSON** e selezionare **Apri con progettazione app per la logica**. (Tastiera: CTRL + L)

   ![Apri il file con estensione JSON dell'app per la logica con progettazione app per la logica](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di avere gli aggiornamenti più recenti per Visual Studio e l'estensione strumenti app per la logica di Azure.

1. Assicurarsi che la finestra di progettazione dell'app per la logica abbia lo stato attivo selezionando la scheda o la superficie della finestra di progettazione in modo che la Finestra Proprietà mostri la proprietà dell' **account di integrazione** per l'app per la logica

   ![Finestra Proprietà-Proprietà "account di integrazione"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Se la Finestra Proprietà non è già aperta, scegliere **finestra Proprietà**dal menu **Visualizza** . (Tastiera: premere F4)

1. Aprire l'elenco delle proprietà dell' **account di integrazione** e selezionare l'account di integrazione da collegare all'app per la logica, ad esempio:

   ![Aprire l'elenco delle proprietà "account di integrazione"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Al termine, ricordarsi di salvare la soluzione di Visual Studio.

Quando si imposta la proprietà dell' **account di integrazione** in Visual Studio e si salva l'app per la logica come modello di Azure Resource Manager, tale modello include anche una dichiarazione di parametro per l'account di integrazione selezionato. Per altre informazioni sui parametri del modello e sulle app per la logica, vedere [Panoramica: automatizzare la distribuzione delle app](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)per la logica.

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Modificare il percorso di distribuzione

In Visual Studio, se l'app per la logica esiste come file JSON (JSON) in un [progetto di gruppo di risorse di Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) usato per automatizzare la distribuzione, l'app per la logica è impostata su un tipo di percorso e una posizione specifica. Questo percorso è un'area di Azure o un [ambiente del servizio di integrazione esistente (ISE)](connect-virtual-network-vnet-isolated-environment.md).

Per modificare il tipo o il percorso dell'app per la logica, è necessario aprire il file di definizione del flusso di lavoro dell'app per la logica (con estensione JSON) da Esplora soluzioni usando la finestra di progettazione dell'app per la logica. Non è possibile modificare queste proprietà utilizzando Cloud Explorer.

> [!IMPORTANT]
> La modifica del tipo di percorso da **Region** a [**ambiente del servizio di integrazione**](connect-virtual-network-vnet-isolated-environment-overview.md) influiscono sul [modello di determinazione prezzi](logic-apps-pricing.md#fixed-pricing) dell'app per la logica usato per la fatturazione, i [limiti](logic-apps-limits-and-config.md#integration-account-limits), il supporto dell' [account di integrazione](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)e così via. Prima di selezionare un altro tipo di percorso, verificare di aver compreso l'effetto risultante sull'app per la logica.

1. In Visual Studio aprire il progetto gruppo di risorse di Azure che contiene l'app per la logica.

1. In Esplora soluzioni aprire il menu di scelta rapida del file `<logic-app-name>.json` e selezionare **Apri con progettazione app per la logica**. (Tastiera: CTRL + L)

   ![Apri il file con estensione JSON dell'app per la logica con progettazione app per la logica](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di avere gli aggiornamenti più recenti per Visual Studio e l'estensione strumenti app per la logica di Azure.

1. Assicurarsi che la finestra di progettazione dell'app per la logica abbia lo stato attivo selezionando la scheda o la superficie della finestra di progettazione in modo che l'Finestra Proprietà visualizzi le proprietà **Scegli tipo di percorso** e **percorso** per l'app per la logica. Il tipo di percorso del progetto è impostato su **Region** o **ambiente del servizio di integrazione**.

   ![Finestra Proprietà-"Scegli tipo di percorso" & proprietà "percorso"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Se la Finestra Proprietà non è già aperta, scegliere **finestra Proprietà**dal menu **Visualizza** . (Tastiera: premere F4)

1. Per modificare il tipo di percorso, aprire l'elenco **Scegli proprietà tipo di percorso** e selezionare il tipo di percorso desiderato.

   Se, ad esempio, il tipo di percorso è **ambiente del servizio di integrazione**, è possibile selezionare **Region (area**).

   ![Proprietà "Scegli tipo di percorso"-modifica del tipo di percorso](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Per modificare il percorso specifico, aprire l'elenco delle proprietà **location** . In base al tipo di percorso, selezionare il percorso desiderato, ad esempio:

   * Selezionare un'area di Azure diversa:

     ![Aprire l'elenco delle proprietà "location", selezionare un'altra area di Azure](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Selezionare un'altra ISE:

     ![Aprire l'elenco delle proprietà "location", selezionare un altro ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Al termine, ricordarsi di salvare la soluzione di Visual Studio.

Quando si modifica il tipo di percorso o il percorso in Visual Studio e si salva l'app per la logica come modello di Azure Resource Manager, il modello include anche le dichiarazioni di parametro per il tipo e il percorso del percorso. Per altre informazioni sui parametri del modello e sulle app per la logica, vedere [Panoramica: automatizzare la distribuzione delle app](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)per la logica.

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Aggiornare da Azure

Se si modifica l'app per la logica nel portale di Azure e si vogliono mantenere tali modifiche, assicurarsi di aggiornare la versione dell'app in Visual Studio in base a tali modifiche.

* In Visual Studio, nella barra degli strumenti di progettazione app per la logica, selezionare **Aggiorna**.

  -oppure-

* In Visual Studio Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e scegliere **Aggiorna**.

![Aggiornare l'app per la logica con aggiornamenti](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Pubblicare aggiornamenti delle app per la logica

Quando si è pronti per distribuire gli aggiornamenti dell'app per la logica da Visual Studio in Azure, nella barra degli strumenti di progettazione app per la logica selezionare **pubblica**.

![Pubblicare l'app per la logica aggiornata in portale di Azure](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Eseguire manualmente l'app per la logica

È possibile attivare manualmente un'app per la logica distribuita in Azure da Visual Studio. Nella barra degli strumenti di progettazione app per la logica selezionare **Esegui trigger**.

![Eseguire manualmente il trigger per l'app per la logica](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Esaminare la cronologia di esecuzione

Per controllare lo stato e diagnosticare i problemi relativi alle esecuzioni dell'app per la logica, è possibile esaminare i dettagli, ad esempio gli input e gli output, per tali esecuzioni in Visual Studio.

1. In Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e selezionare **Apri cronologia di esecuzione**.

   ![Apri cronologia di esecuzione per l'app per la logica](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Per visualizzare i dettagli per un'esecuzione specifica, fare doppio clic su di essa. ad esempio:

   ![Visualizza informazioni sull'esecuzione specifica](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Per ordinare la tabella in base alla proprietà, selezionare l'intestazione di colonna per la proprietà.

1. Espandere i passaggi di cui si desidera esaminare gli input e gli output, ad esempio:

   ![Visualizzare gli input e gli output per ogni passaggio](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Disabilitare o abilitare l'app per la logica

Senza eliminare l'app per la logica, è possibile arrestare l'attivazione del trigger la volta successiva in cui viene soddisfatta la condizione di trigger. La disabilitazione dell'app per la logica impedisce al motore di App per la logica di creare ed eseguire istanze di flusso di lavoro future per l'app per la logica. In Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e scegliere **Disabilita**.

![Disabilitare l'app per la logica in Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Quando si disabilita un'app per la logica, non viene eseguita alcuna nuova istanza di esecuzione. Tutte le esecuzioni in corso e in sospeso continuano fino a quando non vengono completate, il che potrebbe richiedere tempo.

Per riattivare l'app per la logica, in Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e selezionare **Abilita**.

![Abilitare l'app per la logica in Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Eliminare l'app per la logica

Per eliminare l'app per la logica dal portale di Azure, in Cloud Explorer aprire il menu di scelta rapida dell'app per la logica e scegliere **Elimina**.

![Eliminare l'app per la logica da portale di Azure](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Quando si elimina un'app per la logica, non viene eseguita alcuna nuova istanza di esecuzione. Tutte le esecuzioni in corso e in sospeso vengono annullate. Se si dispone di migliaia di esecuzioni, l'annullamento potrebbe richiedere molto tempo. 

## <a name="troubleshooting"></a>risoluzione dei problemi

Quando si apre il progetto dell'app per la logica in Progettazione app per la logica, l'opzione per la selezione della sottoscrizione di Azure potrebbe non essere presente. L'app per la logica viene invece aperta con una sottoscrizione di Azure diversa da quella che si vuole usare. Questo comportamento si verifica perché, dopo aver aperto il file JSON di un'app per la logica, Visual Studio memorizza nella cache la prima sottoscrizione selezionata per un uso futuro. Per risolvere il problema, provare a eseguire uno di questi passaggi:

* Rinominare il file JSON dell'app per la logica. La cache della sottoscrizione dipende dal nome del file.

* Per rimuovere le sottoscrizioni selezionate in precedenza per *tutte* le app per la logica nella soluzione, eliminare la cartella di impostazioni di Visual Studio nascosta (. vs) nella directory della soluzione. In questa posizione sono archiviate le informazioni sulla sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come gestire le app per la logica distribuite con Visual Studio. A questo punto, è necessario imparare come personalizzare le definizioni di app per la logica per la distribuzione:

> [!div class="nextstepaction"]
> [Creare definizioni di app per la logica in JSON](../logic-apps/logic-apps-author-definitions.md)
