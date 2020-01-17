---
title: Gestione del consenso alle applicazioni e valutazione delle richieste di consenso-Azure AD
description: Informazioni su come gestire le richieste di consenso quando il consenso dell'utente è disabilitato o limitato e come valutare una richiesta di consenso dell'amministratore a livello di tenant per un'applicazione.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c6a77fa8c6c48c9c3d4f2eba3ba56ac9114707a
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122990"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Gestione del consenso alle applicazioni e valutazione delle richieste di consenso

Microsoft [consiglia](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) di disabilitare il consenso dell'utente finale per le applicazioni. Il processo decisionale verrà centralizzato con il team di amministratori di sicurezza e identità dell'organizzazione.

Quando il consenso dell'utente finale è disabilitato o limitato, è necessario tenere presenti alcune considerazioni importanti per garantire che l'organizzazione rimanga protetta, pur continuando a consentire l'uso di applicazioni aziendali critiche. Questa procedura è fondamentale per ridurre al minimo l'effetto sul team di supporto e sugli amministratori IT dell'organizzazione, evitando l'uso di account non gestiti in applicazioni di terze parti.

## <a name="process-changes-and-education"></a>Elaborazione delle modifiche e della formazione

 1. Provare ad abilitare il [flusso di lavoro di consenso dell'amministratore (anteprima)](configure-admin-consent-workflow.md) per consentire agli utenti di richiedere l'approvazione dell'amministratore direttamente dalla schermata di consenso.

 2. Assicurarsi che tutti gli amministratori conoscano le [autorizzazioni e il Framework di consenso](../develop/consent-framework.md), il funzionamento della [richiesta di consenso](../develop/application-consent-experience.md) e la [valutazione di una richiesta di consenso dell'amministratore a livello di tenant](#evaluating-a-request-for-tenant-wide-admin-consent).
 3. Esaminare i processi esistenti dell'organizzazione per consentire agli utenti di richiedere l'approvazione dell'amministratore per un'applicazione e di effettuare gli aggiornamenti, se necessario. Se i processi vengono modificati:
    * Aggiornare la documentazione, il monitoraggio, l'automazione e così via pertinenti.
    * Comunicare le modifiche del processo a tutti gli utenti, gli sviluppatori, i team di supporto e gli amministratori IT interessati.

## <a name="auditing-and-monitoring"></a>Controllo e monitoraggio

1. [Controllare le app e concedere le autorizzazioni](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) nell'organizzazione per assicurarsi che in precedenza non sia stato concesso l'accesso ai dati alle applicazioni non autorizzate o sospette.

2. Esaminare [rilevare e correggere le concessioni di consenso illecito in Office 365](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) per altre procedure consigliate e misure di sicurezza per le applicazioni sospette che richiedono il consenso OAuth.

3. Se l'organizzazione ha la licenza appropriata:

    * Usare le [funzionalità aggiuntive di controllo delle applicazioni OAuth in Microsoft cloud app Security](https://docs.microsoft.com/azure/cloud-app-security/investigate-risky-oauth).
    * Usare [le cartelle di lavoro di monitoraggio di Azure per monitorare le autorizzazioni e le](../reports-monitoring/howto-use-azure-monitor-workbooks.md) attività correlate al consenso. La cartella di lavoro di *acconsente Insights* offre una visualizzazione delle app in base al numero di richieste di consenso non riuscite. Questa operazione può essere utile per classificare in ordine di priorità le applicazioni per gli amministratori e decidere se concedere loro il consenso dell'amministratore.

### <a name="additional-considerations-for-reducing-friction"></a>Considerazioni aggiuntive per la riduzione dell'attrito

Per ridurre al minimo l'effetto sulle applicazioni attendibili e cruciali per l'azienda già in uso, è consigliabile concedere in modo proattivo il consenso dell'amministratore per le applicazioni che dispongono di un numero elevato di concessioni di consenso utente:

1. Eseguire un inventario delle app già aggiunte all'organizzazione con utilizzo elevato, in base ai registri di accesso o all'attività di concessione del consenso. Uno [script](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) di PowerShell può essere usato per individuare in modo rapido e semplice le applicazioni con un numero elevato di concessioni di consenso dell'utente.

2. Valutare le prime applicazioni a cui non è stato ancora concesso il consenso dell'amministratore.

   > [!IMPORTANT]
   > Valutare attentamente un'applicazione prima di concedere il consenso dell'amministratore a livello di tenant, anche se molti utenti dell'organizzazione hanno già acconsentito per se stessi.

3. Per ogni applicazione approvata, concedere il consenso dell'amministratore a livello di tenant usando uno dei metodi descritti di seguito.

4. Per ogni applicazione approvata, è consigliabile [limitare l'accesso degli utenti](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Valutazione di una richiesta di consenso dell'amministratore a livello di tenant

La concessione del consenso dell'amministratore a livello di tenant è un'operazione sensibile.  Le autorizzazioni verranno concesse per conto dell'intera organizzazione e possono includere le autorizzazioni per tentare operazioni con privilegi elevati. Ad esempio, la gestione dei ruoli, l'accesso completo a tutte le cassette postali o tutti i siti e la rappresentazione completa dell'utente.

Prima di concedere il consenso dell'amministratore a livello di tenant, è necessario assicurarsi che l'applicazione e l'autore dell'applicazione siano considerati attendibili per il livello di accesso concesso. Se non si è certi di chi controlla l'applicazione e perché l'applicazione richiede le autorizzazioni, non *concedere il consenso*.

Nell'elenco seguente vengono fornite alcune raccomandazioni da considerare durante la valutazione di una richiesta di concessione del consenso dell'amministratore.

* **Informazioni sul [Framework di consenso e autorizzazioni](../develop/consent-framework.md) nella piattaforma di identità Microsoft.**

* **Comprendere la differenza tra le [autorizzazioni delegate e le autorizzazioni dell'applicazione](../develop/v2-permissions-and-consent.md#permission-types).**

   Le autorizzazioni dell'applicazione consentono all'applicazione di accedere ai dati per l'intera organizzazione, senza alcuna interazione da parte dell'utente. Le autorizzazioni delegate consentono all'applicazione di agire per conto di un utente che a un certo punto è stato connesso all'applicazione.

* **Informazioni sulle autorizzazioni richieste.**

   Le autorizzazioni richieste dall'applicazione sono elencate nella richiesta di [consenso](../develop/application-consent-experience.md). Espandendo il titolo dell'autorizzazione, viene visualizzata la descrizione dell'autorizzazione. La descrizione per le autorizzazioni dell'applicazione in genere termina con "senza un utente connesso". La descrizione per le autorizzazioni delegate termina in genere con "per conto dell'utente connesso". Le autorizzazioni per l'API Microsoft Graph sono descritte in [riferimento alle autorizzazioni Microsoft Graph]. per informazioni sulle autorizzazioni esposte, vedere la documentazione relativa ad altre API.

   Se non si comprende un'autorizzazione richiesta, non *concedere il consenso*.

* **Individuare l'applicazione che richiede le autorizzazioni e l'autore dell'applicazione.**

   Tenere presente che le applicazioni dannose tentano di somigliare ad altre applicazioni.

   Se si dubita della legittimità di un'applicazione o del relativo editore, non *concedere il consenso*. Al contrario, cercare una conferma aggiuntiva (ad esempio, direttamente dall'editore di applicazioni).

* **Verificare che le autorizzazioni richieste siano allineate con le funzionalità attese dall'applicazione.**

   Ad esempio, un'applicazione che offre la gestione del sito di SharePoint può richiedere l'accesso delegato per leggere tutte le raccolte siti, ma non richiede necessariamente l'accesso completo a tutte le cassette postali o i privilegi di rappresentazione completi nella directory.

   Se si ritiene che l'applicazione richiede più autorizzazioni di quelle necessarie, non *concedere il consenso*. Per ottenere maggiori dettagli, contattare l'autore dell'applicazione.

## <a name="granting-consent-as-an-administrator"></a>Concessione del consenso come amministratore

### <a name="granting-tenant-wide-admin-consent"></a>Concessione del consenso dell'amministratore a livello di tenant

Per istruzioni dettagliate su come concedere il consenso dell'amministratore a livello di tenant dal portale di Azure, usando Azure AD PowerShell o dalla richiesta di consenso, vedere [concedere il consenso dell'amministratore a livello di tenant a un'applicazione](grant-admin-consent.md) .

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Concessione del consenso per conto di un utente specifico

Anziché concedere il consenso per l'intera organizzazione, un amministratore può utilizzare anche il [Azure AD API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) per concedere il consenso alle autorizzazioni delegate per conto di un singolo utente. A tale scopo, inviare una richiesta di `POST` per creare un'entità [OAuth2PermissionGrant](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) in cui `consentType` sia impostato su "Principal" e `principalId` sia impostato sull'ID oggetto per l'utente per conto del quale viene concesso il consenso.

## <a name="limiting-user-access-to-applications"></a>Limitazione dell'accesso degli utenti alle applicazioni

L'accesso degli utenti alle applicazioni può ancora essere limitato anche quando è stato concesso il consenso dell'amministratore a livello di tenant. Per ulteriori informazioni su come richiedere l'assegnazione di un utente a un'applicazione, vedere [metodi per l'assegnazione di utenti e gruppi](methods-for-assigning-users-and-groups.md).

Per una panoramica più ampia, tra cui come gestire scenari complessi aggiuntivi, vedere [uso di Azure ad per la gestione dell'accesso alle applicazioni](what-is-access-management.md).

## <a name="next-steps"></a>Passaggi successivi

[Cinque passaggi per proteggere l'infrastruttura di identità](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)

[Configurare la modalità con cui gli utenti finali accettano le applicazioni](configure-user-consent.md)

[Autorizzazioni e consenso nella piattaforma di identità Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD in StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)