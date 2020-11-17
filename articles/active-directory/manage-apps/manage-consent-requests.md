---
title: Gestione del consenso alle applicazioni e valutazione delle richieste di consenso in Azure Active Directory
description: Informazioni su come gestire le richieste di consenso quando il consenso dell'utente è disabilitato o limitato e come valutare una richiesta di consenso dell'amministratore a livello di tenant a un'applicazione in Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.openlocfilehash: 4664bdd515c815dbd742c0d4e05f1e5c03978526
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649193"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Gestione del consenso alle applicazioni e valutazione delle richieste di consenso

Microsoft [consiglia](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) di disabilitare il consenso dell'utente finale per le applicazioni. Il processo decisionale verrà centralizzato con il team di amministratori di sicurezza e identità dell'organizzazione.

Quando il consenso dell'utente finale è disabilitato o limitato, è necessario tenere presenti alcune considerazioni importanti per garantire che l'organizzazione rimanga protetta, pur continuando a consentire l'uso di applicazioni aziendali critiche. Questa procedura è fondamentale per ridurre al minimo l'effetto sul team di supporto e sugli amministratori IT dell'organizzazione, evitando l'uso di account non gestiti in applicazioni di terze parti.

## <a name="process-changes-and-education"></a>Elaborazione delle modifiche e della formazione

 1. Provare ad abilitare il [flusso di lavoro di consenso dell'amministratore (anteprima)](configure-admin-consent-workflow.md) per consentire agli utenti di richiedere l'approvazione dell'amministratore direttamente dalla schermata di consenso.

 2. Assicurarsi che tutti gli amministratori conoscano le [autorizzazioni e il Framework di consenso](../develop/consent-framework.md), il funzionamento della [richiesta di consenso](../develop/application-consent-experience.md) e la [valutazione di una richiesta di consenso dell'amministratore a livello di tenant](#evaluating-a-request-for-tenant-wide-admin-consent).
 3. Esaminare i processi esistenti dell'organizzazione per consentire agli utenti di richiedere l'approvazione dell'amministratore per un'applicazione e di effettuare gli aggiornamenti, se necessario. Se i processi vengono modificati:
    * Aggiornare la documentazione, il monitoraggio, l'automazione e così via pertinenti.
    * Comunicare le modifiche del processo a tutti gli utenti, gli sviluppatori, i team di supporto e gli amministratori IT interessati.

## <a name="auditing-and-monitoring"></a>Controllo e monitoraggio

1. [Controllare le app e concedere le autorizzazioni](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) nell'organizzazione per assicurarsi che in precedenza non sia stato concesso l'accesso ai dati alle applicazioni non autorizzate o sospette.

2. Esaminare [rilevare e correggere le concessioni di consenso illecito in Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) per altre procedure consigliate e misure di sicurezza per le applicazioni sospette che richiedono il consenso OAuth.

3. Se l'organizzazione ha la licenza appropriata:

    * Usare le [funzionalità aggiuntive di controllo delle applicazioni OAuth in Microsoft cloud app Security](/cloud-app-security/investigate-risky-oauth).
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

   Le autorizzazioni richieste dall'applicazione sono elencate nella richiesta di [consenso](../develop/application-consent-experience.md). Espandendo il titolo dell'autorizzazione, viene visualizzata la descrizione dell'autorizzazione. La descrizione per le autorizzazioni dell'applicazione in genere termina con "senza un utente connesso". La descrizione per le autorizzazioni delegate termina in genere con "per conto dell'utente connesso". Le autorizzazioni per l'API Microsoft Graph sono descritte in [Microsoft Graph riferimento alle autorizzazioni](/graph/permissions-reference) . per informazioni sulle autorizzazioni che espongono, vedere la documentazione relativa ad altre API.

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
Anziché concedere il consenso per l'intera organizzazione, un amministratore può usare anche l' [API Microsoft Graph](/graph/use-the-api) per concedere il consenso alle autorizzazioni delegate per conto di un singolo utente. Per altre informazioni, vedere [ottenere l'accesso per conto di un utente](/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Limitazione dell'accesso degli utenti alle applicazioni
L'accesso degli utenti alle applicazioni può ancora essere limitato anche quando è stato concesso il consenso dell'amministratore a livello di tenant. Per ulteriori informazioni su come richiedere l'assegnazione di un utente a un'applicazione, vedere [metodi per l'assegnazione di utenti e gruppi](./assign-user-or-group-access-portal.md).

Per una panoramica più ampia, tra cui come gestire scenari complessi aggiuntivi, vedere [uso di Azure ad per la gestione dell'accesso alle applicazioni](what-is-access-management.md).

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Disabilitare tutte le future operazioni di consenso utente per qualsiasi applicazione
La disabilitazione di consenso da parte dell'utente per l'intera directory impedisce agli utenti finali di consentire l'accesso a qualsiasi applicazione. Gli amministratori possono comunque acconsentire per conto dell'utente. Per informazioni sul consenso per le applicazioni e sui motivi per cui può essere opportuno o meno concederlo, vedere [Informazioni sul consenso dell'utente e dell'amministratore](../develop/howto-convert-app-to-be-multi-tenant.md).

Per disabilitare tutte le future operazioni di consenso utente nell'intera directory, seguire questa procedura:
1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.
2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4.  Selezionare **utenti e gruppi** nel menu di navigazione.
5.  Selezionare **impostazioni utente**.
6.  Disabilitare tutte le future operazioni di consenso degli utenti impostando l'opzione **Gli utenti possono consentire alle app di accedere ai propri dati** su **No** e facendo clic sul pulsante **Salva**.

## <a name="next-steps"></a>Passaggi successivi
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)
* [Configurare la modalità con cui gli utenti finali consentono le applicazioni](configure-user-consent.md)
* [Autorizzazioni e consenso in Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)