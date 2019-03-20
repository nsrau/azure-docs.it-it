---
title: Usare le verifiche di accesso di Azure AD per gestire gli utenti esclusi dai criteri di accesso condizionale | Microsoft Docs
description: Informazioni su come usare le verifiche di accesso di Azure Active Directory (Azure AD) per gestire gli utenti che sono stati esclusi dai criteri di accesso condizionale
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a197a6c27b337d7aa97667dc07b1059e82050549
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892720"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Usare le verifiche di accesso di Azure AD per gestire gli utenti esclusi dai criteri di accesso condizionale

In una situazione ideale, tutti gli utenti seguono gli stessi criteri di accesso per proteggere l'accesso alle risorse dell'organizzazione. Esistono tuttavia casi aziendali che richiedono di introdurre eccezioni. Questo articolo vengono descritti alcuni esempi in cui le esclusioni potrebbero essere necessarie se e come è, l'amministratore IT può gestire questa attività, evitare supervisione delle eccezioni dei criteri e avere a disposizione i revisori come prova che queste eccezioni vengono esaminate regolarmente con Azure Verifiche di accesso di Active Directory (Azure AD).

> [!NOTE]
> Per usare le verifiche di accesso di Azure AD è necessaria una licenza di valutazione o una licenza a pagamento Azure Premium P2 o Enterprise Mobility + Security E5 valida. Per altre informazioni, vedere [Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Motivi per escludere utenti dai criteri

Un amministratore IT potrebbe usare l'[accesso condizionale di Azure AD](../conditional-access/overview.md) per richiedere agli utenti di eseguire l'autenticazione usando Multi-Factor Authentication (MFA) oppure di accedere da una rete o un dispositivo attendibile. Durante la pianificazione della distribuzione, è possibile scoprire che alcuni di questi requisiti non possono essere soddisfatti da tutti gli utenti. Ad esempio, esistono utenti che lavorano da un ufficio remoto che non fa parte della rete interna o un dirigente che usa un telefono meno recente non supportato. L'azienda richiede che tali utenti siano autorizzati a effettuare l'accesso e svolgere il lavoro, di conseguenza, vengono esclusi dai criteri di accesso condizionale.

Come ulteriore esempio, si potrebbero usare [località denominate](../conditional-access/location-condition.md) nell'accesso condizionale per configurare un set di paesi e aree geografiche da cui non si vuole consentire agli utenti di accedere ai loro tenant.

![Posizioni specifiche](./media/conditional-access-exclusion/named-locations.png)

In alcuni casi, tuttavia, gli utenti potrebbero avere un motivo legittimo per eseguire l'accesso da questi paesi bloccati. Ad esempio, potrebbero essere in viaggio in uno di questi paesi per motivi di lavoro o personali. In questo esempio, i criteri di accesso condizionale per bloccare questi paesi potrebbero avere un gruppo di sicurezza cloud dedicato per gli utenti esclusi dai criteri. Gli utenti che devono avere l'accesso anche in viaggio, possono aggiungersi al gruppo usando la [gestione gruppi self-service di Azure AD](../users-groups-roles/groups-self-service-management.md).

Un altro esempio potrebbe essere la presenza di criteri di accesso condizionale che [bloccano l'autenticazione legacy per la maggior parte degli utenti](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Microsoft consiglia di bloccare l'uso di protocolli legacy nel tenant per migliorare il comportamento di sicurezza. Tuttavia, in presenza di utenti che devono assolutamente usare metodi di autenticazione legacy per accedere alle risorse tramite Office 2010 o client basati su SMTP/IMAP o POP, è possibile escludere gli utenti dai criteri che bloccano i metodi di autenticazione legacy.

## <a name="why-are-exclusions-challenging"></a>Perché le esclusioni sono complesse?

In Azure AD, è possibile definire un set di utenti come ambito per i criteri di accesso condizionale. È anche possibile escludere alcuni di questi utenti, selezionando ruoli della directory, singoli utenti o gli utenti guest. È importante ricordare che quando vengono configurate queste esclusioni, la finalità dei criteri non può essere applicata a questi utenti. Se le esclusioni venissero configurate come un elenco di singoli utenti o tramite un gruppo di sicurezza locale legacy, risulterebbero limitati la visibilità di questo elenco di esclusioni (gli utenti potrebbero non sapere della sua esistenza) e il controllo dell'amministratore IT (gli utenti possono richiedere l'inserimento nel gruppo di sicurezza per evitare i criteri). Inoltre, gli utenti che risultano qualificati per l'esclusione una volta, potrebbero non averne più bisogno o non essere più idonei.

All'inizio di un'esclusione, l'elenco di utenti che possono ignorare i criteri è in genere breve. Con il passare del tempo, il numero degli utenti esclusi cresce, così come l'elenco. A un certo punto, diventa necessario verificare l'elenco e accertarsi che ognuno di questi utenti debba continuare a essere escluso. La gestione dell'elenco da un punto di vista tecnico può essere relativamente semplice, ma chi prende le decisioni aziendali e come ci si può assicurare che tutto ciò sia controllabile?

Tuttavia, se si configura l'esclusione per i criteri di accesso condizionale usando un gruppo di Azure AD, è possibile usare le verifiche di accesso come controllo compensatorio, al fine di promuovere la visibilità e ridurre il numero di utenti con un'eccezione.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Come creare un gruppo di esclusione nei criteri di accesso condizionale

Seguire questa procedura per creare un nuovo gruppo di Azure AD e criteri di accesso condizionale non applicabili a tale gruppo.

### <a name="create-an-exclusion-group"></a>Creare un gruppo di esclusione

1. Accedere al portale di Azure.

1. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory** e quindi su **Gruppi**.

1. Nel menu in alto fare clic su **Nuovo gruppo** per aprire il riquadro del gruppo.

1. Selezionare **Sicurezza** nell'elenco **Tipo gruppo**. Specificare un nome e una descrizione.

1. Assicurarsi di impostare il tipo di **Appartenenza** su **Assegnato**.

1. Selezionare gli utenti che devono far parte di questo gruppo di esclusione e quindi fare clic su **Crea**.

    ![Riquadro Nuovo gruppo](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Creare criteri di accesso condizionale che escludono il gruppo

È ora possibile creare criteri di accesso condizionale che usano questo gruppo di esclusione.

1. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory** e quindi su **Accesso condizionale** per aprire il pannello **Criteri**.

1. Fare clic su **Nuovi criteri** per aprire il riquadro **Nuovo**.

1. Specificare un nome.

1. In Assegnazioni fare clic su **Utenti e gruppi**.

1. Nella scheda **Includi** selezionare **Tutti gli utenti**.

1. Nella scheda **Escludi** aggiungere un segno di spunta per **Utenti e gruppi** e quindi fare clic su **Selezionare gli utenti esclusi**.

1. Selezionare il gruppo di esclusione creato.

    > [!NOTE]
    > Come procedura consigliata, è consigliabile escludere almeno un account amministratore dai criteri durante i test per assicurarsi di non rimanere bloccati fuori dal tenant.

1. Continuare con la configurazione dei criteri di accesso condizionale in base ai requisiti dell'organizzazione.

    ![Selezionare gli utenti esclusi](./media/conditional-access-exclusion/select-excluded-users.png)

Di seguito verranno illustrati due esempi in cui è possibile usare le verifiche di accesso per gestire le esclusioni nei criteri di accesso condizionale.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countries"></a>Esempio 1: Verifica di accesso per gli utenti che accedono da paesi bloccati

Si supponga che esistano criteri di accesso condizionale per bloccare l'accesso da determinati paesi. I criteri includono un gruppo escluso dai criteri. Ecco una verifica di accesso consigliata per controllare i membri del gruppo.

> [!NOTE]
> Un amministratore globale o un ruolo utente amministratore deve creare verifiche di accesso.

1. La verifica verrà eseguita ogni settimana.

2. Non è prevista la fine, per assicurarsi di mantenere sempre aggiornato questo gruppo di esclusione.

3. Tutti i membri di questo gruppo saranno inclusi nell'ambito per la revisione.

4. Ogni utente dovrà attestare di avere ancora la necessità di accedere da questi paesi bloccati, pertanto di dover essere ancora membro del gruppo.

5. Se l'utente non risponde alla richiesta di revisione, verrà automaticamente rimosso dal gruppo e pertanto non potrà più accedere al tenant durante i viaggi in questi paesi.

6. Abilitare le notifiche tramite posta elettronica, in modo che gli utenti ricevano notifica dell'inizio e del completamento della verifica di accesso.

    ![Creare una verifica di accesso](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Esempio 2 Verifica di accesso per gli utenti che accedono con l'autenticazione legacy

Si supponga che esistano criteri di accesso condizionale per bloccare l'accesso per gli utenti che usano l'autenticazione legacy e versioni precedenti del client. I criteri includono un gruppo escluso dai criteri. Ecco una verifica di accesso consigliata per controllare i membri del gruppo.

1. Questa verifica dovrà essere periodica.

2. Tutti i membri del gruppo dovranno essere verificati.

3. Può essere configurata in modo da elencare i responsabili delle unità aziendali come revisori selezionati.

4. Applicare automaticamente i risultati e rimuovere gli utenti non approvati per continuare a usare i metodi di autenticazione legacy.

5. Potrebbe essere utile abilitare le raccomandazioni, in modo che i revisori di gruppi di grandi dimensioni possano prendere facilmente le decisioni necessarie.

6. Abilitare le notifiche tramite posta elettronica, in modo che gli utenti ricevano notifica dell'inizio e del completamento della verifica di accesso.

    ![Creare una verifica di accesso](./media/conditional-access-exclusion/create-access-review-2.png)

**Suggerimento avanzato**: se esistono molti gruppi di esclusione e pertanto è necessario creare più verifiche di accesso, è ora disponibile un'API nell'endpoint beta di Microsoft Graph che consente di crearli e gestirli a livello di codice. Per iniziare, vedere le [informazioni di riferimento per l'API delle verifiche di accesso di Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) e [Example of retrieving Azure AD access reviews via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096) (Esempio di recupero delle verifiche di accesso di Azure AD tramite Microsoft Graph).

## <a name="access-review-results-and-audit-logs"></a>Risultati delle verifiche di accesso e log di controllo

Ora che tutti gli elementi sono pronti (gruppo, criteri di accesso condizionale e verifiche di accesso), è possibile monitorare e tenere traccia dei risultati di queste verifiche.

1. Nel portale di Azure aprire il pannello **Verifiche di accesso**.

1. Aprire il controllo e il programma creati per gestire il gruppo di esclusione.

1. Fare clic su **Risultati** per vedere chi è stato approvato per rimanere nell'elenco e chi è stato rimosso.

    ![Risultati delle verifiche di accesso](./media/conditional-access-exclusion/access-reviews-results.png)

1. Fare quindi clic su **Log di controllo** per visualizzare le azioni eseguite durante la verifica.

    ![Log di controllo delle verifiche di accesso](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Gli amministratori IT sanno che gestire gruppi di esclusione per i criteri è talvolta inevitabile. Tuttavia, la manutenzione di questi gruppi, la loro verifica a intervalli regolari da parte del titolare dell'azienda o degli utenti stessi, così come il controllo di queste modifiche possono essere semplificati grazie alle verifiche di accesso di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)
