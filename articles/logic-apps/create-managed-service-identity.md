---
title: Eseguire l'autenticazione con le identità gestite
description: Accedere alle risorse protette da Azure Active Directory senza eseguire l'accesso con credenziali o segreti usando un'identità gestita
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 73f3e7561b97574c4603e480b972eaf2a9a1ed48
ms.sourcegitcommit: a3c6efa4d4a48e9b07ecc3f52a552078d39e5732
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83708041"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autenticare l'accesso alle risorse di Azure usando identità gestite in App per la logica di Azure

Per accedere facilmente alle risorse protette da Azure Active Directory (Azure AD) ed eseguire l'autenticazione dell'identità senza eseguire l'accesso, l'app per la logica può usare un'[identità gestita](../active-directory/managed-identities-azure-resources/overview.md) (in precedenza denominata identità del servizio gestita) anziché credenziali o segreti. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti.

App per la logica di Azure supporta le identità gestite [*assegnate dal sistema*](../active-directory/managed-identities-azure-resources/overview.md) e [*assegnate dall'utente*](../active-directory/managed-identities-azure-resources/overview.md). L'app per la logica può usare l'identità assegnata dal sistema o una *singola* identità assegnata dall'utente che è possibile condividere in un gruppo di app per la logica, ma non entrambe. Attualmente, solo [trigger e azioni predefiniti specifici](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) supportano le identità gestite, non i connettori gestiti o le connessioni, ad esempio:

* HTTP
* Funzioni di Azure
* Gestione API di Azure
* Servizi app di Azure

Questo articolo illustra come configurare entrambi i tipi di identità gestite per l'app per la logica. Per altre informazioni, vedere gli argomenti seguenti:

* [Trigger e azioni che supportano le identità gestite](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Tipi di autenticazione supportati nelle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limiti relativi delle identità gestite per le app per la logica](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Servizi di Azure che supportano l'autenticazione di Azure AD con identità gestite](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). È necessario che l'identità gestita e la risorsa di destinazione di Azure a cui accedere usino la stessa sottoscrizione di Azure.

* Per concedere a un'identità gestita l'accesso a una risorsa di Azure, è necessario aggiungere un ruolo alla risorsa di destinazione per l'identità. Per aggiungere ruoli, sono necessarie le [autorizzazioni di amministratore di Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) che possono assegnare i ruoli alle identità nel tenant di Azure AD corrispondente.

* La risorsa di destinazione di Azure a cui si vuole accedere. In questa risorsa si aggiungerà un ruolo per l'identità gestita che consente all'app per la logica di autenticare l'accesso alla risorsa di destinazione.

* L'app per la logica in cui si vogliono usare [i trigger o le azioni che supportano le identità gestite](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Abilitare un'identità gestita

Per configurare l'identità gestita da usare, seguire il collegamento per l'identità:

* [Identità assegnata dal sistema](#system-assigned)
* [Identità assegnata dall'utente](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Abilitare l'identità assegnata dal sistema

A differenza delle identità assegnate dall'utente, l'identità assegnata dal sistema non deve essere creata manualmente. Per configurare l'identità assegnata dal sistema per l'app per la logica, è possibile usare le opzioni seguenti:

* [Azure portal](#azure-portal-system-logic-app)
* [Modelli di Gestione risorse di Azure](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Abilitare l'identità assegnata dal sistema nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Identità**. Selezionare **Assegnata dal sistema** > **Attiva** > **Salva**. Quando Azure chiede di confermare, selezionare **Sì**.

   ![Abilitare l'identità assegnata dal sistema](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Se viene visualizzato un errore che indica che è possibile avere una sola identità gestita, l'app per la logica è già associata all'identità assegnata dall'utente. Per poter aggiungere l'identità assegnata dal sistema, è necessario prima *rimuovere* l'identità assegnata dall'utente dall'app per la logica.

   L'app per la logica può ora usare l'identità assegnata dal sistema che è registrata in Azure Active Directory ed è rappresentata da un ID oggetto.

   ![ID oggetto per l'identità assegnata dal sistema](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Proprietà | valore | Descrizione |
   |----------|-------|-------------|
   | **ID oggetto** | <*identity-resource-ID*> | Un identificatore univoco globale (GUID) che rappresenta l'identità assegnata dal sistema per l'app per la logica in un tenant di Azure AD |
   ||||

1. A questo punto, seguire [la procedura che consente di assegnare all'identità l'accesso alla risorsa](#access-other-resources) descritta più avanti in questo argomento.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Abilitare l'identità assegnata dal sistema nel modello di Azure Resource Manager

Per automatizzare la creazione e la distribuzione di risorse di Azure, come le app per la logica, è possibile usare i [modelli di Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Per abilitare l'identità gestita assegnata dal sistema per l'app per la logica nel modello, aggiungere l'oggetto `identity` e la proprietà figlio `type` alla definizione della risorsa dell'app per la logica nel modello, ad esempio:

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

Quando Azure crea la definizione della risorsa dell'app per la logica, l'oggetto `identity` ottiene le proprietà aggiuntive seguenti:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Proprietà (JSON) | valore | Descrizione |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | L'identificatore univoco globale (GUID) dell'oggetto entità servizio per l'identità gestita che rappresenta l'app per la logica nel tenant di Azure AD. Questo GUID viene talvolta visualizzato come "ID oggetto" o `objectID`. |
| `tenantId` | <*Azure-AD-tenant-ID*> | L'identificatore univoco globale (GUID) che rappresenta il tenant di Azure AD di cui l'app per la logica è ora membro. All'interno del tenant di Azure AD, l'entità servizio ha lo stesso nome dell'istanza dell’app per la logica. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Abilitare l'identità assegnata dall'utente

Per configurare un'identità gestita assegnata dall'utente per l'app per la logica, è prima necessario creare l'identità come risorsa di Azure autonoma separata. È possibile usare le opzioni seguenti:

* [Azure portal](#azure-portal-user-identity)
* [Modelli di Gestione risorse di Azure](#template-user-identity)
* Azure PowerShell
  * [Creare un'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Aggiungere un'assegnazione di ruolo](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Interfaccia della riga di comando di Azure
  * [Creare un'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Aggiungere un'assegnazione di ruolo](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* API REST di Azure
  * [Creare un'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Aggiungere un'assegnazione di ruolo](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Creare un'identità assegnata dall'utente nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) nella casella di ricerca di una pagina immettere `managed identities` e selezionare **Identità gestite**.

   ![Trovare e selezionare "Identità gestite"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. In **Identità gestite** selezionare **Aggiungi**.

   ![Aggiungere una nuova identità gestita](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Specificare le informazioni sull'identità gestita e quindi selezionare **Crea**, ad esempio:

   ![Creare un'identità gestita assegnata dall'utente](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Nome risorsa** | Sì | <*user-assigned-identity-name*> | Nome da assegnare all'identità assegnata dall'utente. Questo esempio usa "Fabrikam-user-assigned-identity". |
   | **Sottoscrizione** | Sì | <*nome sottoscrizione di Azure*> | Nome della sottoscrizione di Azure da usare. |
   | **Gruppo di risorse** | Sì | <*Azure-resource-group-name*> | Nome per il gruppo di risorse da usare. Creare un nuovo gruppo o selezionare un gruppo esistente. Questo esempio crea un nuovo gruppo denominato "fabrikam-managed-identities-RG". |
   | **Posizione** | Sì | <*Area di Azure*> | Area di Azure in cui archiviare le informazioni sulla risorsa. Questo esempio usa "Stati Uniti occidentali". |
   |||||

   A questo punto è possibile aggiungere l'identità assegnata dall'utente all'app per la logica. Non è possibile aggiungere più di un'identità assegnata dall'utente all'app per la logica.

1. Nel portale di Azure individuare e aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica in **Impostazioni** selezionare **Identità** e quindi selezionare **Assegnata dall'utente** > **Aggiungi**.

   ![Aggiungere un'identità gestita assegnata dall'utente](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. Nel riquadro **Aggiungi identità gestita assegnata dall'utente** dall'elenco **Sottoscrizione** selezionare la sottoscrizione di Azure, se non è già selezionata. Dall'elenco che mostra *tutte* le identità gestite nella sottoscrizione, trovare e selezionare l'identità assegnata dall'utente desiderata. Per filtrare l'elenco, nella casella di ricerca **Identità gestite assegnate dall'utente** immettere il nome per l'identità o il gruppo di risorse. Al termine, selezionare **Aggiungi**.

   ![Selezionare l'identità assegnata dall'utente da usare](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Se viene visualizzato un errore che indica che è possibile avere una sola identità gestita, l'app per la logica è già associata all'identità assegnata dal sistema. Per poter aggiungere l'identità assegnata dall'utente, è necessario prima disabilitare l'identità assegnata dal sistema nell'app per la logica.

   L'app per la logica è ora associata all'identità gestita assegnata dall'utente.

   ![Associazione a un'identità assegnata dall'utente](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. A questo punto, seguire [la procedura che consente di assegnare all'identità l'accesso alla risorsa](#access-other-resources) descritta più avanti in questo argomento.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Creare l'identità assegnata dall'utente in un modello di Azure Resource Manager

Per automatizzare la creazione e la distribuzione delle risorse di Azure, ad esempio delle app per la logica, è possibile usare i [modelli di Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) che supportano [le identità assegnate dall'utente per l'autenticazione](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). Nella sezione `resources` del modello la definizione di risorsa dell'app per la logica richiede gli elementi seguenti:

* Un oggetto `identity` con la proprietà `type` impostata su `UserAssigned`

* Un oggetto `userAssignedIdentities` figlio che specifica l'ID risorsa dell'identità, ovvero un altro oggetto figlio con le proprietà `principalId` e `clientId`

Questo esempio illustra una definizione di risorsa dell'app per la logica per una richiesta HTTP PUT e include un oggetto `identity` senza parametri. Anche la risposta alla richiesta PUT e l'operazione GET successiva hanno questo oggetto `identity`:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

| Proprietà (JSON) | valore | Descrizione |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | L'identificatore univoco globale (GUID) per l'identità gestita assegnata dall'utente nel tenant di Azure AD |
| `clientId` | <*client-ID*> | Un identificatore univoco globale (GUID) per la nuova identità dell'app per la logica usata per le chiamate durante il runtime |
||||

Se il modello include anche la definizione di risorsa dell'identità gestita, è possibile parametrizzare l'oggetto `identity`. Questo esempio illustra il modo in cui l'oggetto `userAssignedIdentities` figlio fa riferimento a una variabile `userAssignedIdentity` definita nella sezione `variables` del modello. Questa variabile fa riferimento all'ID risorsa per l'identità assegnata dall'utente.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Proprietà (JSON) | valore | Descrizione |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-tenant-ID*> | L'identificatore univoco globale (GUID) che rappresenta il tenant di Azure AD di cui l'identità assegnata dall'utente è ora membro. All'interno del tenant di Azure AD l'entità servizio ha lo stesso nome dell'identità assegnata dall'utente. |
| `principalId` | <*principal-ID*> | L'identificatore univoco globale (GUID) per l'identità gestita assegnata dall'utente nel tenant di Azure AD |
| `clientId` | <*client-ID*> | Un identificatore univoco globale (GUID) per la nuova identità dell'app per la logica usata per le chiamate durante il runtime |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Concedere all'identità l'accesso alle risorse

Prima di poter usare l'identità gestita dell'app per la logica per l'autenticazione, configurare l'accesso per l'identità nella risorsa di Azure in cui si prevede di usare l'identità. Per completare questa attività, assegnare il ruolo appropriato all'identità nella risorsa di destinazione di Azure. È possibile usare le opzioni seguenti:

* [Azure portal](#azure-portal-assign-access)
* [Modello di Azure Resource Manager](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)). Per altre informazioni, vedere [Aggiungere un'assegnazione di ruolo usando il controllo degli accessi in base al ruolo di Azure e Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Interfaccia della riga di comando di Azure ([az role assignment create](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)). Per altre informazioni, vedere [Aggiungere un'assegnazione di ruolo usando il controllo degli accessi in base al ruolo di Azure e l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md).
* [API REST di Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Assegnare l'accesso nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) passare alla risorsa di Azure a cui si vuole che l'identità gestita abbia accesso.

1. Dal menu della risorsa selezionare **Controllo di accesso (IAM)**  > **Assegnazioni di ruolo** in cui è possibile esaminare le assegnazioni di ruolo correnti per la risorsa. Nella barra degli strumenti selezionare **Aggiungi** > **Aggiungi un'assegnazione di ruolo**.

   ![Selezionare "Aggiungi" > "Aggiungi un'assegnazione di ruolo"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Se l'opzione **Aggiungi un'assegnazione di ruolo** è disabilitata, è probabile che non si abbiano le autorizzazioni. Per altre informazioni sulle autorizzazioni che consentono di gestire i ruoli per le risorse, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. In **Aggiungi un'assegnazione di ruolo** selezionare un **Ruolo** che offra all'identità l'accesso necessario alla risorsa di destinazione.

   Per l'esempio di questo argomento, l'identità richiede un [ruolo in grado di accedere al BLOB in un contenitore di Archiviazione di Azure](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

   ![Selezionare il ruolo "Collaboratore ai dati dei BLOB di archiviazione"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Seguire questa procedura per l'identità gestita:

   * **Identità assegnata dal sistema**

     1. Nella casella **Assegna accesso a** selezionare **App per la logica**. Quando viene visualizzata la proprietà **Sottoscrizione**, selezionare la sottoscrizione di Azure associata all'identità.

        ![Selezionare l'accesso per l'identità assegnata dal sistema](./media/create-managed-service-identity/assign-access-system.png)

     1. Nella casella **Seleziona** selezionare l'app per la logica dall'elenco. Se l'elenco è troppo lungo, usare la casella **Seleziona** per filtrare l'elenco.

        ![Selezionare l'app per la logica per l'identità assegnata dal sistema](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Identità assegnata dall'utente**

     1. Nella casella **Assegna accesso a** selezionare **Identità gestita assegnata dall'utente**. Quando viene visualizzata la proprietà **Sottoscrizione**, selezionare la sottoscrizione di Azure associata all'identità.

        ![Selezionare l'accesso per l'identità assegnata dall'utente](./media/create-managed-service-identity/assign-access-user.png)

     1. Nella casella **Seleziona** selezionare l'identità dall'elenco. Se l'elenco è troppo lungo, usare la casella **Seleziona** per filtrare l'elenco.

        ![Selezionare l'identità assegnata dall'utente](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Al termine, selezionare **Salva**.

   L'elenco delle assegnazioni di ruolo della risorsa di destinazione mostra ora l'identità gestita e il ruolo selezionati. Questo esempio mostra come usare l'identità assegnata dal sistema per un'app per la logica e un'identità assegnata dall'utente per un gruppo di altre app per la logica.

   ![Identità gestite e ruoli aggiunti alla risorsa di destinazione](./media/create-managed-service-identity/added-roles-for-identities.png)

   Per altre informazioni, vedere [Assegnare a un'identità gestita l'accesso a una risorsa tramite il portale di Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Seguire ora la [procedura per autenticare l'accesso con l'identità](#authenticate-access-with-identity) in un trigger o in un'azione che supporta le identità gestite.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Eseguire l'autenticazione dell'accesso con l'identità gestita

Dopo aver [abilitato l'identità gestita per l'app per la logica](#azure-portal-system-logic-app) e [aver assegnato all'identità l'accesso alla risorsa o all'entità di destinazione](#access-other-resources), è possibile usare l'identità nei [trigger e nelle azioni che supportano le identità gestite](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Se si ha una funzione di Azure in cui si vuole usare l'identità assegnata dal sistema, [abilitare prima l'autenticazione per le funzioni di Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Questa procedura mostra come usare l'identità gestita con un trigger o un'azione attraverso il portale di Azure. Per specificare l'identità gestita nella definizione JSON sottostante di un trigger o di un'azione, vedere [Autenticazione dell'identità gestita](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Se non è ancora stato fatto, aggiungere [il trigger o l'azione che supporta le identità gestite](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Ad esempio, il trigger o l'azione HTTP può usare l'identità assegnata dal sistema abilitata per l'app per la logica. In generale, il trigger o l'azione HTTP usa queste proprietà per specificare la risorsa o l'entità a cui si vuole accedere:

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Metodo** | Sì | Il metodo HTTP usato dall'operazione che si vuole eseguire |
   | **URI** | Sì | L'URL dell'endpoint per l'accesso alla risorsa o all'entità di destinazione di Azure. La sintassi dell'URI include in genere l'[ID risorsa](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) per la risorsa o il servizio di Azure. |
   | **Intestazioni** | No | Eventuali valori di intestazione necessari o che si vuole includere nella richiesta in uscita, ad esempio il tipo di contenuto |
   | **Query** | No | Tutti i parametri di query necessari o che si vuole includere nella richiesta, ad esempio il parametro per un'operazione specifica o la versione dell'API per l'operazione che si vuole eseguire |
   | **autenticazione** | Sì | Il tipo di autenticazione da usare per autenticare l'accesso alla risorsa o all'entità di destinazione |
   ||||

   Come esempio specifico, si supponga di voler eseguire l'[operazione Snapshot BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) in un BLOB nell'account di archiviazione di Azure in cui è stato precedentemente configurato l'accesso per l'identità. Tuttavia, il [connettore di archiviazione BLOB di Azure](https://docs.microsoft.com/connectors/azureblob/) attualmente non offre questa operazione. In alternativa, è possibile eseguire questa operazione usando l'[azione HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) o un'altra [operazione dell'API REST del servizio BLOB](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Per accedere agli account di archiviazione di Azure dietro i firewall usando le richieste HTTP e le identità gestite, assicurarsi di configurare anche l'account di archiviazione con l'[eccezione che consente l'accesso da parte di servizi Microsoft attendibili](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Per eseguire l'[operazione Snapshot BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob), l'azione HTTP specifica le proprietà seguenti:

   | Proprietà | Obbligatoria | Valore di esempio | Descrizione |
   |----------|----------|---------------|-------------|
   | **Metodo** | Sì | `PUT`| Il metodo HTTP usato dall'operazione Snapshot BLOB |
   | **URI** | Sì | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | L'ID risorsa per un file di archiviazione BLOB di Azure nell'ambiente globale (pubblico) di Azure che usa questa sintassi |
   | **Intestazioni** | Sì, per Archiviazione di Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | I valori di intestazione `x-ms-blob-type` e `x-ms-version` richiesti per le operazioni di Archiviazione di Azure. <p><p>**Importante**: nel trigger HTTP in uscita e nelle richieste di azione per Archiviazione di Azure, l'intestazione richiede la proprietà `x-ms-version` e la versione dell'API per l'operazione che si vuole eseguire. <p>Per altre informazioni, vedere gli argomenti seguenti: <p><p>- [Intestazioni delle richieste - Snapshot BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Controllo delle versioni per i servizi di Archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Query** | Sì, per questa operazione | `comp` = `snapshot` | Nome e valore del parametro di query per l'operazione Snapshot BLOB. |
   |||||

   L'azione HTTP di esempio mostra tutti i valori delle proprietà:

   ![Aggiungere un'azione HTTP per accedere a una risorsa di Azure](./media/create-managed-service-identity/http-action-example.png)

1. Aggiungere ora la proprietà **Autenticazione** all'azione HTTP. Dall'elenco **Aggiungi nuovo parametro** selezionare **Autenticazione**.

   ![Aggiungere la proprietà "Autenticazione" all'azione HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Non tutti i trigger e le azioni supportano l'aggiunta di un tipo di autenticazione. Per altre informazioni, vedere [Aggiungere l'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Dall'elenco **Tipo di autenticazione** selezionare **Identità gestita**.

   ![Per "Autenticazione", selezionare "Identità gestita"](./media/create-managed-service-identity/select-managed-identity.png)

1. Dall'elenco delle identità gestite selezionare una delle opzioni disponibili in base allo scenario.

   * Se si configura l'identità assegnata dal sistema, selezionare l'opzione **Identità gestita assegnata dal sistema**, se non è già selezionata.

     ![Selezionare "Identità gestita assegnata dal sistema"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Se si configura un'identità assegnata dall'utente, selezionare l'identità, se non è già selezionata.

     ![Selezionare l'identità assegnata dall'utente](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Questo esempio continua con l'**Identità gestita assegnata dal sistema**.

1. In alcuni trigger e azioni viene visualizzata anche la proprietà **Destinatari** che consente di impostare l'ID risorsa di destinazione. Impostare la proprietà **Destinatari** sull'[ID di risorsa per la risorsa o il servizio di destinazione](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). In caso contrario, per impostazione predefinita, la proprietà **Destinatari** usa l'ID risorsa `https://management.azure.com/` che è l'ID risorsa per Azure Resource Manager.

   > [!IMPORTANT]
   > Assicurarsi che l'ID risorsa di destinazione *corrisponda esattamente* al valore previsto da Azure Active Directory (AD), incluse le eventuali barre finali necessarie. Ad esempio, l'ID risorsa per tutti gli account di archiviazione BLOB di Azure richiede una barra finale. Tuttavia, l'ID risorsa per un account di archiviazione specifico non richiede una barra finale. Controllare gli [ID risorsa per i servizi di Azure che supportano Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Questo esempio imposta la proprietà **Destinatari** su `https://storage.azure.com/` in modo che i token di accesso usati per l'autenticazione siano validi per tutti gli account di archiviazione. Tuttavia, è anche possibile specificare l'URL del servizio radice, `https://fabrikamstorageaccount.blob.core.windows.net` per un account di archiviazione specifico.

   ![Impostare la proprietà "Destinatari" sull'ID risorsa di destinazione](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Per altre informazioni sull'autorizzazione dell'accesso con Azure AD per Archiviazione di Azure, vedere gli argomenti seguenti:

   * [Autorizzare l'accesso a BLOB e code di Azure tramite Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorizzare l'accesso ad Archiviazione di Azure con Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Continuare a compilare l'app per la logica nel modo desiderato.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Disabilitare l'identità gestita

Per interrompere l'uso di un'identità gestita per l'app per la logica, sono disponibili le opzioni seguenti:

* [Azure portal](#azure-portal-disable)
* [Modelli di Gestione risorse di Azure](#template-disable)
* Azure PowerShell
  * [Rimuovere l'assegnazione di ruolo](../role-based-access-control/role-assignments-powershell.md)
  * [Eliminare l'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Interfaccia della riga di comando di Azure
  * [Rimuovere l'assegnazione di ruolo](../role-based-access-control/role-assignments-cli.md)
  * [Eliminare l'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* API REST di Azure
  * [Rimuovere l'assegnazione di ruolo](../role-based-access-control/role-assignments-rest.md)
  * [Eliminare l'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Se si elimina l'app per la logica, Azure rimuove automaticamente l'identità gestita da Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Disabilitare l'identità gestita nel portale di Azure

Nel portale di Azure rimuovere prima di tutto l'accesso dell'identità alla [risorsa di destinazione](#disable-identity-target-resource). Disattivare quindi l'identità assegnata dal sistema o rimuovere l'identità assegnata dall'utente dall'[app per la logica](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Rimuovere l'accesso dell'identità dalle risorse

1. Nel [portale di Azure](https://portal.azure.com) passare alla risorsa di destinazione di Azure in cui si vuole rimuovere l'accesso per l'identità gestita.

1. Nel menu della risorsa di destinazione selezionare **Controllo di accesso (IAM)** . Nella barra degli strumenti selezionare **Assegnazioni di ruoli**.

1. Nell'elenco dei ruoli selezionare le identità gestite che si vuole rimuovere. Nella barra degli strumenti selezionare **Rimuovi**.

   > [!TIP]
   > Se l'opzione **Rimuovi** è disabilitata, è probabile che non si abbiano le autorizzazioni. Per altre informazioni sulle autorizzazioni che consentono di gestire i ruoli per le risorse, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

L'identità gestita viene ora rimossa e non ha più accesso alla risorsa di destinazione.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Disabilitare l'identità gestita nell'app per la logica

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica in **Impostazioni** selezionare **Identità** e quindi seguire la procedura per l'identità:

   * Selezionare **Assegnata dal sistema** > **Attiva** > **Salva**. Quando Azure chiede di confermare, selezionare **Sì**.

     ![Disabilitare l'identità assegnata dal sistema](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Selezionare **Assegnata dall'utente** e l'identità gestita, quindi selezionare **Rimuovi**. Quando Azure chiede di confermare, selezionare **Sì**.

     ![Rimuovere l'identità assegnata dall'utente](./media/create-managed-service-identity/remove-user-assigned-identity.png)

L'identità gestita è ora disabilitata nell'app per la logica.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Disabilitare l'identità gestita nel modello di Azure Resource Manager

Se l'identità gestita dell'app per la logica è stata creata con un modello di Azure Resource Manager, impostare la proprietà figlio `type` dell'oggetto `identity` su `None`. Per l'identità gestita dal sistema, questa azione elimina anche l'ID entità di sicurezza da Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere l'accesso e i dati in App per la logica di Azure](../logic-apps/logic-apps-securing-a-logic-app.md)
