---
title: Gestire le app per la logica nel portale di AzureManage logic apps in the Azure portal
description: Modificare, abilitare, disabilitare o eliminare app per la logica tramite il portale di Azure.Edit, enable, disable, or delete logic apps by using the Azure portal.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415940"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Gestire le app per la logica nel portale di AzureManage logic apps in the Azure portal

È possibile gestire le app per la logica usando il [portale di Azure](https://portal.azure.com) o Visual [Studio.](manage-logic-apps-with-visual-studio.md) Questo articolo illustra come modificare, disabilitare, abilitare o eliminare app per la logica nel portale di Azure.This article shows how to edit, disable, enable, or delete logic apps in the Azure portal. Se non si ha familiarità con le app per la logica di Azure, vedere [Che cos'è App per la logica](logic-apps-overview.md)di Azure?

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un'app per la logica esistente. Per informazioni su come creare un'app per la logica nel portale di Azure, vedere Guida introduttiva: Creare il primo flusso di lavoro usando App per la logica di Azure - Portale di Azure.To learn how to create a logic app in the Azure portal, [see Quickstart: Create your first workflow by using Azure Logic Apps - Azure portal.](quickstart-create-first-logic-app-workflow.md)

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Trovare le app per la logica

Per trovare e aprire l'app per la logica, attenersi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

1. Nella barra di ricerca `logic apps`di Azure immettere e selezionare **App per la logica**.

   ![Trovare e selezionare "App per la logica"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Nella pagina **App per** la logica individuare e selezionare l'app per la logica che si vuole gestire.

   Dopo l'apertura del riquadro **Panoramica** dell'app per la logica, è possibile filtrare l'elenco visualizzato nella pagina App per la logica nei modi seguenti:After the logic app's Overview pane opens, you can filter the list that appears on the **Logic Apps** page in these ways:

   * Cercare app per la logica in base al nomeSearch for logic apps by name
   * Filtrare le app per la logica per sottoscrizione, gruppo di risorse, posizione e tagFilter logic apps by subscription, resource group, location, and tags
   * Raggruppare le app per la logica per gruppo di risorse, tipo, sottoscrizione e posizione

## <a name="view-logic-app-properties"></a>Visualizzare le proprietà dell'app per la logicaView logic app properties

1. Nel portale di Azure [individuare e aprire l'app per la logica.](#find-logic-app)

1. Dal menu dell'app per la logica, in **Impostazioni,** selezionare **Proprietà**.

1. Nel riquadro **Proprietà** è possibile visualizzare e copiare le informazioni seguenti sull'app per la logica:

   * **Nome**
   * **ID risorsa**
   * **Gruppo di risorse**
   * **Posizione**
   * **Tipo** 
   * **Nome sottoscrizione**
   * **ID sottoscrizione**
   * **Endpoint di accesso**
   * **Indirizzi IP in uscita di runtime**
   * **Indirizzi IP dell'endpoint di accesso**
   * **Indirizzi IP in uscita del connettore**

## <a name="disable-or-enable-logic-apps"></a>Disabilitare o abilitare le app per la logica

È possibile abilitare o disabilitare una singola app per la logica o [più app per la logica contemporaneamente](#disable-or-enable-multiple-logic-apps) nel portale di Azure.You can enable or disable a single [logic app](#disable-enable-single-logic-app) or multiple logic apps at the same time in the Azure portal. È inoltre possibile [abilitare o disabilitare le app per la logica in Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

La disabilitazione dell'app per la logica influisce sulle istanze del flusso di lavoro e viene eseguita nei modi seguenti:Disabling your logic app affects your workflow instances and runs in these ways:

* Tutte le esecuzioni in corso e in sospeso continuano fino al termine. A seconda del numero di queste esecuzioni, questo processo potrebbe richiedere del tempo.

* Il motore App per la logica non creerà o eseguirà nuove istanze del flusso di lavoro.

* Il grilletto non si attiva la prossima volta che le sue condizioni sono soddisfatte.

* Lo stato del trigger ricorda il punto in cui l'app per la logica è stata arrestata. Pertanto, se si riabilita l'app per la logica, il trigger viene attivato per tutti gli elementi non elaborati dall'ultima esecuzione.

  Per interrompere l'attivazione dell'app per la logica dopo l'ultima esecuzione, cancella lo stato del trigger prima di riattivare l'app per la logica:

  1. Nel portale di Azure [individuare e aprire l'app per la logica.](#find-logic-app)

  1. Modificare qualsiasi parte del trigger dell'app per la logica.

  1. Salvare le modifiche. Questo passaggio consente di reimpostare lo stato corrente del trigger.

  1. [Riattivare l'app per la logica.](#disable-enable-single-logic-app)

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Disabilitare o abilitare una singola app per la logica

1. Nel portale di Azure [individuare e aprire l'app per la logica.](#find-logic-app)

1. Nel menu dell'app per la logica selezionare **Panoramica**. Scegliere una delle seguenti opzioni:

   * Sulla barra degli strumenti selezionare **Disabilita**.

     ![Disabilitare una singola app per la logica nel portale di AzureDisable single logic app in Azure portal](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Se l'app per la logica è già disabilitata, viene visualizzata solo l'opzione **Abilita.**

   * Sulla barra degli strumenti selezionare **Abilita**.

     ![Abilitare una singola app per la logica nel portale di AzureEnable single logic app in Azure portal](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Se l'app per la logica è già abilitata, viene visualizzata solo l'opzione **Disabilita.** 

   Il portale di Azure mostra una notifica sulla barra degli strumenti principale di Azure che conferma se l'operazione è stata completata o meno.

   ![Notifica per confermare lo stato dell'operazione](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Disabilitare o abilitare più app per la logica

1. Nel portale di Azure [individuare le app per la logica](#find-logic-app) che si vuole disabilitare o abilitare.

1. Per verificare se un'app per la logica è attualmente abilitata o disabilitata, nella pagina **App per** la logica esaminare la colonna **Stato** per tale app per la logica. 

   ![Colonna di stato App per la logica](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Se la colonna **Stato** non è visibile, nella barra degli strumenti **App per** la logica selezionare **Prova anteprima**.

   ![Attivare l'anteprima](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. Nella colonna della casella di controllo selezionare le app per la logica che si desidera disabilitare o abilitare. Sulla barra degli strumenti selezionare **Disattiva** o **Abilita**.

   ![Abilitare o disabilitare più app per la logica nel portale di AzureEnable or disable multiple logic apps in the Azure portal](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Quando viene visualizzata la finestra di conferma, selezionare **Sì** per continuare.

   Il portale di Azure mostra una notifica sulla barra degli strumenti principale di Azure che conferma se l'operazione è stata completata o meno.

## <a name="delete-logic-apps"></a>Eliminare le app per la logica

È possibile eliminare una singola app per la logica o [più app per la logica contemporaneamente](#delete-multiple-logic-apps) nel portale di Azure.You can delete a single [logic app](#delete-single-logic-app) or delete multiple logic apps at the same time in the Azure portal. È anche possibile [eliminare l'app per la logica in Visual Studio](manage-logic-apps-with-visual-studio.md#delete-your-logic-app).

L'eliminazione dell'app per la logica influisce sulle istanze del flusso di lavoro nei modi seguenti:Deleting your logic app affects your workflow instances in these ways:

* Tutte le esecuzioni in corso e in sospeso continuano fino al termine. A seconda del numero di queste esecuzioni, questo processo potrebbe richiedere del tempo.

* Il motore App per la logica non creerà o eseguirà nuove istanze del flusso di lavoro.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Eliminare un'unica app per la logica

1. Nel portale di Azure [individuare e aprire l'app per la logica.](#find-logic-app)

1. Nel menu dell'app per la logica selezionare **Panoramica**. Nella barra degli strumenti dell'app per la logica selezionare **Elimina**.

   ![Nella barra degli strumenti dell'app per la logica, seleziona "Elimina"On logic app toolbar, select "Delete"](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Quando viene visualizzata la casella di conferma, immettere il nome dell'app per la logica e selezionare **Elimina**.

   ![Confermare l'eliminazione dell'app per la logica](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Il portale di Azure mostra una notifica sulla barra degli strumenti principale di Azure che conferma se l'operazione è stata completata o meno.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Eliminare più app per la logicaDelete multiple logic apps

1. Nel portale di Azure [individuare le app per la logica che si desidera eliminare.](#find-logic-app)

1. Nella colonna della casella di controllo selezionare le app per la logica che si desidera eliminare. Sulla barra degli strumenti selezionare **Elimina**.

   ![Eliminare più app per la logicaDelete multiple logic apps](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Quando viene visualizzata `yes`la casella di conferma, immettere e selezionare **Elimina**.

   ![Confermare l'eliminazione delle app per la logica](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Il portale di Azure mostra una notifica sulla barra degli strumenti principale di Azure che conferma se l'operazione è stata completata o meno.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Gestire le versioni dell'app per la logicaManage logic app versions

È possibile usare il portale di Azure per il controllo della versione delle app per la logica. È possibile trovare la cronologia delle versioni dell'app per la logica e promuovere le versioni precedenti.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Trovare e visualizzare le versioni precedenti

1. Nel portale di Azure [individuare l'app per la logica che si vuole gestire.](#find-logic-app)

1. Nel menu dell'app per la logica, in Strumenti di **sviluppo,** selezionare **Versioni**.

   ![Nel menu dell'app per la logica, seleziona "Versioni" in "Strumenti di sviluppo"](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Selezionare la **versione** dell'app per la logica da gestire dall'elenco. È possibile immettere l'identificatore **di versione** nella barra di ricerca per filtrare l'elenco.

1. Nella pagina **Versione cronologia,** i dettagli della versione precedente verranno visualizzati in modalità di sola lettura. È possibile scegliere tra la progettazione **di** app per la logica e le modalità di **visualizzazione Codice.**

   ![Pagina della versione della cronologia per l'app per la logica con la visualizzazione codice e la visualizzazione Progettazione app per la logica](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Promuovere le versioni precedenti

1. Nella cronologia delle versioni dell'app per la logica [individuare e selezionare la versione che si desidera promuovere.](#find-version-history)

1. Nella pagina **Versione cronologia** selezionare **Promuovi**.

   ![Pulsante Promuovi nella cronologia delle versioni dell'app per la logica](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. Nella pagina **Progettazione app** per la logica visualizzata modificare la versione che si sta promuovendo in base alle esigenze. È possibile passare dalla modalità di **visualizzazione Finestra di progettazione** alla **modalità di visualizzazione Codice** e A passata. È inoltre possibile aggiornare **parametri**, **modelli**e **connettori**.

   ![Pagina di Progettazione app per la logica per la promozione di una versione precedente](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Per salvare gli aggiornamenti e completare la promozione della versione precedente, selezionare **Salva**. (Oppure, per annullare le modifiche, selezionare **Elimina**.) 

   Quando visualizzi di nuovo la [cronologia delle versioni dell'app per](#find-version-history) la logica, la versione promossa viene visualizzata nella parte superiore dell'elenco e ha un nuovo identificatore.

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare le app per la logica](monitor-logic-apps.md)
