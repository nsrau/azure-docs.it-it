---
title: Problemi noti e risolti con la conformità al protocollo SCIM 2.0 del servizio di provisioning utenti di Azure AD | Microsoft Docs
description: Risoluzione dei problemi comuni di compatibilità con il protocollo riscontrati durante l'aggiunta in Azure AD di un'applicazione non inclusa nella raccolta che supporta SCIM 2.0
services: active-directory
documentationcenter: ''
author: asmalser
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2a2c1c415d0862b2631fa749241a9ae07df3b98
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880152"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Problemi noti e risolti con la conformità al protocollo SCIM 2.0 del servizio di provisioning utenti di Azure AD

Azure Active Directory (Azure AD) può effettuare automaticamente il provisioning di utenti e gruppi in qualsiasi applicazione o sistema gestito da un servizio Web con interfaccia definita nella [specifica del protocollo System for Cross-Domain Identity Management (SCIM) 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Il supporto del protocollo SCIM 2.0 da parte di Azure AD è descritto in [Uso di System for Cross-Domain Identity Management (SCIM) per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory ad applicazioni](use-scim-to-provision-users-and-groups.md), in cui sono indicate le parti specifiche del protocollo che vengono implementate per effettuare automaticamente il provisioning di utenti e gruppi da Azure AD alle applicazioni che supportano SCIM 2.0.

In questo articolo sono descritti i problemi correnti e passati relativi alla conformità del servizio di provisioning utenti di Azure AD con il protocollo SCIM 2.0 e sono fornite indicazioni su come risolverli.

> [!IMPORTANT]
> L'aggiornamento più recente del client SCIM per il servizio di provisioning utenti di Azure AD è stato effettuato il 18 dicembre 2018. Questo aggiornamento ha risolto i problemi di compatibilità noti elencati nella tabella seguente. Per ulteriori informazioni su questo aggiornamento, vedere le domande frequenti di seguito.

## <a name="scim-20-compliance-issues-and-status"></a>Problemi di conformità con SCIM 2.0 e stato

| **Problema di conformità con SCIM 2.0** |  **Risolto?** | **Data di risoluzione**  |  
|---|---|---|
| In Azure AD, "/ scim" deve trovarsi nella radice dell’URL endpoint SCIM dell’applicazione  | Sì  |  18 dicembre 2018 | 
| Per gli attributi di estensione, si utilizza la notazione punto "." prima dei nomi di attributo anziché i due punti ":" |  Sì  | 18 dicembre 2018  | 
|  Le richieste di patch per gli attributi multivalore contengono una sintassi del filtro del percorso non valida | Sì  |  18 dicembre 2018  | 
|  Le richieste di creazione dei gruppi contengono un URI di schema non valido | Sì  |  18 dicembre 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Le correzioni dei servizi descritte sono state applicate automaticamente all’app SCIM preesistente?

 No. Poiché si sarebbe trattato di una modifica di rilievo delle app SCIM codificate in modo da funzionare con il comportamento precedente, le modifiche non sono state applicate automaticamente alle app esistenti.

Le modifiche vengono applicate a tutte le nuove [app SCIM non incluse nella raccolta](configure-single-sign-on-non-gallery-applications.md) configurate nel portale di Azure dopo la data della correzione.

Per informazioni su come eseguire la migrazione di un processo di provisioning utenti preesistente in modo da includere le correzioni più recenti, vedere la sezione successiva.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>È possibile eseguire la migrazione di un processo di provisioning utenti SCIM per includere le correzioni più recenti del servizio?

Sì. Se si usa già questa istanza dell'applicazione per il single sign-on ed è necessario eseguire la migrazione del processo di provisioning esistente per includere le correzioni più recenti, procedere come indicato di seguito. Questa procedura spiega come usare l'API Microsoft Graph ed Esplora nell'API Microsoft Graph per rimuovere il processo di provisioning precedente da un'app SCIM esistente e crearne uno nuovo con il nuovo comportamento.

> [!NOTE]
> Se l'applicazione è ancora in fase di sviluppo e non è ancora stata distribuita per l'accesso single sign-on o il provisioning degli utenti, la soluzione più semplice consiste nell'eliminare la voce dell'applicazione nella sezione **Azure Active Directory > Applicazioni aziendali** del portale di Azure, quindi aggiungere una nuova voce per l'applicazione con l’opzione **Crea applicazione > Non nella raccolta**. In alternativa, procedere come segue.
 
1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Nella sezione **Azure Active Directory > Applicazioni aziendali** del portale di Azure, individuare e selezionare la propria applicazione SCIM.
3. Nella sezione **Proprietà** della propria app SCIM, copiare l’**ID oggetto**.
4. In una nuova finestra del browser Web, accedere a https://developer.microsoft.com/graph/graph-explorer e accedere come amministratore del tenant di Azure AD in cui deve essere aggiunta l'app.
5. In Graph explorer, eseguire il comando seguente per individuare l'ID del processo di provisioning. Sostituire "[id-oggetto]" con l’ID principale del servizio (ID oggetto) copiato nel terzo passaggio.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Get Jobs](./media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Get Jobs") 


6. Nei risultati, copiare la stringa "ID" completa che inizia con "customappsso" o "scim".
7. Eseguire il comando seguente per recuperare la configurazione di mapping degli attributi per eseguirne un backup. Usare lo stesso [id-oggetto] di prima e sostituire [id-processo] con l'ID del processo di provisioning copiato nell'ultimo passaggio.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Get Schema](./media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Get Schema") 

8. Copiare l'output JSON dall'ultimo passaggio e salvarlo in un file di testo. Questo file contiene tutti i mapping degli attributi personalizzati aggiunti all’app precedente e include alcune migliaia di righe di JSON.
9. Eseguire il comando seguente per eliminare il processo di provisioning:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Eseguire il comando seguente per creare un nuovo processo di provisioning con le correzioni più recenti di servizio.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Nei risultati dell’ultimo passaggio, copiare la stringa "ID" completa che inizia con "scim". Facoltativamente, è possibile applicare di nuovo i mapping degli attributi precedenti eseguendo il comando seguente, sostituendo [nuovo-id-processo] con il nuovo ID di processo appena copiato e immettendo il codice JSON di output salvato nel passaggio 7 come corpo della richiesta.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Tornare alla prima finestra del browser Web e selezionare la scheda **Provisioning** dell'applicazione.
13. Verificare la configurazione, quindi avviare il processo di provisioning. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>È possibile aggiungere una nuova app non inclusa nella raccolta con il vecchio comportamento di provisioning utenti?

Sì. Se si è codificata un'applicazione precedente alle correzioni con il comportamento precedente ed è necesario distribuirne una nuova istanza, procedere come indicato di seguito. Questa procedura spiega come usare l'API Microsoft Graph ed Esplora nell'API Microsoft Graph per creare un processo di provisioning SCIM con il comportamento precedente.
 
1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Nella sezione **Azure Active Directory > Applicazioni aziendali > Crea applicazione** del portale di Azure, creare una nuova applicazione **Non nella raccolta**.
3. Nella sezione **Proprietà** della nuova app personalizzata, copiare l’**ID oggetto**.
4. In una nuova finestra del browser Web, accedere a https://developer.microsoft.com/graph/graph-explorer e accedere come amministratore del tenant di Azure AD in cui deve essere aggiunta l'app.
5. In Graph explorer, eseguire il comando seguente per inizializzare la configurazione del provisioning dell'app.
   Sostituire "[id-oggetto]" con l’ID principale del servizio (ID oggetto) copiato nel terzo passaggio.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Tornare alla prima finestra del browser Web e selezionare la scheda **Provisioning** dell'applicazione.
7. Completare la configurazione del provisioning utenti con la normale procedura.


## <a name="next-steps"></a>Passaggi successivi
[Informazioni sul provisioning e il deprovisioning utenti in applicazioni SaaS](user-provisioning.md)

