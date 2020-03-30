---
title: Creare o gestire account di integrazione B2B
description: Creare, collegare e gestire gli account di integrazione per l'integrazione aziendale con le app per la logica di AzureCreate, link, and manage integration accounts for enterprise integration with Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270329"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Creare e gestire gli account di integrazione per le integrazioni aziendali B2B nelle app per la logica di AzureCreate and manage integration accounts for B2B enterprise integrations in Azure Logic Apps

Prima di poter creare [soluzioni di integrazione aziendale e B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) tramite [App per la logica di Azure](../logic-apps/logic-apps-overview.md) è necessario creare un account di integrazione, ovvero una risorsa di Azure separata che fornisca un contenitore sicuro, scalabile e gestibile per gli artefatti di integrazione definiti e usati con i flussi di lavoro di app per la logica.

Ad esempio, è possibile creare, archiviare e gestire elementi B2B, ad esempio partner commerciali, accordi, mappe, schemi, certificati e configurazioni batch. Inoltre, prima che l'app per la logica possa usare questi elementi e usare i connettori B2B delle app per la logica, è necessario collegare l'account di [integrazione](#link-account) all'app per la logica. Sia l'account di integrazione che l'app per la logica devono esistere nella stessa posizione o *nello stesso'area.*

> [!TIP]
> Per creare un account di integrazione all'interno di un ambiente del servizio di [integrazione,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)vedere Creare account di [integrazione in ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

In questo argomento viene illustrato come eseguire queste attività:This topic shows you how to perform these tasks:

* Creare l'account di integrazione.
* Collegare l'account di integrazione a un'app per la logica.
* Modificare il piano tariffario per l'account di integrazione.
* Scollegare l'account di integrazione da un'app per la logica.
* Spostare l'account di integrazione in un gruppo di risorse o in una sottoscrizione di Azure differente.
* Eliminare l'account di integrazione.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Creare un account di integrazione

Per questa attività, è possibile usare il portale di Azure seguendo i passaggi descritti in questa sezione, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)o [l'interfaccia della riga](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create)di comando di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nel menu principale di Azure scegliere **Crea una risorsa**. Nella casella di ricerca immettere "account di integrazione" come filtro e selezionare **Account di integrazione**.

   ![Creare un nuovo account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. In **Account di integrazione**selezionare **Crea**.

   ![Scegliere "Aggiungi" per creare un account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Fornire queste informazioni sull'account di integrazione:Provide this information about your integration account:

   ![Fornire i dettagli dell'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Nome** | Sì | <*integration-account-name*> | Il nome dell'account di integrazione, che può`-`contenere solo`_`lettere, numeri, `)`trattini`.`( ), caratteri di sottolineatura ( ), parentesi (`(`, ) e punti ( ). In questo esempio viene utilizzato "Fabrikam-Integration". |
   | **Sottoscrizione** | Sì | <*Nome sottoscrizione di AzureAzure-subscription-name*> | Nome della sottoscrizione di Azure |
   | **Gruppo di risorse** | Sì | <*Nome-gruppo-risorse di AzureAzure-resource-group-name*> | Nome del [gruppo](../azure-resource-manager/management/overview.md) di risorse di Azure da usare per l'organizzazione delle risorse correlate. Per questo esempio, creare un nuovo gruppo di risorse con il nome "FabrikamIntegration-RG". |
   | **Livello di determinazione dei prezzi** | Sì | <*livello di prezzo*> | Il piano tariffario per l'account di integrazione, che è possibile modificare in un secondo momento. Per questo esempio, selezionare **Gratuito**. Per altre informazioni, vedere gli argomenti seguenti: <p>- [Modello di determinazione dei prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Limiti e configurazione delle app per la logica](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Prezzi delle app per la logica](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Percorso** | Sì | <*Area di AzureAzure-region*> | Area geografica in cui archiviare i metadati dell'account di integrazione. Selezionare lo stesso percorso dell'app per la logica oppure creare le app per la logica nello stesso percorso dell'account di integrazione. Per questo esempio, utilizzare "West US". <p>**Nota:** per creare un account di integrazione all'interno di un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)selezionare ISE come posizione. Per ulteriori informazioni, consultate Creare account di [integrazione in ISE.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment) |
   | **Analisi dei log** | No | Disattivato, Attivato | Mantenere l'impostazione **Off** per questo esempio. |
   |||||

1. Al termine, selezionare **Crea**.

   Al termine della distribuzione, Azure apre l'account di integrazione.

   ![Azure apre l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Prima che l'app per la logica possa usare l'account di integrazione, seguire i passaggi successivi per collegare l'account di integrazione e l'app per la logica.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Collegare all'app per la logica

Per concedere alle app per la logica l'accesso a un account di integrazione che contiene gli elementi B2B, è innanzitutto necessario collegare l'account di integrazione all'app per la logica. Sia l'app per la logica che l'account di integrazione devono esistere nella stessa area. Per completare questa attività, è possibile usare il portale di Azure.To complete this task, you can use the Azure portal. Se si usa Visual Studio e l'app per la logica si trova in un progetto Gruppo di risorse di [Azure,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)è possibile [collegare l'app per la logica a un account di integrazione tramite Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Nel portale di Azure trovare e aprire l'app per la logica.

1. Nel [portale di Azure](https://portal.azure.com)aprire un'app per la logica esistente o crearne una nuova.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**. In Account di **integrazione**aprire l'elenco Selezionare un account di **integrazione.** Selezionare l'account di integrazione da collegare all'app per la logica.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Per completare il collegamento, selezionare **Salva**.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Dopo aver collegato correttamente l'account di integrazione, Azure visualizza un messaggio di conferma.

   ![Azure conferma l'esito positivo del collegamento](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Ora l'app per la logica può usare gli elementi nell'account di integrazione più i connettori B2B, ad esempio la convalida XML e la codifica o decodifica dei file flat.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Modificare il piano tariffario

Per aumentare i [limiti](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) per un account di integrazione, è possibile [eseguire l'aggiornamento a un piano tariffario superiore,](#upgrade-pricing-tier)se disponibile. Ad esempio, è possibile eseguire l'aggiornamento dal livello Gratuito al livello Basic o Standard.For example, you can upgrade from the Free tier to the Basic tier or Standard tier. È anche possibile [eseguire il downgrade a un livello inferiore,](#downgrade-pricing-tier)se disponibile. Per altre informazioni sui prezzi, vedere gli argomenti seguenti:For more information pricing information, see these topics:

* [Prezzi delle app per la logica](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Piano tariffario per l'aggiornamento

Per apportare questa modifica, è possibile usare il portale di Azure seguendo i passaggi descritti in questa sezione o l'interfaccia della riga di comando di Azure.To make this change, you can use either the Azure portal by following the steps in this section or the [Azure CLI.](#upgrade-tier-azure-cli)

#### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca principale di Azure immettere "account di integrazione" come filtro e selezionare Account di **integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure mostra tutti gli account di integrazione nelle sottoscrizioni di Azure.Azure shows all the integration accounts in your Azure subscriptions.

1. In **Account di integrazione** selezionare l'account di integrazione che si vuole spostare. Nel menu dell'account di integrazione selezionare **Panoramica**.

   ![Nel menu dell'account di integrazione, seleziona "Panoramica"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Nel riquadro Panoramica selezionare **Aggiorna piano tariffario**, in cui sono elencati tutti i livelli superiori disponibili. Quando si seleziona un livello, la modifica diventa immediatamente effettiva.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

1. Se non è già stato fatto, [installare i prerequisiti dell'interfaccia della](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)riga di comando di Azure.

1. Nel portale di Azure aprire l'ambiente Azure [**Cloud Shell.In**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) the Azure portal, open the Azure Cloud Shell environment.

   ![Aprire Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Al prompt dei comandi immettere il `skuName` comando az [ **resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)e impostare il livello superiore desiderato.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Ad esempio, se si dispone del `skuName` livello `Standard`Basic, è possibile impostare su :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Livello tariffario downgrade

Per apportare questa modifica, usare l'interfaccia della riga di comando di [Azure.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. Se non è già stato fatto, [installare i prerequisiti dell'interfaccia della](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)riga di comando di Azure.

1. Nel portale di Azure aprire l'ambiente Azure [**Cloud Shell.In**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) the Azure portal, open the Azure Cloud Shell environment.

   ![Aprire Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Al prompt dei comandi, immettere `skuName` il comando az [ **resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) e impostare il livello inferiore desiderato.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Ad esempio, se si dispone del `skuName` livello `Basic`Standard, è possibile impostare su :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Scollegare da un'app per la logica

Se si vuole collegare l'app per la logica a un altro account di integrazione o non si usa più un account di integrazione con l'app per la logica, eliminare il collegamento usando Esplora risorse di Azure.If you want to link your logic app to another integration account, or no longer use an integration account with your logic app, delete the link by using Azure Resource Explorer.

1. Aprire la finestra del browser e passare a [Esplora risorse di Azure (https://resources.azure.com)](https://resources.azure.com). Accedere con le stesse credenziali dell'account Azure.Sign in with the same Azure account credentials.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Nella casella di ricerca immettere il nome dell'app per la logica in modo da poter trovare e selezionare l'app per la logica.

   ![Trovare e selezionare l'app per la logica](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Nella barra del titolo di Explorer selezionare **Lettura/Scrittura**.

   ![Attivare la modalità "Lettura/Scrittura"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Nella scheda **Dati** selezionare **Modifica**.

   ![Nella scheda "Dati", seleziona "Modifica"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Nell'editor, trovare `integrationAccount` l'oggetto ed eliminare tale proprietà, che ha questo formato:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Ad esempio:

   ![Trovare l'oggetto "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Nella scheda **Dati** selezionare **Put** per salvare le modifiche.

   ![Per salvare le modifiche, selezionare "Put"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Nel portale di Azure trovare e selezionare l'app per la logica. In **Impostazioni flusso di lavoro**dell'app verificare che la proprietà Account di **integrazione** sia vuota.

   ![Verificare che l'account di integrazione non sia collegato](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Spostare l'account di integrazione

È possibile spostare l'account di integrazione in un altro gruppo di risorse di Azure o in una sottoscrizione di Azure.You can move your integration account to another Azure resource group or Azure subscription. Quando si spostano le risorse, Azure crea nuovi ID di risorsa, quindi assicurati di usare i nuovi ID e di aggiornare eventuali script o strumenti associati alle risorse spostate. Se si desidera modificare la sottoscrizione, è necessario specificare anche un gruppo di risorse nuovo o esistente.

Per questa attività, è possibile usare il portale di Azure seguendo i passaggi descritti in questa sezione o l'interfaccia della riga di comando di [Azure.For](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move)this task, you can use either the Azure portal by following the steps in this section or the Azure CLI.

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca principale di Azure immettere "account di integrazione" come filtro e selezionare Account di **integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure mostra tutti gli account di integrazione nelle sottoscrizioni di Azure.Azure shows all the integration accounts in your Azure subscriptions.

1. In **Account di integrazione** selezionare l'account di integrazione che si vuole spostare. Nel menu dell'account di integrazione selezionare **Panoramica**.

   ![Nel menu dell'account di integrazione, seleziona "Panoramica"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Accanto a **Gruppo di risorse** o Nome **sottoscrizione**selezionare **modifica**.

   ![Modificare il gruppo di risorse o la sottoscrizioneChange resource group or subscription](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Selezionare le risorse correlate che si desidera spostare.

1. In base alla selezione effettuata, attenersi alla seguente procedura per modificare il gruppo di risorse o la sottoscrizione:Based on your selection, follow these steps to change the resource group or subscription:

   * Gruppo di risorse: nell'elenco **Gruppo di** risorse selezionare il gruppo di risorse di destinazione. In alternativa, per creare un gruppo di risorse diverso, selezionare **Crea un nuovo gruppo di risorse.**

   * Sottoscrizione: nell'elenco **Sottoscrizione** selezionare la sottoscrizione di destinazione. Nell'elenco **Gruppo di** risorse selezionare il gruppo di risorse di destinazione. In alternativa, per creare un gruppo di risorse diverso, selezionare **Crea un nuovo gruppo di risorse.**

1. Per riconoscere la comprensione che gli script o gli strumenti associati alle risorse spostate non funzioneranno fino a quando non vengono aggiornati con i nuovi ID risorsa, selezionare la casella di conferma e quindi **scegliere OK**.

1. Al termine, assicurarsi di aggiornare tutti gli script con i nuovi ID di risorsa per le risorse spostate.  

## <a name="delete-integration-account"></a>Eliminare l'account di integrazione

Per questa attività, è possibile usare il portale di Azure seguendo i passaggi descritti in questa sezione, [dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)di Azure o di Azure PowerShell.For this task, you can use either the Azure portal by following the steps in this section, Azure CLI, or Azure [PowerShell.](/powershell/module/az.logicapp/remove-azintegrationaccount)

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca principale di Azure immettere "account di integrazione" come filtro e selezionare Account di **integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure mostra tutti gli account di integrazione nelle sottoscrizioni di Azure.Azure shows all the integration accounts in your Azure subscriptions.

1. In **Account di integrazione** selezionare l'account di integrazione che si vuole eliminare. Nel menu dell'account di integrazione selezionare **Panoramica**.

   ![Nel menu dell'account di integrazione, seleziona "Panoramica"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Nel riquadro Panoramica selezionare **Elimina**.

   ![Nel riquadro "Panoramica", selezionare "Elimina"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Per confermare l'eliminazione dell'account di integrazione, selezionare **Sì**.

   ![Per confermare l'eliminazione, selezionare "Sì"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Passaggi successivi

* [Crea partner commerciali nel tuo account di integrazione](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Creare accordi tra partner nell'account di integrazione](../logic-apps/logic-apps-enterprise-integration-agreements.md)
