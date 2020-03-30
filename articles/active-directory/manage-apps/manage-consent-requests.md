---
title: Gestione del consenso alle applicazioni e valutazione delle richieste di consenso - Azure ADManaging consent to applications and evaluating consent requests - Azure AD
description: Informazioni su come gestire le richieste di consenso quando il consenso dell'utente è disabilitato o limitato e su come valutare una richiesta di consenso di amministratore a livello di tenant per un'applicazione.
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
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367842"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Gestione del consenso alle domande e valutazione delle richieste di consenso

Microsoft [consiglia di](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) disabilitare il consenso dell'utente finale alle applicazioni. Questo centralirà il processo decisionale con il team di amministratori di identità e sicurezza dell'organizzazione.

Dopo che il consenso dell'utente finale è stato disabilitato o limitato, esistono diverse considerazioni importanti per garantire che l'organizzazione rimanga sicura, pur consentendo l'utilizzo di applicazioni aziendali critiche. Questi passaggi sono fondamentali per ridurre al minimo l'impatto sul team di supporto dell'organizzazione e sugli amministratori IT, impedendo al contempo l'utilizzo di account non gestiti in applicazioni di terze parti.

## <a name="process-changes-and-education"></a>Cambiamenti di processo e istruzione

 1. Valutare la possibilità di abilitare il flusso di lavoro di [consenso dell'amministratore (anteprima)](configure-admin-consent-workflow.md) per consentire agli utenti di richiedere l'approvazione dell'amministratore direttamente dalla schermata di consenso.

 2. Assicurarsi che tutti gli amministratori comprendano il framework delle autorizzazioni e del [consenso,](../develop/consent-framework.md)il funzionamento della richiesta di [consenso](../develop/application-consent-experience.md) e come valutare una richiesta di consenso di amministratore a livello di [tenant.](#evaluating-a-request-for-tenant-wide-admin-consent)
 3. Esaminare i processi esistenti dell'organizzazione per consentire agli utenti di richiedere l'approvazione dell'amministratore per un'applicazione e apportare aggiornamenti, se necessario. Se i processi vengono modificati:
    * Aggiornare la documentazione, il monitoraggio, l'automazione e così via pertinenti.
    * Comunicare le modifiche dei processi a tutti gli utenti, gli sviluppatori, i team di supporto e gli amministratori IT interessati.

## <a name="auditing-and-monitoring"></a>Controllo e monitoraggio

1. [Applicazioni di controllo e autorizzazioni concesse](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) all'organizzazione per garantire che nessuna applicazione ingiustificata o sospetta non abbia ricevuto l'accesso ai dati in precedenza.

2. Esaminare Rilevare e correggere le concessioni di [consenso illecito in Office 365](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) per ulteriori procedure consigliate e misure di protezione contro le applicazioni sospette che richiedono il consenso OAuth.

3. Se l'organizzazione dispone della licenza appropriata:

    * Usare funzionalità di controllo delle [applicazioni OAuth](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)aggiuntive in Microsoft Cloud App Security .
    * Usare Le cartelle di lavoro di Monitoraggio di [Azure per monitorare le autorizzazioni e consentire le](../reports-monitoring/howto-use-azure-monitor-workbooks.md) attività correlate. La cartella di lavoro *Consent Insights* fornisce una visualizzazione delle app in base al numero di richieste di consenso non riuscite. Ciò può essere utile per assegnare priorità alle applicazioni per gli amministratori di rivedere e decidere se concedere loro il consenso dell'amministratore.

### <a name="additional-considerations-for-reducing-friction"></a>Considerazioni aggiuntive per ridurre l'attrito

Per ridurre al minimo l'impatto sulle applicazioni attendibili e business-critical già in uso, è consigliabile concedere in modo proattivo il consenso dell'amministratore alle applicazioni con un numero elevato di concessioni di consenso degli utenti:

1. Eseguire un inventario delle app già aggiunte all'organizzazione con un utilizzo elevato, in base ai log di accesso o all'attività di concessione del consenso. Uno [script](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) di PowerShell può essere utilizzato per individuare rapidamente e facilmente le applicazioni con un numero elevato di concessioni di consenso degli utenti.

2. Valutare le applicazioni principali a cui non è stato ancora concesso il consenso dell'amministratore.

   > [!IMPORTANT]
   > Valutare attentamente un'applicazione prima di concedere il consenso di amministratore a livello di tenant, anche se molti utenti dell'organizzazione hanno già acconsentito a se stessi.

3. Per ogni applicazione approvata, concedere il consenso di amministratore a livello di tenant utilizzando uno dei metodi descritti di seguito.

4. Per ogni applicazione approvata, [è consigliabile limitare l'accesso degli utenti.](configure-user-consent.md)

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Valutazione di una richiesta di consenso di amministratore a livello di tenant

La concessione del consenso di amministratore a livello di tenant è un'operazione delicata.  Le autorizzazioni verranno concesse per conto dell'intera organizzazione e possono includere le autorizzazioni per tentare operazioni con privilegi elevati. Ad esempio, la gestione dei ruoli, l'accesso completo a tutte le cassette postali o a tutti i siti e la rappresentazione utente completa.

Prima di concedere il consenso di amministratore a livello di tenant, è necessario assicurarsi di considerare attendibile l'applicazione e l'autore dell'applicazione, per il livello di accesso che si sta concedendo. Se non si è certi di aver compreso chi controlla l'applicazione e perché l'applicazione richiede le autorizzazioni, *non concedere*il consenso .

Nell'elenco seguente vengono forniti alcuni suggerimenti da considerare quando si valuta una richiesta di concessione del consenso dell'amministratore.

* **Comprendere il framework per [le autorizzazioni e il consenso](../develop/consent-framework.md) nella piattaforma microsoft di identità.**

* **Comprendere la differenza tra [le autorizzazioni delegate e le autorizzazioni dell'applicazione.](../develop/v2-permissions-and-consent.md#permission-types)**

   Le autorizzazioni dell'applicazione consentono all'applicazione di accedere ai dati per l'intera organizzazione, senza alcuna interazione da parte dell'utente. Le autorizzazioni delegate consentono all'applicazione di agire per conto di un utente che a un certo punto è stato connesso all'applicazione.

* **Comprendere le autorizzazioni richieste.**

   Le autorizzazioni richieste dall'applicazione sono elencate nella richiesta di [consenso](../develop/application-consent-experience.md). Espandendo il titolo dell'autorizzazione verrà visualizzata la descrizione dell'autorizzazione. La descrizione delle autorizzazioni dell'applicazione in genere termina con "senza un utente connesso". La descrizione delle autorizzazioni delegate termina in genere con "per conto dell'utente connesso". Le autorizzazioni per l'API Microsoft Graph sono descritte in [Microsoft Graph Permissions Reference]- fare riferimento alla documentazione di altre API per comprendere le autorizzazioni che espongono.

   Se non si comprende un'autorizzazione richiesta, *non concedere il consenso*.

* **Comprendere quale applicazione richiede le autorizzazioni e chi ha pubblicato l'applicazione.**

   Diffidare di applicazioni dannose cercando di apparire come altre applicazioni.

   Se avete dubbi sulla legittimità di una domanda o del suo editore, *non concedete il consenso*. Cercare invece una conferma aggiuntiva (ad esempio, direttamente dall'autore dell'applicazione).

* **Assicurarsi che le autorizzazioni richieste siano allineate con le funzionalità previste dall'applicazione.**

   Ad esempio, un'applicazione che offre la gestione del sito di SharePoint potrebbe richiedere l'accesso delegato per leggere tutte le raccolte siti, ma non richiede necessariamente l'accesso completo a tutte le cassette postali o i privilegi di rappresentazione completa nella directory.

   Se si sospetta che l'applicazione richieda più autorizzazioni di quelle necessarie, *non concedere*il consenso . Contattare l'autore dell'applicazione per ottenere ulteriori dettagli.

## <a name="granting-consent-as-an-administrator"></a>Concessione del consenso come amministratore

### <a name="granting-tenant-wide-admin-consent"></a>Concessione del consenso di amministratore a livello di tenantGranting tenant-wide admin consent

Vedere Concedere il consenso di amministratore a livello di [tenant a un'applicazione](grant-admin-consent.md) per istruzioni dettagliate per concedere il consenso di amministratore a livello di tenant dal portale di Azure, tramite Azure AD PowerShell o dalla richiesta di consenso stessa.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Concessione del consenso per conto di un utente specifico

Anziché concedere il consenso per l'intera organizzazione, un amministratore può anche utilizzare [l'API Microsft Graph](https://docs.microsoft.com/graph/use-the-api) per concedere il consenso alle autorizzazioni delegate per conto di un singolo utente. Per ulteriori informazioni, vedere [Ottenere l'accesso per conto di un utente.](https://docs.microsoft.com/graph/auth-v2-user)

## <a name="limiting-user-access-to-applications"></a>Limitazione dell'accesso degli utenti alle applicazioni

L'accesso degli utenti alle applicazioni può comunque essere limitato anche quando è stato concesso il consenso di amministratore a livello di tenant. Per ulteriori informazioni su come richiedere l'assegnazione dell'utente a un'applicazione, vedere [metodi per l'assegnazione di utenti e gruppi](methods-for-assigning-users-and-groups.md).

Per una panoramica più ampia, incluso come gestire scenari complessi aggiuntivi, vedere Uso di Azure AD per la [gestione dell'accesso alle applicazioni.](what-is-access-management.md)

## <a name="next-steps"></a>Passaggi successivi

[Cinque passaggi per proteggere l'infrastruttura di identità](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)

[Configurare il modo in cui gli utenti finali consentono di concedere il consenso alle applicazioni](configure-user-consent.md)

[Autorizzazioni e consenso nella piattaforma di identità Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD on StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)