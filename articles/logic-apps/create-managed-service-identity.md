---
title: Eseguire l'autenticazione con le identità gestite
description: Accedere alle risorse in altri tenant di Azure Active Directory senza accedere con credenziali o segreti usando un'identità gestitaAccess resources in other Azure Active Directory tenants without signing in with credentials or secrets by using a managed identity
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117531"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autenticare l'accesso alle risorse di Azure usando le identità gestite nelle app per la logica di AzureAuthenticate access to Azure resources by using managed identities in Azure Logic Apps

Per accedere alle risorse in altri tenant di Azure Active Directory (Azure AD) e autenticare l'identità senza eseguire l'accesso, l'app per la logica può usare [un'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) (in precedenza identità del servizio gestito o MSI) anziché credenziali o segreti. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti.

Le app per la logica di Azure supportano sia le identità gestite [*assegnate dal sistema*](../active-directory/managed-identities-azure-resources/overview.md) che quelle [*assegnate dall'utente.*](../active-directory/managed-identities-azure-resources/overview.md) L'app per la logica può usare l'identità assegnata dal sistema o una *singola* identità assegnata dall'utente, che è possibile condividere tra un gruppo di app per la logica, ma non entrambe. Attualmente, solo i trigger predefiniti specifici e le azioni supportano le identità gestite, non i connettori gestiti o le connessioni, ad esempio:Currently, only, only specific [built-in triggers](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) and actions support managed identities, not managed connectors or connections, for example:

* HTTP
* Funzioni di Azure
* Gestione API di Azure
* Servizi app di Azure

Questo articolo illustra come configurare entrambi i tipi di identità gestite per l'app per la logica. Per altre informazioni, vedere gli argomenti seguenti:

* [Trigger e azioni che supportano le identità gestiteTriggers and actions that support managed identities](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Tipi di autenticazione supportati nelle chiamate in uscitaSupported authentication types on outbound calls](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limiti di identità gestiti per le app per la logicaManaged identity limits for logic apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Servizi di Azure che supportano l'autenticazione di Azure AD con identità gestiteAzure services that support Azure AD authentication with managed identities](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). Sia l'identità gestita che la risorsa di Azure di destinazione a cui è necessario accedere devono usare la stessa sottoscrizione di Azure.Both the managed identity and the target Azure resource where you need access must use the same Azure subscription.

* Per concedere a un'identità gestita l'accesso a una risorsa di Azure, è necessario aggiungere un ruolo alla risorsa di destinazione per tale identità. Per aggiungere ruoli, sono necessarie le autorizzazioni di amministratore di [Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) che possono assegnare ruoli alle identità nel tenant di Azure AD corrispondente.

* Risorsa di Azure di destinazione a cui si vuole accedere. In questa risorsa si aggiungerà un ruolo per l'identità gestita, che consente all'app per la logica di autenticare l'accesso alla risorsa di destinazione.

* App per la logica in cui si vuole usare il [trigger o le azioni che supportano le identità gestiteThe logic](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) app where you want to use the trigger or actions that support managed identities

## <a name="enable-managed-identity"></a>Abilitare un'identità gestita

Per impostare l'identità gestita che si desidera utilizzare, seguire il collegamento relativo a tale identità:

* [Identità assegnata dal sistema](#system-assigned)
* [Identità assegnata dall'utente](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Abilita identità assegnata dal sistema

A differenza delle identità assegnate dall'utente, non è necessario creare manualmente l'identità assegnata dal sistema. Per configurare l'identità assegnata dal sistema per l'app per la logica, ecco le opzioni che puoi usare:

* [Portale di Azure](#azure-portal-system-logic-app)
* [Modelli di Gestione risorse di Azure](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Abilitare l'identità assegnata dal sistema nel portale di AzureEnable system-assigned identity in Azure portal

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Identità**. Selezionare **Sistema assegnato** > **al** > **salvataggio**. Quando viene richiesto da Azure di confermare, selezionare **Sì**.

   ![Abilitare l'identità assegnata dal sistemaEnable the system-assigned identity](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Se viene visualizzato un errore che indica che è possibile avere una sola identità gestita, l'app per la logica è già associata all'identità assegnata dall'utente. Prima di poter aggiungere l'identità assegnata dal sistema, è necessario *rimuovere* l'identità assegnata dall'utente dall'app per la logica.

   L'app per la logica può ora usare l'identità assegnata dal sistema, che è registrata con Azure Active Directory ed è rappresentata da un ID oggetto.

   ![ID oggetto per l'identità assegnata dal sistema](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Proprietà | valore | Descrizione |
   |----------|-------|-------------|
   | **ID oggetto** | <*identità-resource-ID*> | Un identificatore univoco globale (GUID) che rappresenta l'identità assegnata dal sistema per l'app per la logica in un tenant di Azure ADA Globally Unique Identifier (GUID) that represents the system-assigned identity for your logic app in an Azure AD tenant |
   ||||

1. Seguire ora i [passaggi che concedono a tale identità l'accesso alla risorsa](#access-other-resources) più avanti in questo argomento.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Abilitare l'identità assegnata dal sistema nel modello di Azure Resource ManagerEnable system-assigned identity in Azure Resource Manager template

Per automatizzare la creazione e la distribuzione di risorse di Azure, ad esempio app per la logica, è possibile usare i modelli di [Azure Resource Manager.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) Per abilitare l'identità gestita assegnata dal sistema `identity` per `type` l'app per la logica nel modello, aggiungere l'oggetto e la proprietà figlio alla definizione di risorsa dell'app per la logica nel modello, ad esempio:To enable the system-assigned managed identity for your logic app in the template, add the object and the child property to the logic app's resource definition in the template, for example:

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

Quando Azure crea la definizione `identity` di risorsa dell'app per la logica, l'oggetto ottiene queste proprietà aggiuntive:When Azure creates your logic app resource definition, the object gets these additional properties:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property (JSON) | valore | Descrizione |
|-----------------|-------|-------------|
| `principalId` | <*ID-entità*> | Identificatore univoco globale (GUID) dell'oggetto entità servizio per l'identità gestita che rappresenta l'app per la logica nel tenant di Azure AD. Questo GUID viene talvolta visualizzato `objectID`come "ID oggetto" o . |
| `tenantId` | <*Azure-AD-tenant-ID*> | Identificatore univoco globale (GUID) che rappresenta il tenant di Azure AD in cui l'app per la logica è ora membro. All'interno del tenant di Azure AD, l'entità servizio ha lo stesso nome dell'istanza dell’app per la logica. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Abilitare l'identità assegnata dall'utenteEnable user-assigned identity

Per configurare un'identità gestita assegnata dall'utente per l'app per la logica, è innanzitutto necessario creare tale identità come risorsa di Azure autonoma separata. Di seguito sono riportate le opzioni che è possibile utilizzare:

* [Portale di Azure](#azure-portal-user-identity)
* [Modelli di Gestione risorse di Azure](#template-user-identity)
* Azure PowerShell
  * [Creare un'identità assegnata dall'utenteCreate user-assigned identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Aggiungi un'assegnazione di ruolo](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Interfaccia della riga di comando di Azure
  * [Creare un'identità assegnata dall'utenteCreate user-assigned identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Aggiungi un'assegnazione di ruolo](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* API REST di Azure
  * [Creare un'identità assegnata dall'utenteCreate user-assigned identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Aggiungi un'assegnazione di ruolo](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Creare un'identità assegnata dall'utente nel portale di AzureCreate user-assigned identity in the Azure portal

1. Nel [portale di Azure](https://portal.azure.com), nella casella `managed identities`di ricerca in qualsiasi pagina, immettere e selezionare **Identità gestite**.

   ![Trovare e selezionare "Identità gestite"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. In **Identità gestite**selezionare **Aggiungi**.

   ![Aggiungere una nuova identità gestitaAdd new managed identity](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Fornire informazioni sull'identità gestita e quindi selezionare **Crea**, ad esempio:

   ![Creare un'identità gestita assegnata dall'utenteCreate user-assigned managed identity](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Nome risorsa** | Sì | <*user-assigned-identity-name*> | Nome da assegnare all'identità assegnata dall'utente. In questo esempio viene utilizzata "Fabrikam-user-assigned-identity". |
   | **Sottoscrizione** | Sì | <*Nome sottoscrizione di AzureAzure-subscription-name*> | Nome della sottoscrizione di Azure da usare. |
   | **Gruppo di risorse** | Sì | <*Nome-gruppo-risorse di AzureAzure-resource-group-name*> | Nome del gruppo di risorse da utilizzare. Creare un nuovo gruppo o selezionarne uno esistente. In questo esempio viene creato un nuovo gruppo denominato "fabrikam-managed-identities-RG". |
   | **Percorso** | Sì | <*Area di AzureAzure-region*> | Area di Azure in cui archiviare le informazioni sulla risorsa. Questo esempio usa "Stati Uniti occidentali". |
   |||||

   A questo punto è possibile aggiungere l'identità assegnata dall'utente all'app per la logica. Non è possibile aggiungere più di un'identità assegnata dall'utente all'app per la logica.

1. Nel portale di Azure individuare e aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Identità**, quindi Selezionare**Aggiungi** **assegnato dall'utente** > .

   ![Aggiungere l'identità gestita assegnata dall'utenteAdd user-assigned managed identity](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. Nel riquadro **Aggiungi identità gestita assegnata dall'utente** selezionare la sottoscrizione di Azure nell'elenco **Sottoscrizione,** se non è già selezionata. Nell'elenco che mostra *tutte le* identità gestite nella sottoscrizione individuare e selezionare l'identità assegnata dall'utente desiderata. Per filtrare l'elenco, nella casella di ricerca **Identità gestite assegnate dall'utente** immettere il nome dell'identità o del gruppo di risorse. Al termine, selezionare **Aggiungi**.

   ![Selezionare l'identità assegnata dall'utente da utilizzare](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Se viene visualizzato un errore che indica che è possibile avere una sola identità gestita, l'app per la logica è già associata all'identità assegnata dal sistema. Prima di poter aggiungere l'identità assegnata dall'utente, è necessario disabilitare l'identità assegnata dal sistema nell'app per la logica.

   L'app per la logica è ora associata all'identità gestita assegnata dall'utente.

   ![Associazione con l'identità assegnata dall'utente](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Seguire ora i [passaggi che concedono a tale identità l'accesso alla risorsa](#access-other-resources) più avanti in questo argomento.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Creare un'identità assegnata dall'utente in un modello di Azure Resource ManagerCreate user-assigned identity in an Azure Resource Manager template

Per automatizzare la creazione e la distribuzione di risorse di Azure, ad esempio app per la logica, è possibile usare i modelli di [Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), che supportano le [identità assegnate dall'utente per l'autenticazione.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) Nella sezione del `resources` modello, la definizione delle risorse dell'app per la logica richiede questi elementi:In your template's section, your logic app's resource definition requires these items:

* Un `identity` oggetto `type` con la proprietà impostata su`UserAssigned`

* Oggetto `userAssignedIdentities` figlio che specifica l'ID di risorsa dell'identità, `principalId` `clientId` ovvero un altro oggetto figlio con le proprietà e

Questo esempio mostra una definizione di risorsa dell'app per `identity` la logica per una richiesta HTTP PUT e include un oggetto senza parametri. Anche la risposta alla richiesta PUT e `identity` alla successiva operazione GET hanno questo oggetto:

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

| Property (JSON) | valore | Descrizione |
|-----------------|-------|-------------|
| `principalId` | <*ID-entità*> | Identificatore univoco globale (GUID) per l'identità gestita assegnata dall'utente nel tenant di Azure ADThe Globally Unique Identifier (GUID) for the user-assigned managed identity in the Azure AD tenant |
| `clientId` | <*ID client*> | Un identificatore univoco globale (GUID) per la nuova identità dell'app per la logica usata per le chiamate durante il runtime |
||||

Se il modello include anche la definizione di risorsa `identity` dell'identità gestita, è possibile parametrizzare l'oggetto. In questo esempio `userAssignedIdentities` viene illustrato `userAssignedIdentity` come l'oggetto figlio `variables` fa riferimento a una variabile definita nella sezione del modello. Questa variabile fa riferimento all'ID risorsa per l'identità assegnata dall'utente.

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

| Property (JSON) | valore | Descrizione |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-tenant-ID*> | Identificatore univoco globale (GUID, Globally Unique Identifier) che rappresenta il tenant di Azure AD in cui l'identità assegnata dall'utente è ora membro. All'interno del tenant di Azure AD, l'entità servizio ha lo stesso nome del nome di identità assegnato dall'utente. |
| `principalId` | <*ID-entità*> | Identificatore univoco globale (GUID) per l'identità gestita assegnata dall'utente nel tenant di Azure ADThe Globally Unique Identifier (GUID) for the user-assigned managed identity in the Azure AD tenant |
| `clientId` | <*ID client*> | Un identificatore univoco globale (GUID) per la nuova identità dell'app per la logica usata per le chiamate durante il runtime |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Concedere l'accesso di identità alle risorse

Prima di poter usare l'identità gestita dell'app per la logica per l'autenticazione, configurare l'accesso per tale identità nella risorsa di Azure in cui si prevede di usare l'identità. Per completare questa attività, assegnare il ruolo appropriato a tale identità nella risorsa di Azure di destinazione. Di seguito sono riportate le opzioni che è possibile utilizzare:

* [Portale di Azure](#azure-portal-assign-access)
* [Modello di Azure Resource ManagerAzure Resource Manager template](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ( New-AzRoleAssignment ) - Per altre informazioni, vedere Aggiungere assegnazioni di ruolo tramite Controllo degli accessi in base al ruolo di [Azure e Azure PowerShell.Azure](../role-based-access-control/role-assignments-powershell.md)PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)) - For more information, see Add role assignment by using Azure RBAC and Azure PowerShell.
* Interfaccia di comando di Azure ( az role assignment create ) - Per altre informazioni, vedere [Aggiungere un'assegnazione di ruolo tramite Controllo degli accessi](../role-based-access-control/role-assignments-cli.md)in base al ruolo di Azure e l'interfaccia della riga di comando di Azure.Azure CLI ([az role assignment create](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) - For more information, see Add role assignment by using Azure RBAC and Azure CLI .
* [API REST di Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Assegnare l'accesso nel portale di AzureAssign access in the Azure portal

1. Nel [portale di Azure](https://portal.azure.com)passare alla risorsa di Azure a cui si vuole che l'identità gestita abbia accesso.

1. Dal menu della risorsa, selezionare**Assegnazioni di** ruolo di controllo di **accesso (IAM)** > in cui è possibile esaminare le assegnazioni di ruolo correnti per tale risorsa. Sulla barra degli strumenti selezionare **Aggiungi** > **assegnazione ruolo**.

   ![Selezionare "Aggiungi" > "Aggiungi assegnazione ruolo"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Se l'opzione **Aggiungi assegnazione ruolo** è disabilitata, molto probabilmente non si dispone delle autorizzazioni. Per altre informazioni sulle autorizzazioni che consentono di gestire i ruoli per le risorse, vedere Autorizzazioni del [ruolo di amministratore in Azure Active Directory.For](../active-directory/users-groups-roles/directory-assign-admin-roles.md)more information about the permissions that let you manage roles for resources, see Administrator role permissions in Azure Active Directory .

1. In **Aggiungi assegnazione ruolo**selezionare un **ruolo** che conceda all'identità l'accesso necessario alla risorsa di destinazione.

   Per l'esempio di questo argomento, l'identità richiede un [ruolo che possa accedere al BLOB in un contenitore di Archiviazione di Azure.For](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)this topic's example, your identity needs a role that can access the blob in an Azure Storage container.

   ![Selezionare il ruolo "Collaboratore dati BLOB di archiviazione"Select "Storage Blob Data Contributor" role](./media/create-managed-service-identity/select-role-for-identity.png)

1. Seguire questi passaggi per l'identità gestita:Follow these steps for your managed identity:

   * **Identità assegnata dal sistema**

     1. Nella casella **Assegna accesso a** selezionare App per la **logica**. Quando viene visualizzata la proprietà **Sottoscrizione,** selezionare la sottoscrizione di Azure associata all'identità.

        ![Selezionare l'accesso per l'identità assegnata dal sistemaSelect access for system-assigned identity](./media/create-managed-service-identity/assign-access-system.png)

     1. Nella casella **Seleziona** selezionare l'app per la logica dall'elenco. Se l'elenco è troppo lungo, utilizzare la casella Seleziona per **filtrare** l'elenco.

        ![Selezionare l'app per la logica per l'identità assegnata dal sistemaSelect logic app for system-assigned identity](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Identità assegnata dall'utente**

     1. Nella casella **Assegna accesso a** selezionare **Identità gestita assegnata dall'utente**. Quando viene visualizzata la proprietà **Sottoscrizione,** selezionare la sottoscrizione di Azure associata all'identità.

        ![Selezionare l'accesso per l'identità assegnata dall'utenteSelect access for user-assigned identity](./media/create-managed-service-identity/assign-access-user.png)

     1. Nella casella **Seleziona** selezionare l'identità dell'elenco. Se l'elenco è troppo lungo, utilizzare la casella Seleziona per **filtrare** l'elenco.

        ![Selezionare l'identità assegnata dall'utente](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Al termine, selezionare **Salva**.

   L'elenco delle assegnazioni di ruolo della risorsa di destinazione ora mostra l'identità gestita e il ruolo selezionati. Questo esempio mostra come usare l'identità assegnata dal sistema per un'app per la logica e un'identità assegnata dall'utente per un gruppo di altre app per la logica.

   ![Aggiunte identità e ruoli gestiti alla risorsa di destinazioneAdded managed identities and roles to target resource](./media/create-managed-service-identity/added-roles-for-identities.png)

   Per altre informazioni, [assegnare a un'identità gestita l'accesso a una risorsa tramite il portale](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)di Azure .

1. Seguire ora i [passaggi per autenticare l'accesso con l'identità](#authenticate-access-with-identity) in un trigger o in un'azione che supporta le identità gestite.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autenticare l'accesso con identità gestitaAuthenticate access with managed identity

Dopo aver [abilitato l'identità gestita per l'app per la logica](#azure-portal-system-logic-app) e aver conto a tale [identità l'accesso alla risorsa o all'entità](#access-other-resources)di destinazione, è possibile usare tale identità nei trigger e nelle azioni che [supportano le identità gestite.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

> [!IMPORTANT]
> Se si dispone di una funzione di Azure in cui si vuole usare l'identità assegnata dal sistema, abilitare innanzitutto [l'autenticazione per](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)le funzioni di Azure.If you have an Azure function where you want to use the system-assigned identity, first enable authentication for Azure functions .

Questi passaggi illustrano come usare l'identità gestita con un trigger o un'azione tramite il portale di Azure.These steps show how to use the managed identity with a trigger or action through the Azure portal. Per specificare l'identità gestita in una definizione JSON sottostante di un trigger o di un'azione, vedere [Autenticazione dell'identità gestita](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Se non è ancora stato fatto, aggiungere il [trigger o l'azione che supporta le identità gestite.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

   Ad esempio, il trigger o l'azione HTTP può usare l'identità assegnata dal sistema abilitata per l'app per la logica. In generale, il trigger o l'azione HTTP usa queste proprietà per specificare la risorsa o l'entità a cui si desidera accedere:

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Metodo** | Sì | Il metodo HTTP utilizzato dall'operazione che si desidera eseguire |
   | **URI** | Sì | URL dell'endpoint per l'accesso alla risorsa o all'entità di Azure di destinazione. La sintassi URI include in genere [l'ID risorsa](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) per la risorsa o il servizio di Azure.The URI syntax usually includes the resource ID for the Azure resource or service. |
   | **Intestazioni** | No | Tutti i valori di intestazione che si desidera o si desidera includere nella richiesta in uscita, ad esempio il tipo di contenuto |
   | **Query** | No | Tutti i parametri di query che è necessario o che si desidera includere nella richiesta, ad esempio il parametro per un'operazione specifica o la versione API per l'operazione che si desidera eseguire |
   | **Autenticazione** | Sì | Tipo di autenticazione da utilizzare per l'autenticazione dell'accesso alla risorsa o all'entità di destinazione |
   ||||

   Come esempio specifico, si supponga di voler eseguire [l'operazione BLOB snapshot](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) in un BLOB nell'account di archiviazione di Azure in cui è stato precedentemente configurato l'accesso per l'identità. Tuttavia, il [connettore di archiviazione BLOB](https://docs.microsoft.com/connectors/azureblob/) di Azure non offre attualmente questa operazione. È invece possibile eseguire questa operazione usando [l'azione HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) o un'altra [operazione dell'API REST](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)del servizio BLOB.

   > [!IMPORTANT]
   > Per accedere agli account di archiviazione di Azure protetti da firewall tramite richieste HTTP e identità gestite, assicurarsi di configurare anche l'account di archiviazione con [l'eccezione che consente l'accesso tramite servizi Microsoft attendibili.](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)

   Per eseguire [l'operazione BLOB snapshot,](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)l'azione HTTP specifica queste proprietà:

   | Proprietà | Obbligatoria | Valore di esempio | Descrizione |
   |----------|----------|---------------|-------------|
   | **Metodo** | Sì | `PUT`| Il metodo HTTP utilizzato dall'operazione blob snapshot |
   | **URI** | Sì | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | ID risorsa per un file di archiviazione BLOB di Azure nell'ambiente Azure Global (pubblico) che usa questa sintassi |
   | **Intestazioni** | Sì, per Archiviazione di Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | I `x-ms-blob-type` `x-ms-version` valori dell'intestazione e necessari per le operazioni di Archiviazione di Azure.The e header values that are required for Azure Storage operations. <p><p>**Importante:** nelle richieste di trigger e azione HTTP `x-ms-version` in uscita per Archiviazione di Azure l'intestazione richiede la proprietà e la versione dell'API per l'operazione che si vuole eseguire. <p>Per altre informazioni, vedere gli argomenti seguenti: <p><p>- [Intestazioni di richiesta - BLOB snapshotRequest headers - Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Controllo delle versioni per i servizi di archiviazione di AzureVersioning for Azure Storage services](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Query** | Sì, per questa operazione | `comp` = `snapshot` | Nome e valore del parametro di query per l'operazione BLOB di snapshot. |
   |||||

   Di seguito è riportato l'azione HTTP di esempio che mostra tutti questi valori di proprietà:Here is the example HTTP action that shows all these property values:

   ![Aggiungere un'azione HTTP per accedere a una risorsa di AzureAdd an HTTP action to access an Azure resource](./media/create-managed-service-identity/http-action-example.png)

1. Aggiungere ora la proprietà **Authentication** all'azione HTTP. Nell'elenco **Aggiungi nuovo parametro** selezionare **Autenticazione**.

   ![Aggiungere la proprietà "Autenticazione" all'azione HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Non tutti i trigger e le azioni supportano l'aggiunta di un tipo di autenticazione. Per ulteriori informazioni, vedere [Aggiungere l'autenticazione alle chiamate in uscita.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Nell'elenco **Tipo di autenticazione** selezionare **Identità gestita**.

   ![Per "Autenticazione", selezionare "Identità gestita"](./media/create-managed-service-identity/select-managed-identity.png)

1. Nell'elenco delle identità gestite selezionare una delle opzioni disponibili in base allo scenario.

   * Se si imposta l'identità assegnata dal sistema, selezionare **Identità gestita assegnata** sistema se non è già selezionata.

     ![Selezionare "Identità gestita assegnata dal sistema"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Se si imposta un'identità assegnata dall'utente, selezionare tale identità, se non è già selezionata.

     ![Selezionare l'identità assegnata dall'utente](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Questo esempio continua con **l'identità gestita assegnata dal**sistema .

1. In alcuni trigger e azioni, la proprietà **Audience** viene visualizzata anche per impostare l'ID risorsa di destinazione. Impostare la proprietà **Audience** sull'ID risorsa per la risorsa o il servizio di [destinazione.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) In caso contrario, per `https://management.azure.com/` impostazione predefinita, la proprietà Audience usa l'ID risorsa, ovvero l'ID risorsa per Azure Resource Manager.Otherwise, by default, the **Audience** property uses the resource ID, which is the resource ID for Azure Resource Manager.

   > [!IMPORTANT]
   > Assicurarsi che l'ID della risorsa di destinazione *corrisponda esattamente* al valore previsto da Azure Active Directory (AD), incluse eventuali barre finali necessarie. Ad esempio, l'ID risorsa per tutti gli account di archiviazione BLOB di Azure richiede una barra finale. Tuttavia, l'ID risorsa per un account di archiviazione specifico non richiede una barra finale. Controllare gli ID risorsa per i servizi di [Azure che supportano Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

   In questo **Audience** esempio la `https://storage.azure.com/` proprietà Audience viene impostata su in modo che i token di accesso usati per l'autenticazione siano validi per tutti gli account di archiviazione. Tuttavia, è anche possibile specificare l'URL del servizio radice, `https://fabrikamstorageaccount.blob.core.windows.net`, per un account di archiviazione specifico.

   ![Impostare la proprietà "Audience" su target per l'ID risorsa](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Per altre informazioni sull'autorizzazione dell'accesso con Azure AD per Archiviazione di Azure, vedere gli argomenti seguenti:For more information about authorizing access with Azure AD for Azure Storage, see these topics:

   * [Autorizzare l'accesso ablob e code di Azure usando Azure Active DirectoryAuthorize access to Azure blobs and queues by using Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorizzare l'accesso ad Archiviazione di Azure con Azure Active DirectoryAuthorize access to Azure Storage with Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Continuare a compilare l'app per la logica nel modo desiderato.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Disabilitare l'identità gestitaDisable managed identity

Per interrompere l'uso di un'identità gestita per l'app per la logica, sono disponibili le opzioni seguenti:To stop using a managed identity for your logic app, you have these options:

* [Portale di Azure](#azure-portal-disable)
* [Modelli di Gestione risorse di Azure](#template-disable)
* Azure PowerShell
  * [Rimuovere l'assegnazione di ruolo](../role-based-access-control/role-assignments-powershell.md)
  * [Eliminare l'identità assegnata dall'utenteDelete user-assigned identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Interfaccia della riga di comando di Azure
  * [Rimuovere l'assegnazione di ruolo](../role-based-access-control/role-assignments-cli.md)
  * [Eliminare l'identità assegnata dall'utenteDelete user-assigned identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* API REST di Azure
  * [Rimuovere l'assegnazione di ruolo](../role-based-access-control/role-assignments-rest.md)
  * [Eliminare l'identità assegnata dall'utenteDelete user-assigned identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Se si elimina l'app per la logica, Azure rimuove automaticamente l'identità gestita da Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Disabilitare l'identità gestita nel portale di AzureDisable managed identity in the Azure portal

Nel portale di Azure rimuovere innanzitutto l'accesso dell'identità alla [risorsa di destinazione.](#disable-identity-target-resource) Disattivare quindi l'identità assegnata dal sistema o rimuovere l'identità assegnata dall'utente [dall'app per la logica.](#disable-identity-logic-app)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Rimuovere l'accesso alle identità dalle risorseRemove identity access from resources

1. Nel [portale di Azure](https://portal.azure.com)passare alla risorsa di Azure di destinazione in cui si vuole rimuovere l'accesso per l'identità gestita.

1. Dal menu della risorsa di destinazione, selezionare **Controllo di accesso (IAM)**. Nella barra degli strumenti selezionare **Assegnazioni ruolo**.

1. Nell'elenco dei ruoli selezionare le identità gestite che si desidera rimuovere. Sulla barra degli strumenti selezionare **Rimuovi**.

   > [!TIP]
   > Se l'opzione **Rimuovi** è disabilitata, molto probabilmente non si dispone delle autorizzazioni. Per altre informazioni sulle autorizzazioni che consentono di gestire i ruoli per le risorse, vedere Autorizzazioni del [ruolo di amministratore in Azure Active Directory.For](../active-directory/users-groups-roles/directory-assign-admin-roles.md)more information about the permissions that let you manage roles for resources, see Administrator role permissions in Azure Active Directory .

L'identità gestita è stata rimossa e non ha più accesso alla risorsa di destinazione.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Disabilitare l'identità gestita nell'app per la logicaDisable managed identity on logic app

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Identità**, quindi seguire i passaggi per l'identità:

   * Selezionare **Sistema assegnato** > **al** > **salvataggio**. Quando viene richiesto da Azure di confermare, selezionare **Sì**.

     ![Disabilitare l'identità assegnata dal sistema](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Selezionare **Assegnato dall'utente** e l'identità gestita, quindi scegliere **Rimuovi**. Quando viene richiesto da Azure di confermare, selezionare **Sì**.

     ![Rimuovere l'identità assegnata dall'utente](./media/create-managed-service-identity/remove-user-assigned-identity.png)

L'identità gestita è ora disabilitata nell'app per la logica.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Disabilitare l'identità gestita nel modello di Azure Resource ManagerDisable managed identity in Azure Resource Manager template

Se è stata creata l'identità gestita dell'app `identity` per la `type` logica `None`usando un modello di Azure Resource Manager, impostare la proprietà figlio dell'oggetto su . Per l'identità gestita dal sistema, questa azione elimina anche l'ID entità da Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Accesso sicuro e dati nelle app per la logica di AzureSecure access and data in Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)