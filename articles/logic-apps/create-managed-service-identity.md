---
title: Eseguire l'autenticazione con le identità gestite nelle App per la logica di Azure | Microsoft Docs
description: Per eseguire l'autenticazione senza eseguire l'accesso alle risorse, è possibile creare un'identità gestita (in precedenza denominata Identità del servizio gestita) in modo che l'app per la logica possa accedere alle risorse in altri tenant di Azure Active Directory (Azure AD) senza credenziali o segreti
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 10/05/2018
ms.openlocfilehash: 19e6693de673eae6fe0b885580975c4cefc35d60
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725149"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Eseguire l'autenticazione e accedere alle risorse con le identità gestite nelle App per la logica di Azure

Per accedere alle risorse in altri tenant di Azure Active Directory (Azure AD) ed eseguire l'autenticazione dell'identità senza eseguire l'accesso, l'app per la logica può usare un'[identità gestita](../active-directory/managed-identities-azure-resources/overview.md) (in precedenza denominata identità del servizio gestita) invece di credenziali o segreti. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti. Questo articolo illustra come creare e usare un'identità gestita assegnata dal sistema per l'app per la logica. Per altre informazioni sulle identità gestite, vedere [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

> [!NOTE]
> In ogni sottoscrizione di Azure si possono attualmente avere fino a 10 flussi di lavoro di app per la logica con identità gestite assegnate dal sistema.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure, o in assenza di sottoscrizione, occorre eseguire una <a href="https://azure.microsoft.com/free/" target="_blank">iscrizione per creare un account Azure gratuito</a>.

* L'app per la logica in cui si vuole usare l'identità gestita assegnata dal sistema. Se non si dispone di un'app per la logica, vedere [Creare il primo flusso di lavoro di app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Creare un'identità gestita

È possibile creare o abilitare un'identità gestita assegnata dal sistema per la propria app per la logica attraverso il portale di Azure, i modelli di Azure Resource Manager o Azure PowerShell. 

### <a name="azure-portal"></a>Portale di Azure

