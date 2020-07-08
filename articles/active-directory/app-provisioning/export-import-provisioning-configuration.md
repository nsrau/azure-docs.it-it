---
title: Esportare la configurazione del provisioning ed eseguire il rollback a uno stato valido noto per il ripristino di emergenza
description: Informazioni su come esportare la configurazione del provisioning ed eseguire il rollback a uno stato valido noto per il ripristino di emergenza.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/19/2020
ms.author: kenwith
ms.openlocfilehash: ef4fbf582baf1e4b81d49c81a8b0e16674e64841
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781723"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Procedura: esportare la configurazione del provisioning ed eseguire il rollback a uno stato valido noto

In questo articolo si apprenderà come:

- Esportare e importare la configurazione di provisioning dal portale di Azure
- Esportare e importare la configurazione di provisioning tramite l'API Microsoft Graph

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Esportare e importare la configurazione di provisioning dal portale di Azure

### <a name="export-your-provisioning-configuration"></a>Esportare la configurazione del provisioning

Per esportare la configurazione:

1. Nel [portale di Azure](https://portal.azure.com/) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.
1. Nel riquadro **Azure Active Directory** selezionare **applicazioni aziendali** e scegliere l'applicazione.
1. Nel riquadro di spostamento a sinistra selezionare **provisioning**. Dalla pagina di configurazione del provisioning, fare clic su **mapping attributi**, quindi su **Mostra opzioni avanzate**e infine **esaminare lo schema**. Verrà visualizzata l'Editor schemi.
1. Fare clic su download nella barra dei comandi nella parte superiore della pagina per scaricare lo schema.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Ripristino di emergenza: eseguire il rollback a uno stato valido noto

L'esportazione e il salvataggio della configurazione consentono di eseguire il rollback a una versione precedente della configurazione. Si consiglia di esportare la configurazione del provisioning e di salvarla per un uso successivo in qualsiasi momento in cui si apportino una modifica ai mapping degli attributi o ai filtri di ambito. È sufficiente aprire il file JSON che è stato scaricato nei passaggi precedenti, copiare l'intero contenuto del file JSON, sostituire l'intero contenuto del payload JSON nell'Editor schemi e quindi salvare. Se è presente un ciclo di provisioning attivo, questo verrà completato e il ciclo successivo utilizzerà lo schema aggiornato. Il ciclo successivo sarà anche un ciclo iniziale, che rivaluterà tutti gli utenti e i gruppi in base alla nuova configurazione. Quando si esegue il rollback a una configurazione precedente, tenere presente quanto segue:

- Gli utenti verranno valutati di nuovo per determinare se devono essere nell'ambito. Se i filtri di ambito sono stati modificati, un utente non è presente nell'ambito, più saranno disabilitati. Sebbene questo sia il comportamento desiderato nella maggior parte dei casi, è possibile che si desideri impedire questa operazione e utilizzare la funzionalità [Ignora eliminazioni dell'ambito](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions) . 
- Se si modifica la configurazione del provisioning, il servizio viene riavviato e viene attivato un [ciclo iniziale](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental).

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Esportare e importare la configurazione di provisioning tramite l'API Microsoft Graph

È possibile usare l'API Microsoft Graph e Esplora Microsoft Graph per esportare i mapping degli attributi e lo schema del provisioning degli utenti in un file JSON e importarli di nuovo in Azure AD. È anche possibile usare i passaggi acquisiti qui per creare un backup della configurazione del provisioning.

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passaggio 1: recuperare l'ID dell'entità servizio dell'app di provisioning (ID oggetto)

1. Avviare il [portale di Azure](https://portal.azure.com)e passare alla sezione proprietà dell'applicazione di provisioning. Se ad esempio si vuole esportare la *giornata lavorativa nel mapping di un'applicazione di provisioning utenti ad* , passare alla sezione proprietà dell'app.
1. Nella sezione delle proprietà dell'app di provisioning, copiare il valore GUID associato al campo *Object ID* (ID oggetto). Questo valore viene anche chiamato **ServicePrincipalId** dell'app e verrà usato nelle operazioni di Microsoft Graph Explorer.

   ![ID entità Servizio app Workday](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passaggio 2: accedere a Microsoft Graph Explorer

1. Avviare [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Fare clic sul pulsante "Sign-In with Microsoft" (Accedi con Microsoft) e accedere con le credenziali di amministratore globale di AD o di amministratore dell'app.

    ![Accesso Microsoft Graph](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Quando l'accesso è stato completato, vengono visualizzati i dettagli dell'account utente nel riquadro sinistro.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Passaggio 3: recuperare l'ID del processo di provisioning dell'app di provisioning

In Microsoft Graph Explorer, eseguire la query GET seguente, sostituendo [servicePrincipalId] con il **ServicePrincipalId** estratto dal [Passaggio 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Si otterrà una risposta come illustrato di seguito. Copiare l'attributo"id" presente nella risposta. Questo valore è il **ProvisioningJobId** e verrà usato per recuperare i metadati dello schema sottostante.

   [![ID processo di provisioning](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Passaggio 4: scaricare lo schema di provisioning

In Microsoft Graph Explorer, eseguire la query GET seguente, sostituendo [servicePrincipalId] e [ProvisioningJobId] con il ServicePrincipalId e il ProvisioningJobId recuperati nei passaggi precedenti.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copiare l'oggetto JSON dalla risposta e salvarlo in un file per creare un backup dello schema.

### <a name="step-5-import-the-provisioning-schema"></a>Passaggio 5: importare lo schema di provisioning

> [!CAUTION]
> Eseguire questo passaggio solo se è necessario modificare lo schema per la configurazione che non può essere modificato usando il portale di Azure o se è necessario ripristinare la configurazione da un file di cui è stato eseguito il backup in precedenza con uno schema di lavoro valido.

In Microsoft Graph Explorer, eseguire la query PUT seguente, sostituendo [servicePrincipalId] e [ProvisioningJobId] con il ServicePrincipalId e il ProvisioningJobId recuperati nei passaggi precedenti.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Nella scheda "Corpo della richiesta" copiare il contenuto del file dello schema JSON.

   [![Corpo della richiesta](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Nella scheda "Richiesta delle intestazioni" aggiungere l'attributo di intestazione Content-Type con valore "application/json"

   [![Intestazioni della richiesta](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Selezionare **Esegui query** per importare il nuovo schema.
