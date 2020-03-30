---
title: Ignorare l'eliminazione degli utenti esterni all'ambito. Documenti Microsoft
description: Informazioni su come eseguire l'override del comportamento predefinito di deprovisioning al di fuori dell'ambito degli utenti.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522450"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Ignorare l'eliminazione degli account utente che escono dall'ambito

Per impostazione predefinita, il motore di provisioning di Azure AD elimina o disabilita gli utenti che escono dall'ambito. Tuttavia, per alcuni scenari come Workday to AD User Inbound Provisioning questo comportamento potrebbe non essere il previsto ed è possibile eseguire l'override di questo comportamento predefinito.  

In questa guida viene descritto come utilizzare l'API Microsoft Graph e Esplora API Microsoft Graph per impostare il flag ***SkipOutOfScopeDeletions*** che controlla l'elaborazione degli account che escono dall'ambito. 
* Se ***SkipOutOfScopeDeletions*** è impostato su 0 (false), gli account che escono dall'ambito verranno disabilitati nella destinazione
* Se ***SkipOutOfScopeDeletions*** è impostato su 1 (true), gli account che escono dall'ambito non verranno disabilitati nella destinazione Questo flag è impostato a livello di app di *provisioning* e può essere configurato tramite l'API Graph. 

Poiché questa configurazione è ampiamente utilizzata con l'app di provisioning degli utenti da *Workday a Active Directory,* i passaggi seguenti includono screenshot dell'applicazione Workday. Tuttavia questo può essere utilizzato anche con **tutte le altre applicazioni** (ServiceNow, Salesforce, Dropbox, ecc.).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passaggio 1: Recuperare l'ID dell'entità servizio dell'app di provisioning (ID oggetto)Step 1: Retrieve your Provisioning App Service Principal ID (Object ID)

1. Avviare il portale di [Azure](https://portal.azure.com)e passare alla sezione Proprietà dell'applicazione di provisioning. Ad esempio, se si desidera esportare il mapping *dell'applicazione Workday in AD User Provisioning,* passare alla sezione Proprietà dell'app. 
1. Nella sezione delle proprietà dell'app di provisioning, copiare il valore GUID associato al campo *Object ID* (ID oggetto). Questo valore è denominato anche **ServicePrincipalId** dell'app e verrà usato nelle operazioni di Graph Explorer.

   ![ID entità Servizio app Workday](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passaggio 2: Accedere a Microsoft Graph Explorer

1. Avviare [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Fare clic sul pulsante "Sign-In with Microsoft" (Accedi con Microsoft) e accedere con le credenziali di amministratore globale di AD o di amministratore dell'app.

    ![Accedere a Graph](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Quando l'accesso è stato completato, vengono visualizzati i dettagli dell'account utente nel riquadro sinistro.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Passaggio 3: Ottenere le credenziali dell'app esistenti e i dettagli di connettivitàStep 3: Get existing app credentials and connectivity details

In Microsoft Graph Explorer, eseguire la query GET seguente, sostituendo [servicePrincipalId] con il **ServicePrincipalId** estratto dal [Passaggio 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![Query processo GET](./media/skip-out-of-scope-deletions/skip-03.png)

Copiare la risposta in un file di testo. Sarà simile al testo JSON illustrato di seguito, con i valori evidenziati in giallo specifici per la distribuzione. Aggiungere le righe evidenziate in verde alla fine e aggiornare la password di connessione Workday evidenziata in blu. 

   ![Risposta del processo GET](./media/skip-out-of-scope-deletions/skip-04.png)

Ecco il blocco JSON da aggiungere al mapping. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Passaggio 4: Aggiornare l'endpoint secrets con il flag SkipOutOfScopeDeletionsStep 4: Update the secrets endpoint with the SkipOutOfScopeDeletions flag

In Graph Explorer eseguire il comando seguente per aggiornare l'endpoint secrets con il flag ***SkipOutOfScopeDeletions.*** 

Nell'URL seguente sostituire [servicePrincipalId] con il **ServicePrincipalId** estratto dal [passaggio 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Copiare il testo aggiornato dal passaggio 3 nel "Corpo della richiesta" e impostare l'intestazione "Content-Type" su "application/json" in "Intestazioni richiesta". 

   ![PUT, richiesta](./media/skip-out-of-scope-deletions/skip-05.png)

Fare clic su "Esegui query". 

Si dovrebbe ottenere l'output come "Success – Status Code 204". 

   ![Risposta PUT](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Passaggio 5: Verificare che gli utenti esterni all'ambito non vengano disabilitatiStep 5: Verify that out of scope users don't get disabled

È possibile testare i risultati di questo flag nel comportamento previsto aggiornando le regole di ambito per ignorare un utente specifico. Nell'esempio seguente viene escluso il dipendente con ID 21173 (che in precedenza si trovava nell'ambito) aggiungendo una nuova regola di ambito:In the example below, we are excluding the employee with ID 21173 (who was earlier in scope) by adding a new scopeng rule: 

   ![Esempio di ambitoScoping](./media/skip-out-of-scope-deletions/skip-07.png)

Nel successivo ciclo di provisioning, il servizio di provisioning di Azure AD identificherà che l'utente 21173 ha superato l'ambito e se la proprietà SkipOutOfScopeDeletions è abilitata, la regola di sincronizzazione per tale utente visualizzerà un messaggio come illustrato di seguito:In the next provisioning cycle, the Azure AD provisioning service will identify that the user 21173 has out out scope and if the SkipOutOfScopeDeletions property is enabled, then the synchronization rule for that user will display a message as shown below: 

   ![Esempio di ambitoScoping](./media/skip-out-of-scope-deletions/skip-08.png)


