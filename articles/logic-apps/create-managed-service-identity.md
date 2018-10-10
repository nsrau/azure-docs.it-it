---
title: Accedere ed eseguire l'autenticazione senza eseguire l'accesso - App per la logica di Azure | Microsoft Docs
description: Creare un'identità gestita in modo che la propria app per la logica possa eseguire l’autenticazione e accedere alle risorse di altri tenant di Azure Active Directory (Azure AD) senza le credenziali
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: fb1c31e6e7c075e20191a4e51d7b1a9323f3b979
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973967"
---
# <a name="access-resources-and-authenticate-as-managed-identities-in-azure-logic-apps"></a>Accedere a risorse ed eseguire l'autenticazione come identità gestite nelle App per la logica di Azure

Per accedere alle risorse di altri tenant Azure Active Directory (Azure AD) ed eseguire l'autenticazione dell'identità senza effettuare l'accesso, è possibile creare un’[identità gestita](../active-directory/managed-identities-azure-resources/overview.md) che l'app per la logica usa al posto delle proprie credenziali. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti. Questo articolo illustra come creare e usare un'identità gestita per l'app per la logica. Per altre informazioni, vedere [Gestione delle identità per le risorse di Azure](../app-service/app-service-managed-service-identity.md).

> [!NOTE]
> Identità gestite per le risorse di Azure è il nome sostitutivo assegnato al servizio precedentemente noto come Identità del servizio gestita.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure, o in assenza di sottoscrizione, occorre eseguire una <a href="https://azure.microsoft.com/free/" target="_blank">iscrizione per creare un account Azure gratuito</a>.

* L'app per la logica in cui si vuole usare l’identità gestita. Se non si dispone di un'app per la logica, vedere [Creare il primo flusso di lavoro di app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Creare un'identità gestita

È possibile creare o abilitare un'identità gestita per la propria app per la logica attraverso il portale di Azure, i modelli di Azure Resource Manager o Azure PowerShell. 

### <a name="azure-portal"></a>Portale di Azure

Per creare un'identità gestita per l'app per la logica tramite il portale di Azure, attivare l'impostazione **Registrazione con Azure Active Directory** nelle impostazioni del flusso di lavoro dell'app per la logica.

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. A tale scopo, seguire questa procedura: 

   1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**. 

   1. Nella sezione **Identità del servizio gestito** > 
   **Registrazione con Azure Active Directory**, scegliere **On**.

   1. Al termine dell'operazione, scegliere **Salva** sulla barra degli strumenti.

      ![Attivare l'impostazione di identità gestita](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      Azure mostra ora queste proprietà e i valori per l’identità gestita dell'app per la logica:

      ![GUID per l'ID entità di sicurezza e l'ID tenant](./media/create-managed-service-identity/principal-tenant-id.png)

      | Proprietà | Valore | DESCRIZIONE | 
      |----------|-------|-------------| 
      | **ID entità di sicurezza** | <*principal-ID-GUID*> | Un identificatore univoco globale (GUID) che rappresenta l'app per la logica in un tenant di Azure AD | 
      | **ID tenant** | <*Azure-AD-tenant--ID-GUID*> | Un identificatore univoco globale (GUID) che rappresenta il tenant di Azure AD di cui l'app per la logica è ora membro. All'interno del tenant di Azure AD, l'entità servizio ha lo stesso nome dell'istanza dell’app per la logica. | 
      ||| 

### <a name="deployment-template"></a>Modello di distribuzione

Per automatizzare la creazione e la distribuzione di risorse di Azure, come le app per la logica, è possibile impostare i modelli di Azure Resource Manager. Per ulteriori informazioni, vedere [Creare e distribuire app con i modelli di Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). 

Per creare un'identità gestita per l’app per la logica tramite un modello, aggiungere l’elemento **identity** e **type** proprietà per la definizione del flusso di lavoro dell'app per la logica nel modello di distribuzione. Queste impostazioni indicano che Azure crea e gestisce questa identità per l'app per la logica:

```json
"identity": {
    "type": "SystemAssigned"
}
```

Ad esempio, l'app per la logica dovrebbe avere un aspetto simile alla versione seguente:

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
    "principalId": "<principal-ID-GUID>",
    "tenantId": "<Azure-AD-tenant-ID>-GUID"
}
```

| Proprietà | Valore | DESCRIZIONE | 
|----------|-------|-------------|
| **principalId** | <*principal-ID-GUID*> | Un identificatore univoco globale (GUID) che rappresenta l'app per la logica nel tenant di Azure AD | 
| **tenantId** | <*Azure-AD-tenant--ID-GUID*> | Un identificatore univoco globale (GUID) che rappresenta il tenant di Azure AD di cui l'app per la logica è ora membro. All'interno del tenant di Azure AD, l'entità servizio ha lo stesso nome dell'istanza dell’app per la logica. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Accedere alle risorse con l’identità gestita

Dopo aver creato un'identità gestita per l’app per la logica, è possibile [consentire l'accesso di identità ad altre risorse](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). È quindi possibile usare tale identità gestita per l'autenticazione, esattamente come qualsiasi altra [entità servizio](../active-directory/develop/app-objects-and-service-principals.md). 

Ad esempio, si supponga di aver già configurato un'app per la logica con un'identità gestita che ha accesso a un'altra risorsa. È ora possibile aggiungere un'azione HTTP in modo che l’app per la logica possa inviare una richiesta HTTP o chiamare questa risorsa. 

1. Nell'app per la logica, aggiungere l'azione **HTTP**. 

1. Fornire i dettagli necessari per quell'azione, come la richiesta **Metodo** e la posizione **URI** della risorsa che si desidera chiamare.

1. Nell'azione "HTTP" scegliere **Mostra opzioni avanzate**. 

1. Dall'elenco **Autenticazione**, selezionare **Identità del servizio gestita**, che mostra la proprietà **Destinatari** da impostare:

   ![Selezionare "Identità del servizio gestita"](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Continuare a compilare le app per la logica nel modo desiderato.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Rimuovere l’identità gestita

Per disabilitare un'identità gestita per app per la logica, è possibile seguire i passaggi simili a quelli con cui è stata creata l'identità tramite il portale di Azure, i modelli di distribuzione Azure Resource Manager o Azure PowerShell. 

Quando si elimina l'app per la logica, Azure rimuove automaticamente l'identità assegnata dal sistema dell'app per la logica di Azure AD.

### <a name="azure-portal"></a>Portale di Azure

1. Aprire l'app per la logica in Logic App Designer.

1. A tale scopo, seguire questa procedura: 

   1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**. 
   
   1. Nella sezione **Identità del servizio gestito**, selezionare **Off** per la proprietà **Registrazione con Azure Active Directory**.

   1. Al termine dell'operazione, scegliere **Salva** sulla barra degli strumenti.

      ![Disattivare l'impostazione di identità gestita](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Modello di distribuzione

Se l’identità gestita dell'app per la logica è stata creata con un modello di distribuzione Azure Resource Manager, impostare la proprietà `"type"` dell’elemento `"identity"` su `"None"`. Questa azione elimina anche l'ID entità di sicurezza da Azure AD. 

```json
"identity": {
    "type": "None"
}
```

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).
