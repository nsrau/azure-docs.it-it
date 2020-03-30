---
title: Eseguire la migrazione di app per la logica tra sottoscrizioni, gruppi di risorse o areeMigrate logic apps across subscriptions, resource groups, or regions
description: Eseguire la migrazione di app per la logica o account di integrazione ad altre sottoscrizioni, gruppi di risorse o posizioni (aree) di AzureMigrate logic apps or integration accounts to other Azure subscriptions, resource groups, or locations (regions)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: f5944accb185f1311c811cf65a8ea8348fd569db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605615"
---
# <a name="move-logic-app-resources-to-other-azure-subscriptions-resource-groups-or-regions"></a>Spostare le risorse dell'app per la logica in altre sottoscrizioni, gruppi di risorse o aree di AzureMove logic app resources to other Azure subscriptions, resource groups, or regions

Per eseguire la migrazione dell'app per la logica o delle risorse correlate a un'altra sottoscrizione, gruppo di risorse o area di Azure, è possibile completare queste attività, ad esempio il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Prima di spostare le risorse, esaminare le considerazioni seguenti:Before you move resources, review these considerations: 

* È possibile spostare solo [tipi di risorse dell'app per la logica specifici](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) tra sottoscrizioni o gruppi di risorse di Azure.You can move only specific logic app resource types between Azure resource groups or subscriptions.

* Controllare i limiti sul numero di risorse dell'app per la logica che è possibile avere nella sottoscrizione di Azure e in ogni area di Azure.Check the [limits](../logic-apps/logic-apps-limits-and-config.md) on the number of logic app resources that you can have in your Azure subscription and in each Azure region. Questi limiti influiscono sulla possibilità di spostare tipi di risorse specifici quando l'area rimane invariata tra sottoscrizioni o gruppi di risorse. Ad esempio, è possibile avere un solo account di integrazione di livello gratuito per ogni area di Azure in ogni sottoscrizione di Azure.For example, you can have only one Free tier integration account for each Azure region in each Azure subscription.

* Dopo aver eseguito la migrazione di app per la logica tra sottoscrizioni, gruppi di risorse o aree, è necessario ricreare o autorizzare nuovamente tutte le connessioni che richiedono l'autenticazione aperta (OAuth).

* Ogni volta che si spostano le risorse, Azure crea nuovi ID di risorsa. Quindi, assicurarsi di utilizzare i nuovi ID e aggiornare eventuali script o strumenti associati alle risorse spostate.

## <a name="prerequisites"></a>Prerequisiti

* La stessa sottoscrizione di Azure usata per creare l'app per la logica o l'account di integrazione che si vuole spostare

* Autorizzazioni del proprietario della risorsa per spostare e impostare le risorse desiderate. Ulteriori informazioni sul [controllo degli accessi in base](../role-based-access-control/built-in-roles.md#owner)al ruolo .

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Spostare le risorse fra sottoscrizioni

Per spostare una risorsa, ad esempio un'app per la logica o un account di integrazione, in un'altra sottoscrizione di Azure, è possibile usare il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Questi passaggi riguardano il portale di Azure, che è possibile usare quando l'area della risorsa rimane invariata. Per altri passaggi e preparazione generale, vedere [Spostare le risorse in un nuovo gruppo](../azure-resource-manager/management/move-resource-group-and-subscription.md)di risorse o in una nuova sottoscrizione.

1. Nel [portale di Azure](https://portal.azure.com)individuare e selezionare la risorsa dell'app per la logica che si vuole spostare.

1. Nella pagina **Panoramica** della risorsa, accanto a **Sottoscrizione**, selezionare il collegamento **di modifica.**

1. Nella pagina **Sposta risorse** selezionare la risorsa dell'app per la logica e tutte le risorse correlate che si desidera spostare.

1. Nell'elenco **Sottoscrizione** selezionare la sottoscrizione di destinazione.

1. Nell'elenco **Gruppo di** risorse selezionare il gruppo di risorse di destinazione. In alternativa, per creare un gruppo di risorse diverso, selezionare **Crea un nuovo gruppo**.

1. Per confermare la comprensione che gli script o gli strumenti associati alle risorse spostate non funzioneranno fino a quando non vengono aggiornati con i nuovi ID risorsa, selezionare la casella di conferma e quindi **scegliere OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Spostare le risorse tra gruppi di risorse

Per spostare una risorsa, ad esempio un'app per la logica o un account di integrazione, in un altro gruppo di risorse di Azure, è possibile usare il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Questi passaggi riguardano il portale di Azure, che è possibile usare quando l'area della risorsa rimane invariata. Per altri passaggi e preparazione generale, vedere [Spostare le risorse in un nuovo gruppo](../azure-resource-manager/management/move-resource-group-and-subscription.md)di risorse o in una nuova sottoscrizione.

Prima di spostare effettivamente le risorse tra gruppi, è possibile verificare se è possibile spostare correttamente la risorsa in un altro gruppo. Per ulteriori informazioni, consultate [Convalidare lo spostamento.](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move)

1. Nel [portale di Azure](https://portal.azure.com)individuare e selezionare la risorsa dell'app per la logica che si vuole spostare.

1. Nella pagina **Panoramica** della risorsa, accanto a **Gruppo**di risorse, selezionare il collegamento **Di modifica.**

1. Nella pagina **Sposta risorse** selezionare la risorsa dell'app per la logica e tutte le risorse correlate che si desidera spostare.

1. Nell'elenco **Gruppo di** risorse selezionare il gruppo di risorse di destinazione. In alternativa, per creare un gruppo di risorse diverso, selezionare **Crea un nuovo gruppo**.

1. Per confermare la comprensione che gli script o gli strumenti associati alle risorse spostate non funzioneranno fino a quando non vengono aggiornati con i nuovi ID risorsa, selezionare la casella di conferma e quindi **scegliere OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Spostare le risorse da un'aree all'altro

Quando si vuole spostare un'app per la logica in un'area diversa, le opzioni dipendono dal modo in cui è stata creata l'app per la logica. In base all'opzione scelta, è necessario ricreare o autorizzare nuovamente le connessioni nell'app per la logica.

* Nel portale di Azure ricreare l'app per la logica nella nuova area e riconfigurare le impostazioni del flusso di lavoro. Per risparmiare tempo, puoi copiare la definizione del flusso di lavoro e le connessioni sottostanti dall'app di origine all'app di destinazione. Per visualizzare il "codice" dietro un'app per la logica, nella barra degli strumenti di Progettazione app per la logica selezionare **Visualizzazione Codice**.

* Usando Visual Studio e gli strumenti per le app per la logica di Azure per Visual Studio, è possibile [aprire e scaricare l'app per](../logic-apps/manage-logic-apps-with-visual-studio.md) la logica dal portale di Azure come modello di Azure Resource [Manager.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) Questo modello è per lo più pronto per la distribuzione e include le definizioni delle risorse per l'app per la logica, incluso il flusso di lavoro stesso e le connessioni. Il modello dichiara anche i parametri per i valori da utilizzare in fase di distribuzione. In questo modo, è possibile modificare più facilmente la posizione e la modalità di distribuzione dell'app per la logica, in base alle proprie esigenze. Per specificare il percorso e altre informazioni necessarie per la distribuzione, è possibile utilizzare un file di parametri separato.

* Se l'app per la logica è stata creata e distribuita usando strumenti di integrazione continua (CI) e di distribuzione continua (CD), ad esempio Pipeline di Azure in Azure DevOps, è possibile distribuire l'app in un'altra area usando tali strumenti.

Per altre informazioni sui modelli di distribuzione per le app per la logica, vedere gli argomenti seguenti:For more information about deployment templates for logic apps, see these topics:

* [Panoramica: automatizzare la distribuzione per le app per la logica di Azure usando i modelli di Azure Resource ManagerOverview: Automate deployment for Azure Logic Apps by using Azure Resource Manager templates](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Trovare, aprire e scaricare l'app per la logica dal portale di Azure in Visual StudioFind, open, and download your logic app from the Azure portal into Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Creare modelli di Azure Resource Manager per le app per la logica di AzureCreate Azure Resource Manager templates for Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Distribuire modelli di Azure Resource Manager per app per la logica di AzureDeploy Azure Resource Manager templates for Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Risorse correlate

Alcune risorse di Azure, ad esempio le risorse del gateway dati locali in Azure, possono esistere in un'area diversa dalle app per la logica che usano tali risorse. Tuttavia, altre risorse di Azure, ad esempio gli account di integrazione collegati, devono esistere nella stessa area delle app per la logica. In base allo scenario, assicurarsi che le app per la logica possano accedere alle risorse che le app prevedono di esistere nella stessa area.

Ad esempio, per collegare un'app per la logica a un account di integrazione, entrambe le risorse devono esistere nella stessa area. In scenari quali il ripristino di emergenza, in genere si desiderano account di integrazione con la stessa configurazione ed elementi. In altri scenari, potrebbero essere necessari account di integrazione con configurazioni ed elementi diversi.

I connettori personalizzati in App per la logica di Azure sono visibili agli autori e agli utenti dei connettori che hanno la stessa sottoscrizione di Azure e lo stesso tenant di Azure Active Directory.Custom connectors in Azure Logic Apps are visible to the connectors' authors and users who have the same Azure subscription and the same Azure Active Directory tenant. Questi connettori sono disponibili nella stessa area in cui vengono distribuite le app per la logica. Per altre informazioni, vedere [Share custom connectors in your organization](https://docs.microsoft.com/connectors/custom-connectors/share) (Condividere i connettori personalizzati nell'organizzazione).

Il modello ottenuto da Visual Studio include solo le definizioni di risorsa per l'app per la logica e le relative connessioni. Pertanto, se l'app per la logica usa altre risorse, ad esempio un account di integrazione e gli elementi B2B, ad esempio partner, accordi e schemi, è necessario esportare il modello dell'account di integrazione tramite il portale di Azure.So, if your logic app uses other resources, for example, an integration account and B2B artifacts, such as partners, agreements, and schemas, you must export that integration account's template by using the Azure portal. Questo modello include le definizioni delle risorse sia per l'account di integrazione che per gli elementi. Tuttavia, il modello non è completamente parametrizzato. Pertanto, è necessario parametrizzare manualmente i valori che si desidera utilizzare per la distribuzione.

### <a name="export-templates-for-integration-accounts"></a>Esportare modelli per gli account di integrazione

1. Nel [portale di Azure](https://portal.azure.com)individuare e aprire l'account di integrazione.

1. Nel menu dell'account di integrazione, in **Impostazioni**, selezionare **Esporta modello**.

1. Sulla barra degli strumenti selezionare **Scarica**e salvare il modello.

1. Aprire e modificare il modello per parametrizzare i valori necessari per la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

[Spostare le risorse di Azure in nuovi gruppi di risorse o sottoscrizioniMove Azure resources to new resource groups or subscriptions](../azure-resource-manager/management/move-resource-group-and-subscription.md)
