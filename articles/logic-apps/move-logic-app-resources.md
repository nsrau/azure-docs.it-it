---
title: Spostare app per la logica in sottoscrizioni, gruppi di risorse o aree geografiche-app per la logica di Azure
description: Eseguire la migrazione di app per la logica o account di integrazione ad altre sottoscrizioni di Azure, gruppi di risorse o posizioni (aree)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 8c3bad32943b83cbfe4c96087f3fef1c51f64bb1
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679102"
---
# <a name="move-logic-app-resources-to-other-azure-subscriptions-resource-groups-or-regions"></a>Spostare le risorse dell'app per la logica in altre sottoscrizioni, gruppi di risorse o aree di Azure

Per spostare l'app per la logica o le risorse correlate in un'altra sottoscrizione di Azure, gruppo di risorse o area, sono disponibili vari modi per completare queste attività, ad esempio portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Prima di spostare le risorse, esaminare le considerazioni seguenti: 

* È possibile spostare solo [specifici tipi di risorse dell'app](../azure-resource-manager/move-support-resources.md#microsoftlogic) per la logica tra gruppi di risorse o sottoscrizioni di Azure.

* Verificare i [limiti](../logic-apps/logic-apps-limits-and-config.md) per il numero di risorse dell'app per la logica che è possibile avere nella sottoscrizione di Azure e in ogni area di Azure. Questi limiti influiscono sulla possibilità di spostare tipi di risorse specifici quando l'area rimane invariata tra le sottoscrizioni o i gruppi di risorse. Ad esempio, è possibile avere un solo account di integrazione del livello gratuito per ogni area di Azure in ogni sottoscrizione di Azure.

* Quando si spostano le risorse, Azure crea nuovi ID risorsa. Quindi, assicurarsi di usare invece i nuovi ID e aggiornare gli script o gli strumenti associati alle risorse spostate. Dopo aver spostato le app per la logica tra sottoscrizioni, gruppi di risorse o aree, è necessario ricreare o riautorizzare le connessioni basate su OAuth.

## <a name="prerequisites"></a>Prerequisiti

* La stessa sottoscrizione di Azure usata per creare l'app per la logica o l'account di integrazione che si desidera spostare

* Autorizzazioni del proprietario della risorsa per spostare e configurare le risorse desiderate. Altre informazioni sul [controllo degli accessi in base al ruolo (RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Spostare le risorse tra sottoscrizioni

Per spostare una risorsa, ad esempio un'app per la logica o un account di integrazione, in un'altra sottoscrizione di Azure, è possibile usare l'portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Questi passaggi illustrano la portale di Azure, che è possibile usare quando l'area della risorsa rimane invariata. Per altri passaggi e preparazione generale, vedere [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../azure-resource-manager/resource-group-move-resources.md).

1. Nella [portale di Azure](https://portal.azure.com)trovare e selezionare la risorsa dell'app per la logica che si desidera spostare.

1. Nella pagina **Panoramica** della risorsa, accanto a **sottoscrizione**, selezionare il collegamento **modifica** .

1. Nella pagina **Sposta risorse** selezionare la risorsa app per la logica ed eventuali risorse correlate che si desidera spostare.

1. Dall'elenco **sottoscrizione** selezionare la sottoscrizione di destinazione.

1. Dall'elenco **gruppo di risorse** selezionare il gruppo di risorse di destinazione. In alternativa, per creare un gruppo di risorse diverso, selezionare **Crea un nuovo gruppo**.

1. Per confermare che gli script o gli strumenti associati alle risorse spostate non funzioneranno finché non vengono aggiornati con i nuovi ID di risorsa, selezionare la casella di conferma e quindi fare clic su **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Spostare le risorse tra gruppi di risorse

Per spostare una risorsa, ad esempio un'app per la logica o un account di integrazione, in un altro gruppo di risorse di Azure, è possibile usare l'portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Questi passaggi illustrano la portale di Azure, che è possibile usare quando l'area della risorsa rimane invariata. Per altri passaggi e preparazione generale, vedere [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../azure-resource-manager/resource-group-move-resources.md).

Prima di spostare effettivamente le risorse tra i gruppi, è possibile verificare se è possibile spostare correttamente la risorsa in un altro gruppo. Per altre informazioni, vedere [convalidare lo spostamento](../azure-resource-manager/resource-group-move-resources.md#validate-move).

1. Nella [portale di Azure](https://portal.azure.com)trovare e selezionare la risorsa dell'app per la logica che si desidera spostare.

1. Nella pagina **Panoramica** della risorsa, accanto a **gruppo di risorse**, selezionare il collegamento **modifica** .

1. Nella pagina **Sposta risorse** selezionare la risorsa app per la logica ed eventuali risorse correlate che si desidera spostare.

1. Dall'elenco **gruppo di risorse** selezionare il gruppo di risorse di destinazione. In alternativa, per creare un gruppo di risorse diverso, selezionare **Crea un nuovo gruppo**.

1. Per confermare che gli script o gli strumenti associati alle risorse spostate non funzioneranno finché non vengono aggiornati con i nuovi ID di risorsa, selezionare la casella di conferma e quindi fare clic su **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Spostare le risorse tra aree

Quando si vuole spostare un'app per la logica in un'area diversa, le opzioni variano a seconda del modo in cui è stata creata l'app per la logica. In base all'opzione scelta, è necessario ricreare o riautorizzare le connessioni nell'app per la logica.

* Nella portale di Azure ricreare l'app per la logica nella nuova area e riconfigurare le impostazioni del flusso di lavoro. Per risparmiare tempo, è possibile copiare la definizione del flusso di lavoro sottostante e le connessioni dall'app di origine all'app di destinazione. Per visualizzare il "codice" dietro un'app per la logica, nella barra degli strumenti di progettazione app per la logica selezionare **visualizzazione codice**.

* Usando Visual Studio e gli strumenti delle app per la logica di Azure per Visual Studio, è possibile [aprire e scaricare l'app](../logic-apps/manage-logic-apps-with-visual-studio.md) per la logica dalla portale di Azure come [modello di Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Questo modello è prevalentemente pronto per la distribuzione e include le definizioni delle risorse per l'app per la logica, tra cui il flusso di lavoro stesso e le connessioni. Il modello dichiara anche i parametri per i valori da usare durante la distribuzione. In questo modo, è possibile modificare più facilmente la posizione e la modalità di distribuzione dell'app per la logica in base alle esigenze. Per specificare il percorso e altre informazioni necessarie per la distribuzione, è possibile usare un file di parametri separato.

* Se l'app per la logica è stata creata e distribuita usando gli strumenti di integrazione continua (CI) e recapito continuo (CD), ad esempio Azure Pipelines in Azure DevOps, è possibile distribuire l'app in un'altra area usando questi strumenti.

Per ulteriori informazioni sui modelli di distribuzione per le app per la logica, vedere gli argomenti seguenti:

* [Panoramica: automatizzare la distribuzione per le app per la logica di Azure usando modelli di Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Trovare, aprire e scaricare l'app per la logica dal portale di Azure in Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Creare modelli di Azure Resource Manager per app per la logica di Azure](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Distribuire modelli di Azure Resource Manager per app per la logica di Azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Risorse correlate

Alcune risorse di Azure, ad esempio le risorse del gateway dati locale in Azure, possono esistere in un'area diversa dalle app per la logica che usano tali risorse. Tuttavia, altre risorse di Azure, ad esempio gli account di integrazione collegati, devono esistere nella stessa area delle app per la logica. In base allo scenario, assicurarsi che le app per la logica possano accedere alle risorse che le app si aspettano di esistere nella stessa area.

Ad esempio, per collegare un'app per la logica a un account di integrazione, entrambe le risorse devono esistere nella stessa area. In scenari come il ripristino di emergenza, in genere si vogliono gli account di integrazione con la stessa configurazione e gli stessi artefatti. In altri scenari potrebbe essere necessario disporre di account di integrazione con configurazioni ed elementi diversi.

I connettori personalizzati in app per la logica di Azure sono visibili agli autori dei connettori e agli utenti che hanno la stessa sottoscrizione di Azure e lo stesso tenant Azure Active Directory. Questi connettori sono disponibili nella stessa area in cui vengono distribuite le app per la logica. Per altre informazioni, vedere [Share custom connectors in your organization](https://docs.microsoft.com/connectors/custom-connectors/share) (Condividere i connettori personalizzati nell'organizzazione).

Il modello ottenuto da Visual Studio include solo le definizioni delle risorse per l'app per la logica e le relative connessioni. Quindi, se l'app per la logica usa altre risorse, ad esempio un account di integrazione e gli artefatti B2B, ad esempio partner, contratti e schemi, è necessario esportare il modello di tale account di integrazione usando il portale di Azure. Questo modello include le definizioni delle risorse per l'account di integrazione e gli artefatti. Tuttavia, il modello non è completamente parametrizzato. Pertanto, è necessario parametrizzare manualmente i valori che si desidera utilizzare per la distribuzione.

### <a name="export-templates-for-integration-accounts"></a>Esportare i modelli per gli account di integrazione

1. Nella [portale di Azure](https://portal.azure.com)individuare e aprire l'account di integrazione.

1. Nel menu dell'account di integrazione in **Impostazioni**selezionare **Esporta modello**.

1. Sulla barra degli strumenti selezionare **Scarica**e salvare il modello.

1. Aprire e modificare il modello per parametrizzare i valori necessari per la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

[Spostare le risorse di Azure in nuovi gruppi di risorse o sottoscrizioni](../azure-resource-manager/resource-group-move-resources.md)