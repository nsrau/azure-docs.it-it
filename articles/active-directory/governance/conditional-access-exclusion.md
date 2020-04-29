---
title: Gestire gli utenti esclusi dai criteri di accesso condizionale-Azure AD
description: Informazioni su come usare le verifiche di accesso di Azure Active Directory (Azure AD) per gestire gli utenti che sono stati esclusi dai criteri di accesso condizionale
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91bb5a342eea079b6e9abcf109ad472151d3c13d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144491"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Usare le verifiche di accesso Azure AD per gestire gli utenti esclusi dai criteri di accesso condizionale

In un mondo ideale, tutti gli utenti seguono i criteri di accesso per proteggere l'accesso alle risorse dell'organizzazione. Esistono tuttavia casi aziendali che richiedono di introdurre eccezioni. Questo articolo illustra alcuni esempi di situazioni in cui potrebbero essere necessarie esclusioni. L'amministratore IT può gestire questa attività, evitare la supervisione delle eccezioni dei criteri e fornire ai revisori la prova che tali eccezioni vengano esaminate regolarmente utilizzando le verifiche di accesso Azure Active Directory (Azure AD).

>[!NOTE]
> Per usare le verifiche di accesso di Azure AD è necessaria una licenza di valutazione o una licenza a pagamento Azure Premium P2 o Enterprise Mobility + Security E5 valida. Per ulteriori informazioni, vedere [Azure Active Directory edizioni](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Motivi per escludere utenti dai criteri

Si supponga, ad esempio, che l'amministratore decida di usare [Azure ad accesso condizionale](../conditional-access/overview.md) per richiedere l'autenticazione a più fattori e limitare le richieste di autenticazione a reti o dispositivi specifici. Durante la pianificazione della distribuzione, si rende conto che non tutti gli utenti sono in grado di soddisfare tali requisiti. Ad esempio, è possibile avere utenti che lavorano da uffici remoti, non appartenenti alla rete interna. Potrebbe anche essere necessario gestire gli utenti che si connettono usando dispositivi non supportati durante l'attesa della sostituzione dei dispositivi. In breve, è necessario che gli utenti accedano ed eseguano il proprio lavoro in modo da escluderli dai criteri di accesso condizionale.

Un altro esempio potrebbe essere l'uso di [località denominate](../conditional-access/location-condition.md) in accesso condizionale per specificare un set di paesi e aree da cui non si vuole consentire agli utenti di accedere al tenant.

![Località denominate nell'accesso condizionale](./media/conditional-access-exclusion/named-locations.png)

Sfortunatamente, alcuni utenti potrebbero ancora avere un motivo valido per eseguire l'accesso da questi paesi/aree bloccati. Ad esempio, è possibile che gli utenti viaggino per lavoro ed è necessario accedere alle risorse aziendali. In questo caso, i criteri di accesso condizionale per bloccare questi paesi/aree geografiche potrebbero usare un gruppo di sicurezza cloud per gli utenti esclusi dal criterio. Gli utenti che devono avere l'accesso anche in viaggio, possono aggiungersi al gruppo usando la [gestione gruppi self-service di Azure AD](../users-groups-roles/groups-self-service-management.md).

Un altro esempio potrebbe essere che i criteri di accesso condizionale [bloccano l'autenticazione legacy per la maggior parte degli utenti](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Tuttavia, se si dispone di alcuni utenti che devono usare i metodi di autenticazione legacy per accedere alle risorse tramite i client basati su Office 2010 o IMAP/SMTP/POP, è possibile escludere questi utenti dal criterio che blocca i metodi di autenticazione legacy.

>[!NOTE]
>Microsoft consiglia di bloccare l'uso di protocolli legacy nel tenant per migliorare il comportamento di sicurezza.

## <a name="why-are-exclusions-challenging"></a>Perché le esclusioni sono complesse?

In Azure AD, è possibile definire l'ambito di un criterio di accesso condizionale a un set di utenti. È anche possibile configurare le esclusioni selezionando Azure AD ruoli, singoli utenti o Guest. Tenere presente che quando vengono configurate le esclusioni, non è possibile applicare lo scopo dei criteri agli utenti esclusi. Se le esclusioni vengono configurate usando un elenco di utenti o gruppi di sicurezza locali legacy, la visibilità delle esclusioni sarà limitata. Di conseguenza:

- Gli utenti potrebbero non essere a conoscenza del fatto che sono esclusi.

- Gli utenti possono partecipare al gruppo di sicurezza per ignorare i criteri.

- Gli utenti esclusi possono essere qualificati per l'esclusione prima, ma potrebbero non essere più idonei.

Spesso, quando si configura un'esclusione per la prima volta, è disponibile un elenco di utenti che ignorano i criteri. Nel corso del tempo, un numero sempre maggiore di utenti viene aggiunto all'esclusione e l'elenco cresce. A un certo punto, è necessario rivedere l'elenco e verificare che ognuno di questi utenti sia ancora idoneo per l'esclusione. La gestione dell'elenco di esclusioni, da un punto di vista tecnico, può essere relativamente semplice, ma chi prende le decisioni aziendali e come verificare che sia controllabile? Tuttavia, se si configura l'esclusione utilizzando un gruppo di Azure AD, è possibile utilizzare le verifiche di accesso come controllo di compensazione, per guidare la visibilità e ridurre il numero di utenti esclusi.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Come creare un gruppo di esclusioni in un criterio di accesso condizionale

Attenersi alla procedura seguente per creare un nuovo gruppo di Azure AD e un criterio di accesso condizionale che non si applica a tale gruppo.

### <a name="create-an-exclusion-group"></a>Creare un gruppo di esclusione

1. Accedere al portale di Azure.

2. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory** e quindi su **Gruppi**.

3. Nel menu in alto fare clic su **Nuovo gruppo** per aprire il riquadro del gruppo.

4. Selezionare **Sicurezza** nell'elenco **Tipo gruppo**. Specificare un nome e una descrizione.

5. Assicurarsi di impostare il tipo di **Appartenenza** su **Assegnato**.

6. Selezionare gli utenti che devono far parte di questo gruppo di esclusione e quindi fare clic su **Crea**.

![Riquadro nuovo gruppo in Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Creare un criterio di accesso condizionale che escluda il gruppo

A questo punto è possibile creare criteri di accesso condizionale che usano questo gruppo di esclusioni.

1. Nel riquadro di spostamento a sinistra fare clic su **Azure Active Directory** e quindi fare clic su **accesso condizionale** per aprire il pannello **criteri** .

2. Fare clic su **Nuovi criteri** per aprire il riquadro **Nuovo**.

3. Specificare un nome.

4. In Assegnazioni fare clic su **Utenti e gruppi**.

5. Nella scheda **Includi** selezionare **Tutti gli utenti**.

6. Nella scheda **Escludi** aggiungere un segno di spunta a **utenti e gruppi** e quindi fare clic su **Seleziona utenti esclusi**.

7. Selezionare il gruppo di esclusione creato.

   > [!NOTE] 
   > Come procedura consigliata, è consigliabile escludere almeno un account amministratore dai criteri durante i test per assicurarsi di non rimanere bloccati fuori dal tenant.

8. Continuare con la configurazione dei criteri di accesso condizionale in base ai requisiti dell'organizzazione.

![Selezionare il riquadro utenti esclusi nell'accesso condizionale](./media/conditional-access-exclusion/select-excluded-users.png)
  
Ecco due esempi in cui è possibile usare le verifiche di accesso per gestire le esclusioni nei criteri di accesso condizionale.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Esempio 1: verifica di accesso per gli utenti che accedono da paesi o aree bloccati

Supponiamo di disporre di un criterio di accesso condizionale che blocca l'accesso da determinati paesi/aree. I criteri includono un gruppo escluso dai criteri. Ecco una verifica di accesso consigliata per controllare i membri del gruppo.

> [!NOTE] 
> Per creare le verifiche di accesso, è necessario un ruolo amministratore globale o Amministratore utenti.

1. La revisione viene eseguita ogni settimana.

2. Non terminerà mai per assicurarsi che il gruppo di esclusioni venga mantenuto il più aggiornato.

3. Tutti i membri di questo gruppo saranno inclusi nell'ambito per la revisione.

4. Ogni utente dovrà attestare autonomamente che necessitano ancora dell'accesso da tali paesi o aree geografiche bloccate, di conseguenza è necessario essere un membro del gruppo.

5. Se l'utente non risponde alla richiesta di revisione, verrà automaticamente rimosso dal gruppo e non avrà più accesso al tenant durante il viaggio in questi paesi/aree geografiche.

6. Abilitare le notifiche tramite posta elettronica per consentire agli utenti di conoscere l'inizio e il completamento della verifica di accesso.

    ![Creare un riquadro di verifica di accesso, ad esempio 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Esempio 2: Verifica di accesso per gli utenti che accedono con l'autenticazione legacy

Si immagini di disporre di un criterio di accesso condizionale che blocca l'accesso per gli utenti che usano l'autenticazione legacy e le versioni precedenti del client e include un gruppo escluso dai criteri. Ecco una verifica di accesso consigliata per controllare i membri del gruppo.

1. Questa verifica dovrà essere periodica.

2. Tutti i membri del gruppo dovranno essere verificati.

3. Può essere configurata in modo da elencare i responsabili delle unità aziendali come revisori selezionati.

4. Applicare automaticamente i risultati e rimuovere gli utenti non approvati per continuare a usare i metodi di autenticazione legacy.

5. Potrebbe essere utile abilitare le raccomandazioni, in modo che i revisori di gruppi di grandi dimensioni possano prendere facilmente le decisioni necessarie.

6. Abilitare le notifiche tramite posta elettronica, in modo che gli utenti ricevano notifica dell'inizio e del completamento della verifica di accesso.

    ![Creare un riquadro di verifica di accesso per l'esempio 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Se sono presenti molti gruppi di esclusione e pertanto è necessario creare più verifiche di accesso, è ora disponibile un'API nell'endpoint Microsoft Graph beta che consente di crearle e gestirle a livello di codice. Per iniziare, vedere le [informazioni di riferimento per l'API delle verifiche di accesso di Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) e [Example of retrieving Azure AD access reviews via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096) (Esempio di recupero delle verifiche di accesso di Azure AD tramite Microsoft Graph).

## <a name="access-review-results-and-audit-logs"></a>Risultati delle verifiche di accesso e log di controllo

Ora che sono disponibili tutti gli elementi, il gruppo, i criteri di accesso condizionale e le verifiche di accesso, è il momento di monitorare e tenere traccia dei risultati di queste recensioni.

1. Nel portale di Azure aprire il pannello verifiche di **accesso** .

2. Aprire il controllo e il programma creati per gestire il gruppo di esclusione.

3. Fare clic su **Risultati** per vedere chi è stato approvato per rimanere nell'elenco e chi è stato rimosso.

    ![Risultati delle verifiche di accesso mostrano chi è stato approvato](./media/conditional-access-exclusion/access-reviews-results.png)

4. Fare quindi clic su **Log di controllo** per visualizzare le azioni eseguite durante la verifica.

    ![Verifiche di accesso elenco dei log di controllo azioni](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Gli amministratori IT sanno che gestire gruppi di esclusione per i criteri è talvolta inevitabile. Tuttavia, la gestione di questi gruppi, la loro revisione a intervalli regolari da parte del proprietario dell'azienda o degli utenti stessi, e il controllo di queste modifiche possono essere rese più semplici con le verifiche di accesso Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)
