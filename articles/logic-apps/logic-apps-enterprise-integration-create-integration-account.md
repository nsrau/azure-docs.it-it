---
title: Creare e gestire account di integrazione per soluzioni B2B - App per la logica di Azure | Microsoft Docs
description: Creare, collegare, spostare ed eliminare gli account di integrazione per soluzioni B2B e di integrazione aziendale con App per la logica di Azure
services: logic-apps
documentationcenter: ''
author: ecfan
manager: cfowler
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: e661920974c2b0d28200d4c3d82bd644a7a55395
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166240"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Creare e gestire account di integrazione per soluzioni B2B con le app per la logica

Prima di poter compilare [soluzioni B2B e di integrazione aziendale](../logic-apps/logic-apps-enterprise-integration-overview.md) con [App per la logica di Azure](../logic-apps/logic-apps-overview.md), è necessario innanzitutto un account di integrazione in cui creare, archiviare e gestire elementi B2B, ad esempio partner commerciali, contratti, mappe, schemi, certificati e così via. Prima che l'app per la logica possa sfruttare gli elementi nell'account di integrazione e usare i connettori B2B di App per la logica, ad esempio la convalida XML, è necessario [collegare l'account di integrazione](#link-account) all'app per la logica. Per eseguire il collegamento, è necessario che l'account di integrazione e l'app per la logica si trovino nella *stessa* posizione o area di Azure.

Questo articolo descrive come eseguire le attività seguenti:

* Creare l'account di integrazione.
* Collegare l'account di integrazione a un'app per la logica.
* Spostare l'account di integrazione in un gruppo di risorse o in una sottoscrizione di Azure differente.
* Eliminare l'account di integrazione.

Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> con le credenziali dell'account Azure.

## <a name="create-integration-account"></a>Creare un account di integrazione

1. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "account di integrazione" come filtro e quindi selezionare **Account di integrazione**.

   ![Trovare gli account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. In **Account di integrazione** scegliere **Aggiungi**.

   ![Scegliere "Aggiungi" per creare un account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Fornire le informazioni relative all'account di integrazione: 

   ![Immettere i dettagli dell'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Proprietà | Obbligatoria | Valore di esempio | Descrizione | 
   |----------|----------|---------------|-------------|
   | Name | Sì | test-integration-account | Nome dell'account di integrazione. Per questo esempio, usare il nome specificato. | 
   | Subscription | Sì | <*nome sottoscrizione di Azure*> | Nome della sottoscrizione di Azure da usare. | 
   | Resource group | Sì | test-integration-account-rg | Nome del [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) usato per organizzare le risorse correlate. Per questo esempio, creare un nuovo gruppo di risorse con il nome specificato. | 
   | Pricing tier | Sì | Gratuito | Piano tariffario che si vuole usare. Per questo esempio, selezionare **Gratuito**, ma per altre informazioni, vedere [Limiti e configurazione di App per la logica](../logic-apps/logic-apps-limits-and-config.md) e [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Location | Sì | Stati Uniti occidentali | Area in cui archiviare le informazioni sull'account di integrazione. Selezionare la stessa posizione dell'app per la logica oppure creare un'app per la logica nella stessa posizione dell'account di integrazione. Per questo esempio: | 
   | Log Analytics | No | Off | Lasciare l'impostazione **No** per la registrazione diagnostica. | 
   ||||| 

4. Selezionare quindi **Aggiungi al dashboard** e scegliere **Crea**.

   Dopo la distribuzione nella posizione selezionata (operazione che in genere si completa in un minuto), Azure apre automaticamente l'account di integrazione.

   ![Azure apre automaticamente l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

A questo punto, prima che l'app per la logica possa usare l'account di integrazione, è necessario collegare l'account di integrazione all'app per la logica.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Collegare all'app per la logica

Per consentire alle app per la logica di accedere a un account di integrazione che contiene elementi B2B, quali partner commerciali, contratti, mappe e schemi, è necessario collegare l'account di integrazione all'app per la logica. 

> [!NOTE]
> L'account di integrazione e l'app per la logica devono trovarsi nella stessa posizione di Azure.

1. Nel portale di Azure trovare e aprire l'app per la logica.

2. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**. Nell'elenco **Selezionare un account di integrazione** selezionare l'account di integrazione da collegare all'app per la logica.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Per completare il collegamento, selezionare **Salva**.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Quando l'account di integrazione è collegato correttamente, Azure visualizza un messaggio di conferma. 

   ![Azure conferma l'esito positivo del collegamento](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

A questo punto, l'app per la logica può usare tutti gli elementi nell'account di integrazione oltre ai connettori B2B, ad esempio la convalida XML e la codifica o decodifica file flat.  

## <a name="unlink-from-logic-app"></a>Scollegare da un'app per la logica

Per collegare l'app per la logica a un altro account di integrazione o smettere di usare un account di integrazione con l'app per la logica, è possibile eliminare il collegamento tramite Azure Resource Explorer.

1. Nel browser passare ad <a href="https://resources.azure.com" target="_blank">Azure Resource Explorer (https://resources.azure.com)</a>. Assicurarsi di aver eseguito l'accesso con le stesse credenziali di Azure.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. Nella casella di ricerca immettere il nome dell'app per la logica, quindi trovare e selezionare l'app per la logica specificata.

   ![Trovare e selezionare l'app per la logica](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. Nella barra del titolo di Resource Explorer scegliere **Lettura/Scrittura**.

   ![Attivare la modalità "Lettura/Scrittura"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. Nella scheda **Dati** scegliere **Modifica**.

   ![Nella scheda "Dati" scegliere "Modifica"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. Nell'editor trovare la proprietà `integrationAccount` per l'account di integrazione ed eliminare tale proprietà, che ha il formato seguente:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Ad esempio:

   ![Trovare la definizione della proprietà "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. Nella scheda **Dati** scegliere **Put** per salvare le modifiche. 

   ![Scegliere "Put" per salvare le modifiche](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. Nel portale di Azure, in **Impostazioni del flusso di lavoro** dell'app per la logica, verificare che la proprietà **Account di integrazione** venga visualizzata vuota.

   ![Verificare che l'account di integrazione non sia collegato](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Spostare l'account di integrazione

È possibile spostare l'account di integrazione in un gruppo di risorse o in una sottoscrizione di Azure differente.

1. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "account di integrazione" come filtro e quindi selezionare **Account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. In **Account di integrazione** selezionare l'account di integrazione che si vuole spostare. Nel menu dell'account di integrazione, in **Impostazioni**, scegliere **Proprietà**.

   ![In "Impostazioni" scegliere "Proprietà"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Modificare il gruppo di risorse o la sottoscrizione di Azure per l'account di integrazione.

   ![Scegliere "Cambia il gruppo di risorse" o "Cambia sottoscrizione"](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Al termine, assicurarsi di aver aggiornato tutti gli script con i nuovi ID di risorsa per gli elementi.  

## <a name="delete-integration-account"></a>Eliminare l'account di integrazione

1. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "account di integrazione" come filtro e quindi selezionare **Account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. In **Account di integrazione** selezionare l'account di integrazione che si vuole eliminare. Nel menu dell'account di integrazione scegliere **Informazioni generali** e quindi scegliere **Elimina**. 

   ![Selezionare l'account di integrazione. Nella pagina "Informazioni generali" scegliere "Elimina"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Per confermare che si vuole eliminare l'account di integrazione, scegliere **Sì**.

   ![Per confermare l'eliminazione, scegliere "Sì"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Creare contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md)