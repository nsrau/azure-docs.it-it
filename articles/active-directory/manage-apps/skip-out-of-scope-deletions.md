---
title: Ignorare l'eliminazione degli utenti fuori ambito | Microsoft Docs
description: Informazioni su come eseguire l'override del comportamento predefinito del deprovisioning fuori dagli utenti dell'ambito.
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
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a40b699c01f50ceb1bedbc36e7f1467772336f
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997072"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Ignora l'eliminazione di account utente che non rientrano nell'ambito

Per impostazione predefinita, il motore di provisioning di Azure AD elimina temporaneamente o Disabilita gli utenti che non rientrano nell'ambito. Tuttavia, per alcuni scenari come la giornata lavorativa ad Active Directory in ingresso, questo comportamento potrebbe non essere quello previsto e potrebbe essere necessario eseguire l'override di questo comportamento predefinito.  

Questa guida descrive come usare l'API Microsoft Graph e il Microsoft Graph Esplora API per impostare il flag ***SkipOutOfScopeDeletions*** che controlla l'elaborazione degli account che non rientrano nell'ambito. 
* Se ***SkipOutOfScopeDeletions*** è impostato su 0 (false), gli account che non rientrano nell'ambito verranno disabilitati nella destinazione
* Se ***SkipOutOfScopeDeletions*** è impostato su 1 (true), gli account che non rientrano nell'ambito non verranno disabilitati nella destinazione. questo flag viene impostato a livello di *app di provisioning* e può essere configurato usando il API Graph. 

Poiché questa configurazione viene usata ampiamente con la *giornata lavorativa per Active Directory app di provisioning degli utenti* , la procedura seguente include schermate dell'applicazione per la giornata lavorativa. Può tuttavia essere usato anche con **tutte le altre app** , ad esempio ServiceNow, Salesforce, Dropbox e così via.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passaggio 1: recuperare l'ID dell'entità servizio dell'app di provisioning (ID oggetto)

1. Avviare il [portale di Azure](https://portal.azure.com)e passare alla sezione proprietà dell'applicazione di provisioning. Ad esempio, se si vuole esportare la *giornata lavorativa nel mapping di un'applicazione di provisioning utenti ad* , passare alla sezione proprietà dell'app. 
1. Nella sezione delle proprietà dell'app di provisioning, copiare il valore GUID associato al campo *Object ID* (ID oggetto). Questo valore è denominato anche **ServicePrincipalId** dell'app e verrà usato nelle operazioni di Graph Explorer.

   ![ID entità Servizio app Workday](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passaggio 2: accedere a Microsoft Graph Explorer

1. Avviare [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Fare clic sul pulsante "Sign-In with Microsoft" (Accedi con Microsoft) e accedere con le credenziali di amministratore globale di AD o di amministratore dell'app.

    ![Accedere a Graph](./media/export-import-provisioning-mappings/wd_export_02.png)

1. Quando l'accesso è stato completato, vengono visualizzati i dettagli dell'account utente nel riquadro sinistro.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Passaggio 3: ottenere le credenziali e i dettagli di connettività dell'app esistente

In Microsoft Graph Explorer, eseguire la query GET seguente, sostituendo [servicePrincipalId] con il **ServicePrincipalId** estratto dal [Passaggio 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![OTTENERE la query del processo](./media/skip-out-of-scope-deletions/skip-03.png)

Copiare la risposta in un file di testo. L'aspetto sarà simile al testo JSON riportato di seguito, con i valori evidenziati in giallo specifici per la distribuzione. Aggiungere le righe evidenziate in verde alla fine e aggiornare la password di connessione per la giornata lavorativa evidenziata in blu. 

   ![OTTENERE la risposta del processo](./media/skip-out-of-scope-deletions/skip-04.png)

Ecco il blocco JSON da aggiungere al mapping. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Passaggio 4: aggiornare l'endpoint dei segreti con il flag SkipOutOfScopeDeletions

In Graph Explorer eseguire il comando seguente per aggiornare l'endpoint Secrets con il flag ***SkipOutOfScopeDeletions*** . 

Nell'URL seguente sostituire [servicePrincipalId] con il **servicePrincipalId** Estratto dal [passaggio 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Copiare il testo aggiornato dal passaggio 3 nel "corpo della richiesta" e impostare l'intestazione "Content-Type" su "application/json" in "Request Headers". 

   ![PUT, richiesta](./media/skip-out-of-scope-deletions/skip-05.png)

Fare clic su "Esegui query". 

L'output dovrebbe essere "operazione riuscita – codice di stato 204". 

   ![Inserisci risposta](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Passaggio 5: verificare che gli utenti non siano disabilitati

È possibile testare questo flag per ottenere un comportamento previsto aggiornando le regole di ambito per ignorare un utente specifico. Nell'esempio seguente viene escluso il dipendente con ID 21173 (che si trovava in precedenza nell'ambito) aggiungendo una nuova regola di ambito: 

   ![Esempio di ambito](./media/skip-out-of-scope-deletions/skip-07.png)

Nel ciclo di provisioning successivo, il servizio di provisioning Azure AD identificherà che l'utente 21173 è uscito dall'ambito e se la proprietà SkipOutOfScopeDeletions è abilitata, la regola di sincronizzazione per tale utente visualizzerà un messaggio come illustrato di seguito: 

   ![Esempio di ambito](./media/skip-out-of-scope-deletions/skip-08.png)


