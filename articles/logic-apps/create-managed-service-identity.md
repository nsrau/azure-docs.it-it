---
title: Eseguire l'autenticazione con le identità gestite
description: Accedere alle risorse in altri tenant Azure Active Directory senza accedere con credenziali o segreti usando un'identità gestita
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
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autenticare l'accesso alle risorse di Azure usando identità gestite in app per la logica di Azure

Per accedere alle risorse in altri tenant di Azure Active Directory (Azure AD) e autenticare l'identità senza eseguire l'accesso, l'app per la logica può usare un' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) (in precedenza identità del servizio gestita o MSI), anziché le credenziali o i segreti. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti.

App per la logica di Azure supporta le identità gestite sia [*assegnate dal sistema*](../active-directory/managed-identities-azure-resources/overview.md) che [*dall'utente*](../active-directory/managed-identities-azure-resources/overview.md) . L'app per la logica può usare l'identità assegnata dal sistema o una *singola* identità assegnata dall'utente, che può essere condivisa in un gruppo di app per la logica, ma non entrambe. Attualmente, solo [trigger e azioni predefiniti specifici supportano le](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) identità gestite, non i connettori gestiti o le connessioni, ad esempio:

* HTTP
* Funzioni di Azure
* Gestione API di Azure
* Servizi app di Azure

Questo articolo illustra come configurare entrambi i tipi di identità gestite per l'app per la logica. Per altre informazioni, vedere gli argomenti seguenti:

* [Trigger e azioni che supportano identità gestite](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Tipi di autenticazione supportati per le chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limiti delle identità gestite per le app per la logica](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Servizi di Azure che supportano l'autenticazione Azure AD con identità gestite](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). Sia l'identità gestita che la risorsa di Azure di destinazione in cui è necessario accedere devono usare la stessa sottoscrizione di Azure.

* Per concedere a un'identità gestita l'accesso a una risorsa di Azure, è necessario aggiungere un ruolo alla risorsa di destinazione per tale identità. Per aggiungere ruoli, è necessario [Azure ad autorizzazioni di amministratore](../active-directory/users-groups-roles/directory-assign-admin-roles.md) che possono assegnare ruoli alle identità nel tenant Azure ad corrispondente.

* Risorsa di Azure di destinazione a cui si vuole accedere. In questa risorsa si aggiungerà un ruolo per l'identità gestita, che consente all'app per la logica di autenticare l'accesso alla risorsa di destinazione.

* App per la logica in cui si vuole usare il [trigger o le azioni che supportano le identità gestite](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Abilitare un'identità gestita

Per configurare l'identità gestita che si vuole usare, seguire il collegamento per l'identità:

* [Identità assegnata dal sistema](#system-assigned)
* [Identità assegnata dall'utente](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Abilitare l'identità assegnata dal sistema

Diversamente dalle identità assegnate dall'utente, non è necessario creare manualmente l'identità assegnata dal sistema. Per configurare l'identità assegnata dal sistema per l'app per la logica, di seguito sono riportate le opzioni che è possibile usare:

* [Azure portal](#azure-portal-system-logic-app)
* [Modelli di Gestione risorse di Azure](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Abilitare l'identità assegnata dal sistema in portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Identità**. Selezionare il **sistema assegnato** > **al** > **salvataggio**. Quando Azure chiede di confermare, selezionare **Sì**.

   ![Abilitare l'identità assegnata dal sistema](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Se viene ricevuto un errore che può avere una sola identità gestita, l'app per la logica è già associata all'identità assegnata dall'utente. Prima di poter aggiungere l'identità assegnata dal sistema, è necessario prima *rimuovere* l'identità assegnata dall'utente dall'app per la logica.

   L'app per la logica può ora usare l'identità assegnata dal sistema, che è registrata con Azure Active Directory ed è rappresentata da un ID oggetto.

   ![ID oggetto per l'identità assegnata dal sistema](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **ID oggetto** | <*Identity-Resource-ID*> | Identificatore univoco globale (GUID) che rappresenta l'identità assegnata dal sistema per l'app per la logica in un tenant di Azure AD |
   ||||

1. A questo punto, seguire i [passaggi che consentono all'identità di accedere alla risorsa](#access-other-resources) più avanti in questo argomento.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Abilitare l'identità assegnata dal sistema nel modello di Azure Resource Manager

Per automatizzare la creazione e la distribuzione di risorse di Azure, ad esempio app per la logica, è possibile usare [modelli di Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Per abilitare l'identità gestita assegnata dal sistema per l'app per la logica nel modello, `identity` aggiungere l'oggetto `type` e la proprietà figlio alla definizione di risorsa dell'app per la logica nel modello, ad esempio:

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

Quando Azure crea la definizione di risorsa dell'app per `identity` la logica, l'oggetto ottiene le proprietà aggiuntive seguenti:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property (JSON) | Valore | Descrizione |
|-----------------|-------|-------------|
| `principalId` | <*ID entità*> | Identificatore univoco globale (GUID) dell'oggetto entità servizio per l'identità gestita che rappresenta l'app per la logica nel tenant del Azure AD. Questo GUID viene talvolta visualizzato come "ID oggetto" o `objectID`. |
| `tenantId` | <*Azure-AD-Tenant-ID*> | Identificatore univoco globale (GUID) che rappresenta il tenant del Azure AD in cui l'app per la logica è ora un membro. All'interno del tenant di Azure AD, l'entità servizio ha lo stesso nome dell'istanza dell’app per la logica. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Abilitare l'identità assegnata dall'utente

Per configurare un'identità gestita assegnata dall'utente per l'app per la logica, è prima necessario creare tale identità come risorsa di Azure autonoma separata. Di seguito sono riportate le opzioni che è possibile usare:

* [Azure portal](#azure-portal-user-identity)
* [Modelli di Gestione risorse di Azure](#template-user-identity)
* Azure PowerShell
  * [Crea identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Aggiungi un'assegnazione di ruolo](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Interfaccia della riga di comando di Azure
  * [Crea identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Aggiungi un'assegnazione di ruolo](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* API REST di Azure
  * [Crea identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Aggiungi un'assegnazione di ruolo](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Crea identità assegnata dall'utente nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com), nella casella di ricerca di una pagina, immettere `managed identities`e selezionare **identità gestite**.

   ![Trovare e selezionare "identità gestite"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. In **identità gestite**selezionare **Aggiungi**.

   ![Aggiungi nuova identità gestita](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Fornire informazioni sull'identità gestita e quindi selezionare **Crea**, ad esempio:

   ![Crea identità gestita assegnata dall'utente](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Proprietà | Obbligatoria | Valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Nome risorsa** | Sì | <*nome-identità assegnato dall'utente*> | Nome da assegnare all'identità assegnata dall'utente. Questo esempio USA "Fabrikam-user-assigned-Identity". |
   | **Sottoscrizione** | Sì | <*Azure-Subscription-Name*> | Nome della sottoscrizione di Azure da usare. |
   | **Gruppo di risorse** | Sì | <*Azure-Resource-Group-Name*> | Nome del gruppo di risorse da usare. Crea un nuovo gruppo o seleziona un gruppo esistente. In questo esempio viene creato un nuovo gruppo denominato "Fabrikam-Managed-Identities-RG". |
   | **Posizione** | Sì | <*Azure-area*> | Area di Azure in cui archiviare le informazioni sulla risorsa. Questo esempio usa "Stati Uniti occidentali". |
   |||||

   A questo punto è possibile aggiungere l'identità assegnata dall'utente all'app per la logica. Non è possibile aggiungere più di un'identità assegnata dall'utente all'app per la logica.

1. Nel portale di Azure individuare e aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **identità**, quindi selezionare**Aggiungi** **utente assegnato** > .

   ![Aggiungi identità gestita assegnata dall'utente](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. Dall'elenco **sottoscrizione** del riquadro **Aggiungi identità gestita assegnata dall'utente** selezionare la sottoscrizione di Azure, se non è già selezionata. Dall'elenco che mostra *tutte* le identità gestite nella sottoscrizione, trovare e selezionare l'identità assegnata dall'utente desiderata. Per filtrare l'elenco, nella casella di ricerca **identità gestite assegnate dall'utente** immettere il nome per l'identità o il gruppo di risorse. Al termine, selezionare **Aggiungi**.

   ![Selezionare l'identità assegnata dall'utente da usare](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Se viene ricevuto un errore che può avere una sola identità gestita, l'app per la logica è già associata all'identità assegnata dal sistema. Prima di poter aggiungere l'identità assegnata dall'utente, è necessario disabilitare prima l'identità assegnata dal sistema nell'app per la logica.

   L'app per la logica è ora associata all'identità gestita assegnata dall'utente.

   ![Associazione con identità assegnata dall'utente](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. A questo punto, seguire i [passaggi che consentono all'identità di accedere alla risorsa](#access-other-resources) più avanti in questo argomento.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Creare un'identità assegnata dall'utente in un modello di Azure Resource Manager

Per automatizzare la creazione e la distribuzione di risorse di Azure, ad esempio app per la logica, è possibile usare [modelli di Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)che supportano le [identità assegnate dall'utente per l'autenticazione](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). Nella `resources` sezione del modello, la definizione di risorsa dell'app per la logica richiede gli elementi seguenti:

* `identity` Oggetto con la `type` proprietà impostata su.`UserAssigned`

* Oggetto figlio `userAssignedIdentities` che specifica l'ID risorsa dell'identità, ovvero un altro oggetto figlio con le `principalId` proprietà e. `clientId`

Questo esempio illustra una definizione di risorsa dell'app per la logica per una richiesta HTTP PUT e include `identity` un oggetto senza parametri. La risposta alla richiesta PUT e all'operazione GET successiva ha anche questo `identity` oggetto:

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

| Property (JSON) | Valore | Descrizione |
|-----------------|-------|-------------|
| `principalId` | <*ID entità*> | Identificatore univoco globale (GUID) per l'identità gestita assegnata dall'utente nel tenant di Azure AD |
| `clientId` | <*ID client*> | Identificatore univoco globale (GUID) per la nuova identità dell'app per la logica usata per le chiamate durante il runtime |
||||

Se il modello include anche la definizione di risorsa dell'identità gestita, è possibile parametrizzare l' `identity` oggetto. Questo esempio Mostra come l'oggetto `userAssignedIdentities` figlio fa riferimento `userAssignedIdentity` a una variabile definita nella `variables` sezione del modello. Questa variabile fa riferimento all'ID risorsa per l'identità assegnata dall'utente.

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

| Property (JSON) | Valore | Descrizione |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-Tenant-ID*> | Identificatore univoco globale (GUID) che rappresenta il tenant del Azure AD in cui l'identità assegnata dall'utente è ora un membro. All'interno del tenant di Azure AD, l'entità servizio ha lo stesso nome del nome di identità assegnato dall'utente. |
| `principalId` | <*ID entità*> | Identificatore univoco globale (GUID) per l'identità gestita assegnata dall'utente nel tenant di Azure AD |
| `clientId` | <*ID client*> | Identificatore univoco globale (GUID) per la nuova identità dell'app per la logica usata per le chiamate durante il runtime |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Fornire l'accesso alle identità alle risorse

Prima di poter usare l'identità gestita dell'app per la logica per l'autenticazione, configurare l'accesso per tale identità nella risorsa di Azure in cui si prevede di usare l'identità. Per completare questa attività, assegnare il ruolo appropriato a tale identità nella risorsa di Azure di destinazione. Di seguito sono riportate le opzioni che è possibile usare:

* [Azure portal](#azure-portal-assign-access)
* [Modello di Azure Resource Manager](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)): per altre informazioni, vedere Aggiungere un'assegnazione di ruolo usando il controllo degli accessi in base al [ruolo e Azure PowerShell di Azure](../role-based-access-control/role-assignments-powershell.md).
* INTERFACCIA della riga di comando di Azure ([AZ Role Assignment create](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)): per altre informazioni, vedere [aggiungere un'assegnazione di ruolo usando RBAC di](../role-based-access-control/role-assignments-cli.md)Azure
* [API REST di Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Assegnare l'accesso nella portale di Azure

1. Nella [portale di Azure](https://portal.azure.com)passare alla risorsa di Azure in cui si vuole che l'identità gestita abbia accesso.

1. Dal menu della risorsa selezionare le**assegnazioni di ruolo** **controllo di accesso (IAM)** > in cui è possibile esaminare le assegnazioni di ruolo correnti per tale risorsa. Sulla barra degli strumenti selezionare **Aggiungi** > **assegnazione ruolo**.

   ![Selezionare "Aggiungi" > "Aggiungi assegnazione ruolo"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Se l'opzione **Aggiungi assegnazione ruolo** è disabilitata, è probabile che non si disponga delle autorizzazioni. Per ulteriori informazioni sulle autorizzazioni che consentono di gestire i ruoli per le risorse, vedere autorizzazioni per i ruoli di [amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. In **Aggiungi assegnazione ruolo**selezionare un **ruolo** che fornisca all'identità l'accesso necessario alla risorsa di destinazione.

   Per l'esempio di questo argomento, l'identità necessita di un [ruolo che possa accedere al BLOB in un contenitore di archiviazione di Azure](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

   ![Selezionare il ruolo "collaboratore dati BLOB di archiviazione"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Seguire questa procedura per l'identità gestita:

   * **Identità assegnata dal sistema**

     1. Nella casella **assegna accesso a** selezionare app per la **logica**. Quando viene visualizzata la proprietà **sottoscrizione** , selezionare la sottoscrizione di Azure associata all'identità.

        ![Selezionare l'accesso per l'identità assegnata dal sistema](./media/create-managed-service-identity/assign-access-system.png)

     1. Nella casella **Seleziona** selezionare l'app per la logica nell'elenco. Se l'elenco è troppo lungo, utilizzare la casella **Seleziona** per filtrare l'elenco.

        ![Selezionare l'app per la logica per l'identità assegnata dal sistema](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Identità assegnata dall'utente**

     1. Nella casella **assegna accesso a** selezionare **identità gestita assegnata dall'utente**. Quando viene visualizzata la proprietà **sottoscrizione** , selezionare la sottoscrizione di Azure associata all'identità.

        ![Selezionare l'accesso per l'identità assegnata dall'utente](./media/create-managed-service-identity/assign-access-user.png)

     1. Nella casella **Seleziona** selezionare la propria identità dall'elenco. Se l'elenco è troppo lungo, utilizzare la casella **Seleziona** per filtrare l'elenco.

        ![Selezionare l'identità assegnata dall'utente](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Al termine, selezionare **Salva**.

   L'elenco assegnazioni di ruolo della risorsa di destinazione Mostra ora l'identità e il ruolo gestiti selezionati. Questo esempio illustra come è possibile usare l'identità assegnata dal sistema per un'app per la logica e un'identità assegnata dall'utente per un gruppo di altre app per la logica.

   ![Aggiunta di identità e ruoli gestiti alla risorsa di destinazione](./media/create-managed-service-identity/added-roles-for-identities.png)

   Per altre informazioni, [assegnare a una risorsa l'accesso a un'identità gestita usando il portale di Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Seguire quindi la [procedura per autenticare l'accesso con l'identità](#authenticate-access-with-identity) in un trigger o in un'azione che supporta le identità gestite.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autenticare l'accesso con identità gestita

Dopo aver [abilitato l'identità gestita per l'app per la logica](#azure-portal-system-logic-app) e aver [dato a tale identità l'accesso alla risorsa o all'entità di destinazione](#access-other-resources), è possibile usare tale identità in [trigger e azioni che supportano le identità gestite](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Se si dispone di una funzione di Azure in cui si vuole usare l'identità assegnata dal sistema, [abilitare prima l'autenticazione per funzioni di Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Questi passaggi illustrano come usare l'identità gestita con un trigger o un'azione tramite il portale di Azure. Per specificare l'identità gestita nella definizione JSON sottostante di un trigger o di un'azione, vedere [autenticazione dell'identità gestita](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. Nella [portale di Azure](https://portal.azure.com)aprire l'app per la logica nella finestra di progettazione dell'app per la logica.

1. Se non è ancora stato fatto, aggiungere il [trigger o l'azione che supporta le identità gestite](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Ad esempio, il trigger o l'azione HTTP può usare l'identità assegnata dal sistema abilitata per l'app per la logica. In generale, il trigger o l'azione HTTP usa queste proprietà per specificare la risorsa o l'entità a cui si vuole accedere:

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Metodo** | Sì | Metodo HTTP usato dall'operazione che si vuole eseguire |
   | **URI** | Sì | URL dell'endpoint per l'accesso alla risorsa o all'entità di Azure di destinazione. La sintassi dell'URI include in genere l' [ID risorsa](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) per la risorsa o il servizio di Azure. |
   | **Headers** | No | Eventuali valori di intestazione necessari o che si desidera includere nella richiesta in uscita, ad esempio il tipo di contenuto |
   | **Query** | No | Tutti i parametri di query necessari o che si desidera includere nella richiesta, ad esempio il parametro per un'operazione specifica o la versione dell'API per l'operazione che si desidera eseguire |
   | **autenticazione** | Sì | Tipo di autenticazione da usare per autenticare l'accesso alla risorsa o all'entità di destinazione |
   ||||

   Come esempio specifico, si supponga di voler eseguire l' [operazione snapshot BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) su un BLOB nell'account di archiviazione di Azure in cui è stato precedentemente configurato l'accesso per l'identità. Tuttavia, il [connettore di archiviazione BLOB di Azure](https://docs.microsoft.com/connectors/azureblob/) attualmente non offre questa operazione. In alternativa, è possibile eseguire questa operazione tramite l' [azione http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) o un'altra [operazione API REST del servizio BLOB](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Per accedere agli account di archiviazione di Azure dietro i firewall usando le richieste HTTP e le identità gestite, assicurarsi di configurare anche l'account di archiviazione con l' [eccezione che consente l'accesso da parte di servizi Microsoft attendibili](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Per eseguire l' [operazione snapshot BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob), l'azione http specifica le proprietà seguenti:

   | Proprietà | Obbligatoria | Valore di esempio | Descrizione |
   |----------|----------|---------------|-------------|
   | **Metodo** | Sì | `PUT`| Metodo HTTP usato dall'operazione BLOB di snapshot |
   | **URI** | Sì | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | ID risorsa per un file di archiviazione BLOB di Azure nell'ambiente globale (pubblico) di Azure, che usa questa sintassi |
   | **Headers** | Sì, per archiviazione di Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Valori `x-ms-blob-type` dell' `x-ms-version` intestazione e richiesti per le operazioni di archiviazione di Azure. <p><p>**Importante**: nel trigger http in uscita e nelle richieste di azione per archiviazione di Azure, l' `x-ms-version` intestazione richiede la proprietà e la versione dell'API per l'operazione che si vuole eseguire. <p>Per altre informazioni, vedere gli argomenti seguenti: <p><p>- [Intestazioni della richiesta-BLOB di snapshot](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Controllo delle versioni per i servizi di archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Query** | Sì, per questa operazione | `comp` = `snapshot` | Nome e valore del parametro di query per l'operazione snapshot BLOB. |
   |||||

   Ecco l'azione HTTP di esempio che Mostra tutti i valori delle proprietà:

   ![Aggiungere un'azione HTTP per accedere a una risorsa di Azure](./media/create-managed-service-identity/http-action-example.png)

1. A questo punto, aggiungere la proprietà **Authentication** all'azione http. Dall'elenco **Aggiungi nuovo parametro** selezionare **autenticazione**.

   ![Aggiungere la proprietà "Authentication" all'azione HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Non tutti i trigger e le azioni supportano consentire l'aggiunta di un tipo di autenticazione. Per altre informazioni, vedere [aggiungere l'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Dall'elenco **tipo di autenticazione** selezionare **identità gestita**.

   ![Per "autenticazione" selezionare "identità gestita".](./media/create-managed-service-identity/select-managed-identity.png)

1. Dall'elenco identità gestite selezionare una delle opzioni disponibili in base allo scenario.

   * Se si configura l'identità assegnata dal sistema, selezionare l' **identità gestita assegnata** dal sistema, se non è già selezionata.

     ![Selezionare "identità gestita assegnata dal sistema"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Se si configura un'identità assegnata dall'utente, selezionare l'identità, se non è già selezionata.

     ![Selezionare l'identità assegnata dall'utente](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Questo esempio continua con l' **identità gestita assegnata dal sistema**.

1. Per alcuni trigger e azioni, viene visualizzata anche la proprietà **audience** per impostare l'ID della risorsa di destinazione. Impostare la proprietà **audience** sull' [ID risorsa per la risorsa o il servizio di destinazione](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). In caso contrario, per impostazione **Audience** predefinita, la proprietà `https://management.azure.com/` audience usa l'ID risorsa, che è l'ID risorsa per Azure Resource Manager.

   > [!IMPORTANT]
   > Verificare che l'ID risorsa di destinazione *corrisponda esattamente* al valore previsto da Azure Active Directory (ad), incluse le barre finali obbligatorie. Ad esempio, l'ID risorsa per tutti gli account di archiviazione BLOB di Azure richiede una barra finale. Tuttavia, l'ID risorsa per un account di archiviazione specifico non richiede una barra finale. Controllare gli [ID risorsa per i servizi di Azure che supportano Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Questo esempio imposta la proprietà **audience** su `https://storage.azure.com/` in modo che i token di accesso usati per l'autenticazione siano validi per tutti gli account di archiviazione. Tuttavia, è anche possibile specificare l'URL del servizio radice `https://fabrikamstorageaccount.blob.core.windows.net`,, per un account di archiviazione specifico.

   ![Imposta la proprietà "audience" sull'ID risorsa di destinazione](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Per ulteriori informazioni sull'autorizzazione dell'accesso con Azure AD per archiviazione di Azure, vedere gli argomenti seguenti:

   * [Autorizzare l'accesso a BLOB e code di Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorizzare l'accesso ad archiviazione di Azure con Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Continuare a compilare l'app per la logica nel modo desiderato.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Disabilitare l'identità gestita

Per interrompere l'uso di un'identità gestita per l'app per la logica, sono disponibili le opzioni seguenti:

* [Azure portal](#azure-portal-disable)
* [Modelli di Gestione risorse di Azure](#template-disable)
* Azure PowerShell
  * [Rimuovi assegnazione di ruolo](../role-based-access-control/role-assignments-powershell.md)
  * [Elimina identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Interfaccia della riga di comando di Azure
  * [Rimuovi assegnazione di ruolo](../role-based-access-control/role-assignments-cli.md)
  * [Elimina identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* API REST di Azure
  * [Rimuovi assegnazione di ruolo](../role-based-access-control/role-assignments-rest.md)
  * [Elimina identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Se si elimina l'app per la logica, Azure rimuove automaticamente l'identità gestita dal Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Disabilitare l'identità gestita nel portale di Azure

Nel portale di Azure rimuovere prima di tutto l'accesso dell'identità alla [risorsa di destinazione](#disable-identity-target-resource). Disabilitare quindi l'identità assegnata dal sistema o rimuovere l'identità assegnata dall'utente dall' [app per la logica](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Rimuovere l'accesso alle identità dalle risorse

1. Nella [portale di Azure](https://portal.azure.com)passare alla risorsa di Azure di destinazione in cui si vuole rimuovere l'accesso per l'identità gestita.

1. Selezionare **controllo di accesso (IAM)** dal menu della risorsa di destinazione. Nella barra degli strumenti selezionare **assegnazioni di ruolo**.

1. Nell'elenco ruoli selezionare le identità gestite che si desidera rimuovere. Sulla barra degli strumenti selezionare **Rimuovi**.

   > [!TIP]
   > Se l'opzione **Rimuovi** è disabilitata, è probabile che non si disponga delle autorizzazioni. Per ulteriori informazioni sulle autorizzazioni che consentono di gestire i ruoli per le risorse, vedere autorizzazioni per i ruoli di [amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

L'identità gestita viene ora rimossa e non ha più accesso alla risorsa di destinazione.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Disabilitare l'identità gestita nell'app per la logica

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **identità**, quindi seguire la procedura per l'identità:

   * Selezionare il **sistema assegnato** > **al** > **salvataggio**. Quando Azure chiede di confermare, selezionare **Sì**.

     ![Disabilitare l'identità assegnata dal sistema](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Selezionare **utente assegnato** e identità gestita, quindi selezionare **Rimuovi**. Quando Azure chiede di confermare, selezionare **Sì**.

     ![Rimuovere l'identità assegnata dall'utente](./media/create-managed-service-identity/remove-user-assigned-identity.png)

L'identità gestita è ora disabilitata nell'app per la logica.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Disabilitare l'identità gestita nel modello di Azure Resource Manager

Se è stata creata l'identità gestita dell'app per la logica usando un modello di Azure Resource Manager `identity` , impostare `type` la proprietà figlio `None`dell'oggetto su. Per l'identità gestita dal sistema, questa azione Elimina anche l'ID entità da Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere l'accesso e i dati in app per la logica di Azure](../logic-apps/logic-apps-securing-a-logic-app.md)