Per abilitare un'identità gestita assegnata dal sistema per l'app per la logica tramite il portale di Azure, attivare l'impostazione **Registra con Azure Active Directory** nelle impostazioni del flusso di lavoro dell'app per la logica.

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. A tale scopo, seguire questa procedura: 

   1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**. 

   1. Nella sezione **Identità del servizio gestito** > 
   **Registrazione con Azure Active Directory**, scegliere **On**.

   1. Al termine dell'operazione, scegliere **Salva** sulla barra degli strumenti.

      ![Attivare l'impostazione di identità gestita](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      L'app per la logica include ora un'identità gestita assegnata dal sistema registrata in Azure Active Directory con queste proprietà e valori:

      ![GUID per l'ID entità di sicurezza e l'ID tenant](./media/create-managed-service-identity/principal-tenant-id.png)

      | Proprietà | Valore | DESCRIZIONE | 
      |----------|-------|-------------| 
      | **ID entità di sicurezza** | <*principal-ID*> | Un identificatore univoco globale (GUID) che rappresenta l'app per la logica in un tenant di Azure AD | 
      | **ID tenant** | <*Azure-AD-tenant-ID*> | Un identificatore univoco globale (GUID) che rappresenta il tenant di Azure AD di cui l'app per la logica è ora membro. All'interno del tenant di Azure AD, l'entità servizio ha lo stesso nome dell'istanza dell’app per la logica. | 
      ||| 

### <a name="deployment-template"></a>Modello di distribuzione

Quando si desidera automatizzare la creazione e la distribuzione di risorse di Azure, ad esempio delle app per la logica, è possibile impostare i [modelli di Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). Per creare un'identità gestita assegnata dal sistema per l'app per la logica tramite un modello, aggiungere l'elemento `"identity"` e la proprietà `"type"` alla definizione del flusso di lavoro dell'app per la logica nel modello di distribuzione: 

```json
"identity": {
   "type": "SystemAssigned"
}
```

Ad esempio: 

```json
{
   "apiVersion": "2016-06-01", 
   "type": "Microsoft.logic/workflows", 
   "name": "[variables('logicappName')]", 
   "location": "[resourceGroup().location]", 
   "identity": { 
      "type": "SystemAssigned" 
   }, 
   "properties": { 
      "definition": { 
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#", 
         "actions": {}, 
         "parameters": {}, 
         "triggers": {}, 
         "contentVersion": "1.0.0.0", 
         "outputs": {} 
   }, 
   "parameters": {}, 
   "dependsOn": [] 
}
```

Quando Azure crea l'app per la logica, la definizione di flusso di lavoro dell'app per la logica include queste proprietà aggiuntive:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Proprietà | Valore | DESCRIZIONE | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Un identificatore univoco globale (GUID) che rappresenta l'app per la logica nel tenant di Azure AD | 
| **tenantId** | <*Azure-AD-tenant-ID*> | Un identificatore univoco globale (GUID) che rappresenta il tenant di Azure AD di cui l'app per la logica è ora membro. All'interno del tenant di Azure AD, l'entità servizio ha lo stesso nome dell'istanza dell’app per la logica. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Accedere alle risorse con l’identità gestita

Dopo aver creato un'identità gestita assegnata dal sistema per l'app per la logica è possibile [consentire all'identità l'accesso ad altre risorse di Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). È quindi possibile usare tale identità per l'autenticazione, esattamente come qualsiasi altra [entità servizio](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> Sia l'identità gestita assegnata dal sistema che la risorsa in cui si desidera assegnare l'accesso devono avere la stessa sottoscrizione di Azure.

### <a name="assign-access-to-managed-identity"></a>Assegnare l'accesso all'identità gestita

Per fornire l'accesso a un'altra risorsa di Azure per l'identità gestita assegnata dal sistema dell'app per la logica, seguire questa procedura:

1. Nel portale di Azure passare alla risorsa di Azure in cui si desidera assegnare l'accesso per l'identità gestita. 

1. Nel menu della risorsa selezionare **Controllo di accesso (IAM)** e scegliere **Aggiungi assegnazione di ruolo**. 

   ![Aggiungi un'assegnazione di ruolo](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. In **Aggiungi assegnazione di ruolo** selezionare il **Ruolo** desiderato per l'identità. 

1. Nella proprietà **Assegna accesso a** selezionare **Entità servizio, gruppo o utente di Azure AD**, se l'opzione non è già selezionata.

1. Nella casella **Seleziona**, a partire dal primo carattere del nome dell'app per la logica, immettere il nome dell'app per la logica. Quando viene visualizzata l'app per la logica, selezionarla.

   ![Selezionare l'app per la logica con identità gestita](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Al termine dell'operazione, scegliere **Salva**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Eseguire l'autenticazione con un'identità gestita nell'app per la logica

Dopo aver configurato l'app per la logica con un'identità gestita assegnata dal sistema e assegnato l'accesso alla risorsa desiderata per l'identità, è ora possibile usare tale identità per l'autenticazione. È possibile, ad esempio, usare un'azione HTTP in modo che l'app per la logica possa inviare una richiesta HTTP o richiamare la risorsa. 

1. Nell'app per la logica, aggiungere l'azione **HTTP**. 

1. Fornire i dettagli necessari per quell'azione, come la richiesta **Metodo** e la posizione **URI** della risorsa che si desidera chiamare.

1. Nell'azione "HTTP" scegliere **Mostra opzioni avanzate**. 

1. Dall'elenco **Autenticazione**, selezionare **Identità del servizio gestita**, che mostra la proprietà **Destinatari** da impostare:

   ![Selezionare "Identità del servizio gestita"](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Continuare a compilare le app per la logica nel modo desiderato.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Rimuovere l’identità gestita

Per disabilitare un'identità gestita assegnata dal sistema nell'app per la logica, è possibile eseguire passaggi simili a quelli con cui è stata creata l'identità tramite il portale di Azure, i modelli di distribuzione Azure Resource Manager o Azure PowerShell. 

Quando si elimina l'app per la logica, Azure rimuove automaticamente l'identità assegnata dal sistema dell'app per la logica di Azure AD.

### <a name="azure-portal"></a>Portale di Azure

1. Aprire l'app per la logica in Logic App Designer.

1. A tale scopo, seguire questa procedura: 

   1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**. 
   
   1. Nella sezione **Identità del servizio gestito**, selezionare **Off** per la proprietà **Registrazione con Azure Active Directory**.

   1. Al termine dell'operazione, scegliere **Salva** sulla barra degli strumenti.

      ![Disattivare l'impostazione di identità gestita](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Modello di distribuzione

Se l'identità gestita assegnata dal sistema dell'app per la logica è stata creata con un modello di distribuzione Azure Resource Manager, impostare la proprietà `"type"` dell'elemento `"identity"` su `"None"`. Questa azione elimina anche l'ID entità di sicurezza da Azure AD. 

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

