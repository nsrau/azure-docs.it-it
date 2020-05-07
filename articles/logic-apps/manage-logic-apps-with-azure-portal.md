---
title: Gestire app per la logica nella portale di Azure
description: Modificare, abilitare, disabilitare o eliminare app per la logica usando il portale di Azure.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: d80972cd200b8f85e14d316c4c06a38f88ac81b5
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598165"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Gestire app per la logica nella portale di Azure

È possibile gestire le app per la logica usando il [portale di Azure](https://portal.azure.com) o [Visual Studio](manage-logic-apps-with-visual-studio.md). Questo articolo illustra come modificare, disabilitare, abilitare o eliminare app per la logica nella portale di Azure. Se non si ha familiarità con app per la logica di Azure, vedere informazioni sulle [app per la logica di Azure](logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un'app per la logica esistente. Per informazioni su come creare un'app per la logica nella portale di Azure, vedere [Guida introduttiva: creare il primo flusso di lavoro usando app per la logica di Azure-portale di Azure](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Trovare le app per la logica

Per trovare e aprire l'app per la logica, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

1. Nella barra di ricerca di Azure immettere `logic apps`e selezionare app per la **logica**.

   ![Trovare e selezionare "App per la logica"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Nella pagina **app** per la logica trovare e selezionare l'app per la logica che si vuole gestire.

   Dopo aver aperto il riquadro della **Panoramica** dell'app per la logica, è possibile filtrare l'elenco visualizzato nella pagina app per la **logica** nei modi seguenti:

   * Cerca app per la logica in base al nome
   * Filtrare le app per la logica per sottoscrizione, gruppo di risorse, posizione e tag
   * Raggruppare app per la logica per gruppo di risorse, tipo, sottoscrizione e località

## <a name="view-logic-app-properties"></a>Visualizzare le proprietà dell'app per la logica

1. Nella portale di Azure [individuare e aprire l'app per la logica](#find-logic-app).

1. Dal menu dell'app per la logica, in **Impostazioni**, selezionare **Proprietà**.

1. Nel riquadro **Proprietà** è possibile visualizzare e copiare le informazioni seguenti sull'app per la logica:

   * **Nome**
   * **ID risorsa**
   * **Gruppo di risorse**
   * **Posizione**
   * **Tipo** 
   * **Nome sottoscrizione**
   * **ID sottoscrizione**
   * **Endpoint di accesso**
   * **Indirizzi IP in uscita Runtime**
   * **Indirizzi IP degli endpoint di accesso**
   * **Indirizzi IP in uscita del connettore**

## <a name="disable-or-enable-logic-apps"></a>Disabilitare o abilitare app per la logica

È possibile abilitare o disabilitare una [singola app](#disable-enable-single-logic-app) [per la logica o più app per la logica nello stesso momento](#disable-or-enable-multiple-logic-apps) nel portale di Azure. È anche possibile [abilitare o disabilitare app per la logica in Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

La disabilitazione dell'app per la logica influiscono sulle istanze del flusso di lavoro e viene eseguita nei modi seguenti:

* Tutte le esecuzioni in corso e in sospeso continuano fino a quando non vengono completate. A seconda del numero di queste esecuzioni, questo processo potrebbe richiedere del tempo.

* Il motore delle app per la logica non creerà né eseguirà nuove istanze del flusso di lavoro.

* Il trigger non viene attivato la volta successiva che vengono soddisfatte le condizioni.

* Lo stato del trigger memorizza il punto in cui l'app per la logica è stata arrestata. Quindi, se si Abilita nuovamente l'app per la logica, il trigger viene attivato per tutti gli elementi non elaborati dall'ultima esecuzione.

  Per arrestare l'attivazione dell'app per la logica su elementi non elaborati dopo l'ultima esecuzione, cancellare lo stato del trigger prima di riabilitare l'app per la logica:

  1. Nella portale di Azure [individuare e aprire l'app per la logica](#find-logic-app).

  1. Modificare qualsiasi parte del trigger dell'app per la logica.

  1. Salvare le modifiche. Questo passaggio Reimposta lo stato corrente del trigger.

  1. [Abilitare di nuovo l'app per la logica](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Disabilitare o abilitare l'app per la logica singola

1. Nella portale di Azure [individuare e aprire l'app per la logica](#find-logic-app).

1. Scegliere **Panoramica**dal menu dell'app per la logica. Scegliere una delle opzioni seguenti:

   * Sulla barra degli strumenti selezionare **Disabilita**.

     ![Disabilitare l'app per la logica singola in portale di Azure](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Se l'app per la logica è già disabilitata, viene visualizzata solo l'opzione **Abilita** .

   * Sulla barra degli strumenti selezionare **Abilita**.

     ![Abilitare l'app per la logica singola in portale di Azure](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Se l'app per la logica è già abilitata, viene visualizzata solo l'opzione **Disable (Disabilita** ). 

   Il portale di Azure Visualizza una notifica sulla barra degli strumenti di Azure principale che conferma se l'operazione ha avuto esito positivo o negativo.

   ![Notifica per confermare lo stato dell'operazione](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Disabilitare o abilitare più app per la logica

1. Nella portale di Azure [individuare le app per la logica](#find-logic-app) che si desidera disabilitare o abilitare.

1. Per verificare se un'app per la logica è attualmente abilitata o disabilitata, nella pagina **app** per la logica esaminare la colonna **stato** per l'app per la logica. 

   ![Colonna stato app per la logica](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Se la colonna **stato** non è visibile, sulla barra degli strumenti app per la **logica** Selezionare **prova anteprima**.

   ![Attiva anteprima](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. Nella colonna CheckBox selezionare le app per la logica che si desidera disabilitare o abilitare. Sulla barra degli strumenti selezionare **Disabilita** o **Abilita**.

   ![Abilitare o disabilitare più app per la logica nella portale di Azure](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Quando viene visualizzata la casella di conferma, selezionare **Sì** per continuare.

   Il portale di Azure Visualizza una notifica sulla barra degli strumenti di Azure principale che conferma se l'operazione ha avuto esito positivo o negativo.

## <a name="delete-logic-apps"></a>Elimina app per la logica

È possibile [eliminare una singola app](#delete-single-logic-app) per la logica o [eliminare più app per la logica nello stesso momento](#delete-multiple-logic-apps) nel portale di Azure. È anche possibile [eliminare l'app per la logica in Visual Studio](manage-logic-apps-with-visual-studio.md#delete-your-logic-app).

L'eliminazione dell'app per la logica influiscono sulle istanze del flusso di lavoro nei modi seguenti:

* Tutte le esecuzioni in corso e in sospeso continuano fino a quando non vengono completate. A seconda del numero di queste esecuzioni, questo processo potrebbe richiedere del tempo.

* Il motore delle app per la logica non creerà né eseguirà nuove istanze del flusso di lavoro.

> [!NOTE]
> Se si elimina e si ricrea un'app per la logica figlio, è necessario salvare di nuovo l'app per la logica padre. L'app figlio ricreata avrà metadati diversi.
> Se non si salva di nuovo l'app per la logica padre dopo aver ricreato l'elemento figlio, le chiamate all'app per la logica figlio avranno esito negativo con un errore di "non autorizzato". Questo comportamento si applica alle app per la logica padre-figlio, ad esempio quelle che usano gli artefatti negli account di integrazione o chiamano funzioni di Azure.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Elimina singola app per la logica

1. Nella portale di Azure [individuare e aprire l'app per la logica](#find-logic-app).

1. Scegliere **Panoramica**dal menu dell'app per la logica. Sulla barra degli strumenti dell'app per la logica selezionare **Elimina**.

   ![Nella barra degli strumenti dell'app per la logica selezionare "Elimina"](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Quando viene visualizzata la casella di conferma, immettere il nome dell'app per la logica e selezionare **Elimina**.

   ![Confermare l'eliminazione dell'app per la logica](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Il portale di Azure Visualizza una notifica sulla barra degli strumenti di Azure principale che conferma se l'operazione ha avuto esito positivo o negativo.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Eliminare più app per la logica

1. Nella portale di Azure [individuare le app per la logica che si desidera eliminare](#find-logic-app).

1. Nella colonna CheckBox selezionare le app per la logica che si desidera eliminare. Sulla barra degli strumenti selezionare **Elimina**.

   ![Eliminare più app per la logica](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Quando viene visualizzata la casella di conferma `yes`, immettere e selezionare **Elimina**.

   ![Confermare l'eliminazione delle app per la logica](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Il portale di Azure Visualizza una notifica sulla barra degli strumenti di Azure principale che conferma se l'operazione ha avuto esito positivo o negativo.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Gestire le versioni delle app per la logica

È possibile usare la portale di Azure per il controllo della versione delle app per la logica. È possibile trovare la cronologia delle versioni dell'app per la logica e innalzare di livello le versioni precedenti.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Trovare e visualizzare le versioni precedenti

1. Nella portale di Azure [individuare l'app per la logica che si vuole gestire](#find-logic-app).

1. Nel menu dell'app per la logica, in **strumenti di sviluppo**selezionare **versioni**.

   ![Nel menu dell'app per la logica selezionare "versioni" in "strumenti di sviluppo".](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Consente di selezionare la **versione** dell'app per la logica da gestire dall'elenco. È possibile immettere l'identificatore di **versione** nella barra di ricerca per filtrare l'elenco.

1. Nella pagina Cronologia **versione** , verranno visualizzati i dettagli della versione precedente in modalità di sola lettura. È possibile scegliere tra le modalità **progettazione** app per la logica e **visualizzazione codice** .

   ![Pagina Cronologia versione per app per la logica con visualizzazione codice e visualizzazione progettazione app per la logica](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Alza di livello le versioni precedenti

1. Nella cronologia delle versioni dell'app per la logica [trovare e selezionare la versione che si vuole alzare di](#find-version-history)livello.

1. Nella pagina Cronologia **versione** selezionare **alza**di livello.

   ![Pulsante Alza di livello nella cronologia delle versioni dell'app per la logica](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. Nella pagina **progettazione app** per la logica visualizzata modificare la versione che si sta innalzando di livello in base alle esigenze. È possibile passare dalla modalità **progettazione** a quella **visualizzazione codice** e viceversa. È anche possibile aggiornare **parametri**, **modelli**e **connettori**.

   ![Pagina progettazione app per la logica per la promozione di una versione precedente](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Per salvare gli aggiornamenti e completare la promozione della versione precedente, selezionare **Salva**. In alternativa, per annullare le modifiche, selezionare **Ignora**. 

   Quando si [Visualizza di](#find-version-history) nuovo la cronologia delle versioni dell'app per la logica, la versione innalzata di livello viene visualizzata nella parte superiore dell'elenco e ha un nuovo identificatore.

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare le app per la logica](monitor-logic-apps.md)
