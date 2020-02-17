---
title: Esportare o importare la configurazione di provisioning tramite l'API Microsoft Graph | Microsoft Docs
description: Informazioni su come esportare e importare la configurazione del provisioning tramite l'API Microsoft Graph.
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
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2fa80726875c82cfa4b5d4cf6a14f4e0dae1871
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367789"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Esportare o importare la configurazione di provisioning tramite l'API Microsoft Graph

È possibile usare l'API Microsoft Graph e Esplora Microsoft Graph per esportare i mapping degli attributi e lo schema del provisioning degli utenti in un file JSON e importarli di nuovo in Azure AD. È anche possibile usare i passaggi acquisiti qui per creare un backup della configurazione del provisioning. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passaggio 1: recuperare l'ID dell'entità servizio dell'app di provisioning (ID oggetto)

1. Avviare il [portale di Azure](https://portal.azure.com)e passare alla sezione proprietà dell'applicazione di provisioning. Ad esempio, se si vuole esportare la *giornata lavorativa nel mapping di un'applicazione di provisioning utenti ad* , passare alla sezione proprietà dell'app. 
1. Nella sezione delle proprietà dell'app di provisioning, copiare il valore GUID associato al campo *Object ID* (ID oggetto). Questo valore viene anche chiamato **ServicePrincipalId** dell'app e verrà usato nelle operazioni di Microsoft Graph Explorer.

   ![ID entità Servizio app Workday](./media/export-import-provisioning-configuration/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passaggio 2: accedere a Microsoft Graph Explorer

1. Avviare [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Fare clic sul pulsante "Sign-In with Microsoft" (Accedi con Microsoft) e accedere con le credenziali di amministratore globale di AD o di amministratore dell'app.

    ![Accesso Microsoft Graph](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Quando l'accesso è stato completato, vengono visualizzati i dettagli dell'account utente nel riquadro sinistro.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Passaggio 3: recuperare l'ID del processo di provisioning dell'app di provisioning

In Microsoft Graph Explorer, eseguire la query GET seguente, sostituendo [servicePrincipalId] con il **ServicePrincipalId** estratto dal [Passaggio 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Si otterrà una risposta come illustrato di seguito. Copiare l'attributo"id" presente nella risposta. Questo valore è il **ProvisioningJobId** e verrà usato per recuperare i metadati dello schema sottostante.

   [ID processo di provisioning ![](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>Passaggio 4: scaricare lo schema di provisioning

In Microsoft Graph Explorer, eseguire la query GET seguente, sostituendo [servicePrincipalId] e [ProvisioningJobId] con il ServicePrincipalId e il ProvisioningJobId recuperati nei passaggi precedenti.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copiare l'oggetto JSON dalla risposta e salvarlo in un file per creare un backup dello schema.

## <a name="step-5-import-the-provisioning-schema"></a>Passaggio 5: importare lo schema di provisioning

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

Fare clic sul pulsante "Run Query" (Esegui Query) per importare il nuovo schema.
