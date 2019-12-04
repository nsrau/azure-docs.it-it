---
title: Eseguire l'autenticazione con le identità gestite
description: Accedere alle risorse in altri tenant Azure Active Directory senza accedere con credenziali o segreti usando un'identità gestita
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: 49c925cfe61084d8fedfdf953d469db4bd2c10b1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792668"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autenticare l'accesso alle risorse di Azure usando identità gestite in app per la logica di Azure

Per accedere alle risorse in altri tenant di Azure Active Directory (Azure AD) e autenticare l'identità senza accedere, l'app per la logica può usare l' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) assegnata dal sistema (precedentemente nota come identità del servizio gestita o MSI), anziché con credenziali o segreti. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti. Questo articolo illustra come configurare e usare l'identità gestita assegnata dal sistema nell'app per la logica. Attualmente, le identità gestite funzionano solo con [trigger e azioni predefiniti specifici](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-to-outbound-calls), non con connessioni o connettori gestiti.

Per altre informazioni, vedere gli argomenti seguenti:

* [Trigger e azioni che supportano identità gestite](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Servizi di Azure che supportano l'autenticazione Azure AD con identità gestite](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [Tipi di autenticazione supportati per le chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limiti delle identità gestite per le app per la logica](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure, o in assenza di sottoscrizione, occorre eseguire una [iscrizione per creare un account Azure gratuito](https://azure.microsoft.com/free/). Sia l'identità gestita che la risorsa di Azure di destinazione in cui si vuole accedere devono usare la stessa sottoscrizione di Azure.

* [Azure ad autorizzazioni di amministratore](../active-directory/users-groups-roles/directory-assign-admin-roles.md) che possono assegnare ruoli alle identità gestite nello stesso tenant di Azure ad della risorsa di destinazione. Per concedere a un'identità gestita l'accesso a una risorsa di Azure, è necessario aggiungere un ruolo per tale identità nella risorsa di destinazione.

* Risorsa di Azure di destinazione a cui si vuole accedere

* App per la logica che usa [trigger e azioni che supportano identità gestite](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>Abilitare l'identità assegnata dal sistema

Diversamente dalle identità assegnate dall'utente, non è necessario creare manualmente l'identità assegnata dal sistema. Per configurare l'identità assegnata dal sistema dell'app per la logica, di seguito sono riportate le opzioni che è possibile usare:

* [Azure portal](#azure-portal-system-logic-app)
* [Modelli di Gestione risorse di Azure](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Abilitare l'identità assegnata dal sistema in portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Identity** > **System assegnato**. In **stato** **selezionare > ** **Salva** > **Sì**.

   ![Abilitare l'identità assegnata dal sistema](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   L'app per la logica può ora usare l'identità assegnata dal sistema, che è registrata con Azure Active Directory ed è rappresentata da un ID oggetto.

   ![ID oggetto per l'identità assegnata dal sistema](./media/create-managed-service-identity/object-id.png)

   | Proprietà | Value | Description |
   |----------|-------|-------------|
   | **ID oggetto** | <*identity-resource-ID*> | Identificatore univoco globale (GUID) che rappresenta l'identità assegnata dal sistema per l'app per la logica nel tenant di Azure AD |
   ||||

1. A questo punto, attenersi alla [procedura che consente di concedere all'identità l'accesso alla risorsa](#access-other-resources).

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Abilitare l'identità assegnata dal sistema nel modello di Azure Resource Manager

Per automatizzare la creazione e la distribuzione di risorse di Azure, ad esempio app per la logica, è possibile usare [modelli di Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Per abilitare l'identità gestita assegnata dal sistema per l'app per la logica nel modello, aggiungere l'oggetto `identity` e la proprietà `type` figlio alla definizione di risorsa dell'app per la logica nel modello, ad esempio:

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

Quando Azure crea la definizione di risorsa dell'app per la logica, l'oggetto `identity` ottiene le proprietà aggiuntive seguenti:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property (JSON) | Value | Description |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | Identificatore univoco globale (GUID) dell'oggetto entità servizio per l'identità gestita che rappresenta l'app per la logica nel tenant del Azure AD. Questo GUID viene talvolta visualizzato come "ID oggetto" o `objectID`. |
| `tenantId` | <*Azure-AD-tenant-ID*> | Identificatore univoco globale (GUID) che rappresenta il tenant del Azure AD in cui l'app per la logica è ora un membro. All'interno del tenant di Azure AD, l'entità servizio ha lo stesso nome dell'istanza dell’app per la logica. |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Fornire l'accesso alle identità alle risorse

Dopo aver configurato un'identità gestita per l'app per la logica, è possibile [concedere a tale identità l'accesso ad altre risorse di Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). È quindi possibile usare tale identità per l'autenticazione.

1. Nella [portale di Azure](https://portal.azure.com)passare alla risorsa di Azure in cui si vuole che l'identità gestita abbia accesso.

1. Dal menu della risorsa selezionare controllo di **accesso (IAM)**  > **assegnazioni di ruolo** in cui è possibile esaminare le assegnazioni di ruolo correnti per tale risorsa. Sulla barra degli strumenti selezionare **aggiungi** > **Aggiungi assegnazione ruolo**.

   ![Selezionare "Aggiungi" > "Aggiungi assegnazione ruolo"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Se l'opzione **Aggiungi assegnazione ruolo** è disabilitata, è probabile che non si disponga delle autorizzazioni. Per ulteriori informazioni sulle autorizzazioni che consentono di gestire i ruoli per le risorse, vedere autorizzazioni per i ruoli di [amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. In **Aggiungi assegnazione ruolo**selezionare un **ruolo** che fornisca all'identità l'accesso necessario alla risorsa di destinazione.

   Per l'esempio di questo argomento, l'identità necessita di un [ruolo che possa accedere al BLOB in un contenitore di archiviazione di Azure](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights):

   ![Selezionare il ruolo "collaboratore dati BLOB di archiviazione"](./media/create-managed-service-identity/assign-role.png)

1. Nella casella **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD**.

   ![Selezionare l'accesso per l'identità assegnata dal sistema](./media/create-managed-service-identity/assign-access-system.png)

1. Nella casella **Seleziona** trovare e selezionare l'app per la logica.

   ![Selezionare l'app per la logica per l'identità assegnata dal sistema](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Al termine, selezionare **Salva**.

   L'elenco assegnazioni di ruolo della risorsa di destinazione Mostra ora l'identità e il ruolo gestiti selezionati.

   ![Aggiunta di identità e ruoli gestiti alla risorsa di destinazione](./media/create-managed-service-identity/added-roles-for-identities.png)

1. Seguire quindi la [procedura per autenticare l'accesso con l'identità](#authenticate-access-with-identity) in un trigger o in un'azione che supporta le identità gestite.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autenticare l'accesso con identità gestita

Dopo aver [abilitato l'identità gestita per l'app per la logica](#azure-portal-system-logic-app) e aver [dato a tale identità l'accesso alla risorsa o all'entità di destinazione](#access-other-resources), è possibile usare tale identità in [trigger e azioni che supportano le identità gestite](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Se si dispone di una funzione di Azure in cui si vuole usare l'identità assegnata dal sistema, [abilitare prima l'autenticazione per funzioni di Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Questi passaggi illustrano come usare l'identità gestita con un trigger o un'azione tramite il portale di Azure. Per specificare l'identità gestita nella definizione JSON sottostante di un trigger o di un'azione, vedere [autenticazione dell'identità gestita](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Se non è ancora stato fatto, aggiungere il [trigger o l'azione che supporta le identità gestite](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Ad esempio, il trigger o l'azione HTTP può usare l'identità assegnata dal sistema abilitata per l'app per la logica. In generale, il trigger o l'azione HTTP usa queste proprietà per specificare la risorsa o l'entità a cui si vuole accedere:

   | Proprietà | Obbligatoria | Description |
   |----------|----------|-------------|
   | **Metodo** | SÌ | Metodo HTTP usato dall'operazione che si vuole eseguire |
   | **URI** | SÌ | URL dell'endpoint per l'accesso alla risorsa o all'entità di Azure di destinazione. La sintassi dell'URI include in genere l' [ID risorsa](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) per la risorsa o il servizio di Azure. |
   | **Intestazioni** | No | Eventuali valori di intestazione necessari o che si desidera includere nella richiesta in uscita, ad esempio il tipo di contenuto |
   | **Query** | No | Tutti i parametri di query necessari o che si desidera includere nella richiesta, ad esempio il parametro per un'operazione specifica o la versione dell'API per l'operazione che si desidera eseguire |
   | **Autenticazione** | SÌ | Tipo di autenticazione da usare per autenticare l'accesso alla risorsa o all'entità di destinazione |
   ||||

   Come esempio specifico, si supponga di voler eseguire l' [operazione snapshot BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) su un BLOB nell'account di archiviazione di Azure in cui è stato precedentemente configurato l'accesso per l'identità. Tuttavia, il [connettore di archiviazione BLOB di Azure](https://docs.microsoft.com/connectors/azureblob/) attualmente non offre questa operazione. In alternativa, è possibile eseguire questa operazione tramite l' [azione http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) o un'altra [operazione API REST del servizio BLOB](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Per accedere agli account di archiviazione di Azure dietro i firewall usando le richieste HTTP e le identità gestite, assicurarsi di configurare anche l'account di archiviazione con l' [eccezione che consente l'accesso da parte di servizi Microsoft attendibili](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Per eseguire l' [operazione snapshot BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob), l'azione http specifica le proprietà seguenti:

   | Proprietà | Obbligatoria | Valore di esempio | Description |
   |----------|----------|---------------|-------------|
   | **Metodo** | SÌ | `PUT`| Metodo HTTP usato dall'operazione BLOB di snapshot |
   | **URI** | SÌ | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | ID risorsa per un file di archiviazione BLOB di Azure nell'ambiente globale (pubblico) di Azure, che usa questa sintassi |
   | **Intestazioni** | Sì, per archiviazione di Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | I valori di intestazione `x-ms-blob-type` e `x-ms-version` richiesti per le operazioni di archiviazione di Azure. <p><p>**Importante**: nel trigger http in uscita e nelle richieste di azione per archiviazione di Azure, l'intestazione richiede la proprietà `x-ms-version` e la versione dell'API per l'operazione che si vuole eseguire. <p>Per altre informazioni, vedere gli argomenti seguenti: <p><p>[intestazioni di richiesta - -BLOB di snapshot](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>[controllo delle versioni di - per i servizi di archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Query** | Sì, per questa operazione | `comp` = `snapshot` | Nome e valore del parametro di query per l'operazione snapshot BLOB. |
   | **Autenticazione** | SÌ | `Managed Identity` | Tipo di autenticazione da usare per l'autenticazione dell'accesso al BLOB di Azure |
   |||||

   Ecco l'azione HTTP di esempio che Mostra tutti i valori delle proprietà:

   ![Aggiungere un'azione HTTP per accedere a una risorsa di Azure](./media/create-managed-service-identity/http-action-example.png)

   Per altre informazioni su tutte le operazioni API REST di Azure disponibili, vedere informazioni di [riferimento sull'API REST di Azure](https://docs.microsoft.com/rest/api/azure/).

1. Nell'elenco **Autenticazione** selezionare **Identità gestita**. Se la [proprietà di **autenticazione** è supportata](logic-apps-securing-a-logic-app.md#add-authentication-outbound) ma nascosta, aprire l'elenco **Aggiungi nuovo parametro** e selezionare **autenticazione**.

   > [!NOTE]
   > Non tutti i trigger e le azioni consentono di selezionare un tipo di autenticazione. Per altre informazioni, vedere [aggiungere l'autenticazione alle chiamate in uscita](logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![Nella proprietà "autenticazione" selezionare "identità gestita".](./media/create-managed-service-identity/select-managed-identity.png)

1. Dopo aver selezionato **identità gestita**, viene visualizzata la proprietà **audience** per alcuni trigger e azioni. Se la proprietà **audience** è supportata ma nascosta, aprire l'elenco **Aggiungi nuovo parametro** e selezionare **audience**.

1. Assicurarsi di impostare il valore **audience** sull'ID risorsa per la risorsa o il servizio di destinazione. In caso contrario, per impostazione predefinita, la proprietà **audience** usa il `https://management.azure.com/` ID risorsa, che è l'ID risorsa per Azure Resource Manager.

   > [!IMPORTANT]
   > Verificare che l'ID risorsa di destinazione *corrisponda esattamente* al valore previsto da Azure Active Directory (ad), incluse le barre finali obbligatorie. Ad esempio, l'ID risorsa per tutti gli account di archiviazione BLOB di Azure richiede una barra finale. Tuttavia, l'ID risorsa per un account di archiviazione specifico non richiede una barra finale. Controllare gli [ID risorsa per i servizi di Azure che supportano Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Questo esempio imposta la proprietà **audience** su `https://storage.azure.com/` in modo che i token di accesso usati per l'autenticazione siano validi per tutti gli account di archiviazione. Tuttavia, è anche possibile specificare l'URL del servizio radice, `https://fabrikamstorageaccount.blob.core.windows.net`, per un account di archiviazione specifico.

   ![Specificare l'ID risorsa di destinazione nella proprietà "audience"](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Per ulteriori informazioni sull'autorizzazione dell'accesso con Azure AD per archiviazione di Azure, vedere gli argomenti seguenti:

   * [Autorizzare l'accesso a BLOB e code di Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorizzare l'accesso ad archiviazione di Azure con Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>Rimuovi identità assegnata dal sistema

Per interrompere l'uso dell'identità assegnata dal sistema per l'app per la logica, sono disponibili le opzioni seguenti:

* [Azure portal](#azure-portal-disable)
* [Modelli di Gestione risorse di Azure](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

Se si elimina l'app per la logica, Azure rimuove automaticamente l'identità gestita dal Azure AD.

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>Rimuovere l'identità assegnata dal sistema nella portale di Azure

Nel portale di Azure rimuovere l'identità assegnata [dal sistema dall'app](#disable-identity-logic-app) per la logica e l'accesso dell'identità [dalla risorsa di destinazione](#disable-identity-target-resource).

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>Rimuovere l'identità assegnata dal sistema dall'app per la logica

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Identity** > **System assegnato**. In **stato**selezionare **disattivato** > **Salva** > **Sì**.

   ![Interrompi usando l'identità assegnata dal sistema](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Rimuovere l'accesso alle identità dalle risorse

1. Nella [portale di Azure](https://portal.azure.com)passare alla risorsa di Azure di destinazione in cui si vuole rimuovere l'accesso per un'identità gestita.

1. Selezionare **controllo di accesso (IAM)** dal menu della risorsa di destinazione. Nella barra degli strumenti selezionare **assegnazioni di ruolo**.

1. Nell'elenco ruoli selezionare le identità gestite che si desidera rimuovere. Sulla barra degli strumenti selezionare **Rimuovi**.

   > [!TIP]
   > Se l'opzione **Rimuovi** è disabilitata, è probabile che non si disponga delle autorizzazioni. Per ulteriori informazioni sulle autorizzazioni che consentono di gestire i ruoli per le risorse, vedere autorizzazioni per i ruoli di [amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

L'identità gestita viene ora rimossa e non ha più accesso alla risorsa di destinazione.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Disabilitare l'identità gestita nel modello di Azure Resource Manager

Se è stata abilitata l'identità gestita dal sistema dell'app per la logica usando un modello di Azure Resource Manager, impostare la proprietà figlio `type` dell'oggetto `identity` su `None`. Questa azione Elimina anche l'ID entità per l'identità gestita dal sistema da Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere l'accesso e i dati in app per la logica di Azure](../logic-apps/logic-apps-securing-a-logic-app.md)
