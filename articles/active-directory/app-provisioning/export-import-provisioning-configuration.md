---
title: Esportare la configurazione di provisioning ed eseguire il rollback a uno stato valido noto per il ripristino di emergenza. Documenti Microsoft
description: Informazioni su come esportare la configurazione di provisioning ed eseguire il rollback a uno stato valido noto per il ripristino di emergenza.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051303"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Esportare la configurazione di provisioning ed eseguire il rollback a uno stato valido noto

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Esportare e importare la configurazione di provisioning dal portale di AzureExport and import your provisioning configuration from the Azure portal

### <a name="how-can-i-export-my-provisioning-configuration"></a>Come posso esportare la mia configurazione di provisioning?
Per esportare la configurazione:
1. Nel [portale di Azure](https://portal.azure.com/) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.
2. Nel riquadro **Azure Active Directory** selezionare Applicazioni **aziendali** e scegliere l'applicazione.
3. Nel riquadro di spostamento sinistro selezionare **provisioning**. Dalla pagina di configurazione del provisioning, fare clic su **mapping di attributi**, quindi visualizzare le opzioni **avanzate**e infine **esaminare lo schema**. Verrà visualizzato l'editor di schema. 
5. Fare clic su download nella barra dei comandi nella parte superiore della pagina per scaricare lo schema.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Ripristino di emergenza - rollback a uno stato valido noto
L'esportazione e il salvataggio della configurazione consentono di ripristinare una versione precedente della configurazione. È consigliabile esportare la configurazione di provisioning e salvarla per un utilizzo successivo in qualsiasi momento se si apporta una modifica ai mapping degli attributi o ai filtri di ambito. È sufficiente aprire il file JSON scaricato nei passaggi precedenti, copiare l'intero contenuto del file JSON, sostituire l'intero contenuto del payload JSON nell'editor dello schema e quindi salvare. Se è presente un ciclo di provisioning attivo, verrà completato e il ciclo successivo utilizzerà lo schema aggiornato. Il ciclo successivo sarà anche un ciclo iniziale, che rivaluta ogni utente e gruppo in base alla nuova configurazione. Quando si esegue il rollback a una configurazione precedente, tenere presente quanto segue:Consider the following when roll back to a previous configuration:
* Gli utenti verranno valutati nuovamente per determinare se devono trovarsi nell'ambito. Se i filtri di ambito sono stati modificati, un utente non si trova più nell'ambito, verrà disabilitato. Anche se questo è il comportamento desiderato nella maggior parte dei casi, in alcuni casi è possibile evitare questo e può utilizzare la funzionalità [di ignorare le eliminazioni dell'ambito.](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions) 
* La modifica della configurazione di provisioning riavvia il servizio e attiva un [ciclo iniziale.](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental)


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Esportare e importare la configurazione di provisioning tramite l'API Microsoft Graph
È possibile usare l'API Microsoft Graph e Microsoft Graph Explorer per esportare i mapping e lo schema degli attributi di provisioning utente in un file JSON e importarlo nuovamente in Azure AD. È anche possibile usare i passaggi acquisiti qui per creare un backup della configurazione di provisioning. 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passaggio 1: Recuperare l'ID dell'entità servizio dell'app di provisioning (ID oggetto)Step 1: Retrieve your Provisioning App Service Principal ID (Object ID)

1. Avviare il portale di [Azure](https://portal.azure.com)e passare alla sezione Proprietà dell'applicazione di provisioning. Ad esempio, se si desidera esportare il mapping *dell'applicazione Workday in AD User Provisioning application* mapping, passare alla sezione Properties dell'app. 
1. Nella sezione delle proprietà dell'app di provisioning, copiare il valore GUID associato al campo *Object ID* (ID oggetto). Questo valore è anche denominato **ServicePrincipalId** dell'app e verrà usato nelle operazioni di Microsoft Graph Explorer.

   ![ID entità Servizio app Workday](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passaggio 2: Accedere a Microsoft Graph Explorer

1. Avviare [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Fare clic sul pulsante "Sign-In with Microsoft" (Accedi con Microsoft) e accedere con le credenziali di amministratore globale di AD o di amministratore dell'app.

    ![Accesso a Microsoft Graph](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Quando l'accesso è stato completato, vengono visualizzati i dettagli dell'account utente nel riquadro sinistro.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Passaggio 3: Recuperare l'ID del processo di provisioning dell'app di provisioningStep 3: Retrieve the Provisioning Job ID of the Provisioning App

In Microsoft Graph Explorer, eseguire la query GET seguente, sostituendo [servicePrincipalId] con il **ServicePrincipalId** estratto dal [Passaggio 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Si otterrà una risposta come illustrato di seguito. Copiare l'attributo"id" presente nella risposta. Questo valore è il **ProvisioningJobId** e verrà usato per recuperare i metadati dello schema sottostante.

   [![ID processo di provisioning](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Passaggio 4: Scaricare lo schema di provisioningStep 4: Download the Provisioning Schema

In Microsoft Graph Explorer, eseguire la query GET seguente, sostituendo [servicePrincipalId] e [ProvisioningJobId] con il ServicePrincipalId e il ProvisioningJobId recuperati nei passaggi precedenti.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copiare l'oggetto JSON dalla risposta e salvarlo in un file per creare un backup dello schema.

### <a name="step-5-import-the-provisioning-schema"></a>Passaggio 5: Importare lo schema di provisioningStep 5: Import the Provisioning Schema

> [!CAUTION]
> Eseguire questo passaggio solo se è necessario modificare lo schema per la configurazione che non può essere modificato usando il portale di Azure o se è necessario ripristinare la configurazione da un file di cui è stato eseguito il backup in precedenza con uno schema di lavoro valido.

In Microsoft Graph Explorer, eseguire la query PUT seguente, sostituendo [servicePrincipalId] e [ProvisioningJobId] con il ServicePrincipalId e il ProvisioningJobId recuperati nei passaggi precedenti.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Nella scheda "Corpo della richiesta" copiare il contenuto del file dello schema JSON.

   [![Corpo della richiesta](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Nella scheda "Richiesta delle intestazioni" aggiungere l'attributo di intestazione Content-Type con valore "application/json"

   [![Intestazioni richiesta](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Fare clic sul pulsante "Run Query" (Esegui Query) per importare il nuovo schema.
