---
title: Usare le verifiche di accesso per gestire utenti esclusi dai criteri di accesso condizionale - Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 880593773ca7801da2874dc2a09a4bddf910a503
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471846"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Verifiche di accesso di Usa Azure AD per gestire gli utenti esclusi dai criteri di accesso condizionale

In una situazione ideale, tutti gli utenti seguono gli stessi criteri di accesso per proteggere l'accesso alle risorse dell'organizzazione. Esistono tuttavia casi aziendali che richiedono di introdurre eccezioni. Questo articolo descrive alcuni esempi in cui potrebbero essere necessarie delle esclusioni e come può l'amministratore IT gestire questa esigenza, evitare sviste nelle eccezioni dei criteri e offrire ai revisori una prova della revisione regolare di tali eccezioni tramite le verifiche di accesso di Azure Active Directory (Azure AD).

> [!NOTE]
> Per usare le verifiche di accesso di Azure AD è necessaria una licenza di valutazione o una licenza a pagamento Azure Premium P2 o Enterprise Mobility + Security E5 valida. Per altre informazioni, vedere [Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Motivi per escludere utenti dai criteri

Un amministratore IT, è possibile usare [accesso condizionale di Azure AD](../conditional-access/overview.md) richiedere agli utenti di eseguire l'autenticazione usando multi-factor authentication (MFA) oppure accesso da una rete attendibile o un dispositivo. Durante la pianificazione della distribuzione, è possibile scoprire che alcuni di questi requisiti non possono essere soddisfatti da tutti gli utenti. Ad esempio, esistono utenti che lavorano da un ufficio remoto che non fa parte della rete interna o un dirigente che usa un telefono meno recente non supportato. L'azienda richiede che tali utenti siano autorizzati a effettuare l'accesso e svolgere il lavoro, di conseguenza, sono escluse dai criteri di accesso condizionale.

Come ulteriore esempio, è possibile usare [località denominate](../conditional-access/location-condition.md) nell'accesso condizionale per configurare un insieme di regioni e aree geografiche da cui si vuole consentire agli utenti di accedere ai loro tenant.

![Località denominate nell'accesso condizionale](./media/conditional-access-exclusion/named-locations.png)

In alcuni casi, tuttavia, utenti potrebbero avere un motivo legittimo che eseguire l'accesso da questi paesi/aree bloccate. Ad esempio, potrebbero essere in viaggio in uno di questi paesi per motivi di lavoro o personali. In questo esempio, i criteri di accesso condizionale per bloccare questi paesi/aree geografiche può avere un gruppo di sicurezza cloud dedicato per gli utenti che sono esclusi dai criteri. Gli utenti che devono avere l'accesso anche in viaggio, possono aggiungersi al gruppo usando la [gestione gruppi self-service di Azure AD](../users-groups-roles/groups-self-service-management.md).

Un altro esempio potrebbe essere che si dispone di un criterio di accesso condizionale che [l'autenticazione legacy di blocchi per la maggior parte degli utenti](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Microsoft consiglia di bloccare l'uso di protocolli legacy nel tenant per migliorare il comportamento di sicurezza. Tuttavia, in presenza di utenti che devono assolutamente usare metodi di autenticazione legacy per accedere alle risorse tramite Office 2010 o client basati su SMTP/IMAP o POP, è possibile escludere gli utenti dai criteri che bloccano i metodi di autenticazione legacy.

## <a name="why-are-exclusions-challenging"></a>Perché le esclusioni sono complesse?

In Azure AD, è possibile definire l'ambito di un criterio di accesso condizionale per un set di utenti. È anche possibile escludere alcuni di questi utenti, selezionare i ruoli Azure AD, singoli utenti o gli utenti guest di utenti. È importante ricordare che quando vengono configurate queste esclusioni, la finalità dei criteri non può essere applicata a questi utenti. Se le esclusioni venissero configurate come un elenco di singoli utenti o tramite un gruppo di sicurezza locale legacy, risulterebbero limitati la visibilità di questo elenco di esclusioni (gli utenti potrebbero non sapere della sua esistenza) e il controllo dell'amministratore IT (gli utenti possono richiedere l'inserimento nel gruppo di sicurezza per evitare i criteri). Inoltre, gli utenti che risultano qualificati per l'esclusione una volta, potrebbero non averne più bisogno o non essere più idonei.

All'inizio di un'esclusione, l'elenco di utenti che possono ignorare i criteri è in genere breve. Con il passare del tempo, il numero degli utenti esclusi cresce, così come l'elenco. A un certo punto, diventa necessario verificare l'elenco e accertarsi che ognuno di questi utenti debba continuare a essere escluso. La gestione dell'elenco da un punto di vista tecnico può essere relativamente semplice, ma chi prende le decisioni aziendali e come ci si può assicurare che tutto ciò sia controllabile?

Tuttavia, se si configura l'esclusione per i criteri di accesso condizionale usando un gruppo di Azure AD, quindi è possibile utilizzare le verifiche di accesso come un controllo di compensazione, al fine di promuovere la visibilità e ridurre il numero di utenti che dispongono di un'eccezione.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Come creare un gruppo di esclusioni in Criteri di accesso condizionale

Seguire questi passaggi per creare un nuovo gruppo di Azure AD e un criterio di accesso condizionale che non si applica a tale gruppo.

### <a name="create-an-exclusion-group"></a>Creare un gruppo di esclusione

1. Accedere al portale di Azure.

1. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory** e quindi su **Gruppi**.

1. Nel menu in alto fare clic su **Nuovo gruppo** per aprire il riquadro del gruppo.

1. Selezionare **Sicurezza** nell'elenco **Tipo gruppo**. Specificare un nome e una descrizione.

1. Assicurarsi di impostare il tipo di **Appartenenza** su **Assegnato**.

1. Selezionare gli utenti che devono far parte di questo gruppo di esclusione e quindi fare clic su **Crea**.

    ![Nuovo riquadro di gruppo in Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Creare un criterio di accesso condizionale che esclude il gruppo

A questo punto è possibile creare un criterio di accesso condizionale che utilizza questo gruppo di esclusione.

1. Nel riquadro di spostamento a sinistra, fare clic su **Azure Active Directory** e quindi fare clic su **accesso condizionale** per aprire il **criteri** pannello.

1. Fare clic su **Nuovi criteri** per aprire il riquadro **Nuovo**.

1. Specificare un nome.

1. In Assegnazioni fare clic su **Utenti e gruppi**.

1. Nella scheda **Includi** selezionare **Tutti gli utenti**.

1. Nella scheda **Escludi** aggiungere un segno di spunta per **Utenti e gruppi** e quindi fare clic su **Selezionare gli utenti esclusi**.

1. Selezionare il gruppo di esclusione creato.

    > [!NOTE]
    > Come procedura consigliata, è consigliabile escludere almeno un account amministratore dai criteri durante i test per assicurarsi di non rimanere bloccati fuori dal tenant.

1. Continuare con l'impostazione di criteri di accesso condizionale in base alle esigenze dell'organizzazione.

    ![Selezionare il riquadro di utenti esclusi nell'accesso condizionale](./media/conditional-access-exclusion/select-excluded-users.png)

Questo articolo verranno illustrati due esempi in cui è possibile usare le verifiche di accesso per gestire l'esclusione di criteri di accesso condizionale.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Esempio 1: Verifica di accesso per gli utenti che accedono da bloccato paesi/aree geografiche

Supponiamo di che avere un criterio di accesso condizionale che blocca l'accesso da determinati paesi/aree geografiche. I criteri includono un gruppo escluso dai criteri. Ecco una verifica di accesso consigliata per controllare i membri del gruppo.

> [!NOTE]
> Un amministratore globale o un ruolo utente amministratore deve creare verifiche di accesso.

1. La verifica verrà eseguita ogni settimana.

2. Non è prevista la fine, per assicurarsi di mantenere sempre aggiornato questo gruppo di esclusione.

3. Tutti i membri di questo gruppo saranno inclusi nell'ambito per la revisione.

4. Ogni utente disporrà di self-attestare che devono comunque avere l'accesso da questi paesi/aree bloccate, pertanto devono comunque essere un membro del gruppo.

5. Se l'utente non risponde alla richiesta di revisione, verrà automaticamente rimosso dal gruppo e pertanto non può più accedere al tenant durante il viaggio per questi paesi/aree geografiche.

6. Abilitare le notifiche tramite posta elettronica, in modo che gli utenti ricevano notifica dell'inizio e del completamento della verifica di accesso.

    ![Creare un riquadro di revisione di accesso, ad esempio 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Esempio 2 Verifica di accesso per gli utenti che accedono con l'autenticazione legacy

Supponiamo di che avere un criterio di accesso condizionale che blocca l'accesso per gli utenti che usano l'autenticazione legacy e versioni precedenti del client. I criteri includono un gruppo escluso dai criteri. Ecco una verifica di accesso consigliata per controllare i membri del gruppo.

1. Questa verifica dovrà essere periodica.

2. Tutti i membri del gruppo dovranno essere verificati.

3. Può essere configurata in modo da elencare i responsabili delle unità aziendali come revisori selezionati.

4. Applicare automaticamente i risultati e rimuovere gli utenti non approvati per continuare a usare i metodi di autenticazione legacy.

5. Potrebbe essere utile abilitare le raccomandazioni, in modo che i revisori di gruppi di grandi dimensioni possano prendere facilmente le decisioni necessarie.

6. Abilitare le notifiche tramite posta elettronica, in modo che gli utenti ricevano notifica dell'inizio e del completamento della verifica di accesso.

    ![Creare un riquadro di revisione di accesso, ad esempio 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Suggerimento avanzato**: se esistono molti gruppi di esclusione e pertanto è necessario creare più verifiche di accesso, è ora disponibile un'API nell'endpoint beta di Microsoft Graph che consente di crearli e gestirli a livello di codice. Per iniziare, vedere le [informazioni di riferimento per l'API delle verifiche di accesso di Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) e [Example of retrieving Azure AD access reviews via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096) (Esempio di recupero delle verifiche di accesso di Azure AD tramite Microsoft Graph).

## <a name="access-review-results-and-audit-logs"></a>Risultati delle verifiche di accesso e log di controllo

Ora che hai tutti gli elementi sul posto, gruppi, criteri di accesso condizionale e le verifiche di accesso, è possibile monitorare e tenere traccia dei risultati di queste verifiche.

1. Nel portale di Azure aprire il pannello **Verifiche di accesso**.

1. Aprire il controllo e il programma creati per gestire il gruppo di esclusione.

1. Fare clic su **Risultati** per vedere chi è stato approvato per rimanere nell'elenco e chi è stato rimosso.

    ![Verifiche di accesso Mostra i risultati che è stata approvata](./media/conditional-access-exclusion/access-reviews-results.png)

1. Fare quindi clic su **Log di controllo** per visualizzare le azioni eseguite durante la verifica.

    ![I log di controllo elenco di azioni delle verifiche di accesso](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Gli amministratori IT sanno che gestire gruppi di esclusione per i criteri è talvolta inevitabile. Tuttavia, la manutenzione di questi gruppi, la loro verifica a intervalli regolari da parte del titolare dell'azienda o degli utenti stessi, così come il controllo di queste modifiche possono essere semplificati grazie alle verifiche di accesso di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)
