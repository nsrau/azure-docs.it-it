---
title: Gestire gli utenti esclusi dai criteri di accesso condizionale - Azure AD
description: Informazioni su come usare le verifiche di accesso di Azure Active Directory (Azure AD) per gestire gli utenti esclusi dai criteri di accesso condizionale
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
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: def74cccb3ad2c45a18210cc9d5a0d7ff0ba76f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88783689"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Usare le verifiche di accesso di Azure AD per gestire gli utenti esclusi dai criteri di accesso condizionale

In una situazione ideale, tutti gli utenti rispettano i criteri di accesso per proteggere l'accesso alle risorse dell'organizzazione. Esistono tuttavia casi aziendali che richiedono di introdurre eccezioni. Questo articolo illustra alcuni esempi di situazioni in cui potrebbero essere necessarie esclusioni. L'amministratore IT può gestire questa esigenza, evitare sviste nelle eccezioni dei criteri e offrire ai revisori una prova della revisione regolare delle eccezioni tramite le verifiche di accesso di Azure Active Directory (Azure AD).

>[!NOTE]
> Per usare le verifiche di accesso di Azure AD è necessaria una licenza di valutazione o una licenza a pagamento Azure Premium P2 o Enterprise Mobility + Security E5 valida. Per altre informazioni, vedere [Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Motivi per escludere utenti dai criteri

Si supponga ad esempio che si decida in qualità di amministratore di usare l'[accesso condizionale di Azure AD](../conditional-access/concept-conditional-access-policy-common.md) per richiedere l'autenticazione a più fattori (MFA) e limitare le richieste di autenticazione a reti o dispositivi specifici. Durante la pianificazione della distribuzione, ci si rende conto che non tutti gli utenti sono in grado di soddisfare questi requisiti. Ad esempio, è possibile che alcuni utenti lavorino da uffici remoti non appartenenti alla rete interna. Potrebbe anche essere necessario gestire gli utenti che si connettono usando dispositivi non supportati in attesa della sostituzione dei dispositivi. In breve, poiché è necessario che gli utenti accedano ed eseguano il proprio lavoro, si escludono gli utenti dai criteri di accesso condizionale.

Come ulteriore esempio, è possibile che si usino [località denominate](../conditional-access/location-condition.md) nell'accesso condizionale per specificare un set di paesi e aree geografiche da cui non si vuole consentire agli utenti di accedere ai loro tenant.

![Località denominate nell'accesso condizionale](./media/conditional-access-exclusion/named-locations.png)

Sfortunatamente, alcuni utenti potrebbero ancora avere un motivo valido per eseguire l'accesso dai paesi o dalle aree bloccate. Ad esempio, è possibile che gli utenti viaggino per lavoro e necessitino di accedere alle risorse aziendali. In questo caso, i criteri di accesso condizionale per bloccare i paesi o le aree geografiche potrebbero usare un gruppo di sicurezza cloud per gli utenti esclusi dai criteri. Gli utenti che devono avere l'accesso anche in viaggio, possono aggiungersi al gruppo usando la [gestione gruppi self-service di Azure AD](../users-groups-roles/groups-self-service-management.md).

Un altro esempio potrebbe essere la presenza di criteri di accesso condizionale che [bloccano l'autenticazione legacy per la maggior parte degli utenti](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Tuttavia, in presenza di utenti che devono usare metodi di autenticazione legacy per accedere alle risorse tramite Office 2010 o client basati su SMTP/IMAP o POP, è possibile escludere gli utenti dai criteri che bloccano i metodi di autenticazione legacy.

>[!NOTE]
>Microsoft consiglia di bloccare l'uso di protocolli legacy nel tenant per migliorare il comportamento di sicurezza.

## <a name="why-are-exclusions-challenging"></a>Perché le esclusioni sono complesse?

In Azure AD è possibile definire un set di utenti come ambito per i criteri di accesso condizionale. È anche possibile configurare le esclusioni selezionando ruoli, singoli utenti o guest di Azure AD. Tenere presente che quando vengono configurate le esclusioni, non è possibile applicare la finalità dei criteri agli utenti esclusi. Se le esclusioni vengono configurate usando un elenco di utenti o gruppi di sicurezza locali legacy, la visibilità delle esclusioni sarà limitata. Di conseguenza:

- Gli utenti potrebbero non essere a conoscenza del fatto che sono esclusi.

- Gli utenti possono unirsi al gruppo di sicurezza per ignorare i criteri.

- Gli utenti esclusi possono essere stati idonei all'esclusione ma non esserlo più.

Spesso, quando si configura un'esclusione per la prima volta, è disponibile un elenco degli utenti che ignorano i criteri. Man mano che vengono aggiunti utenti all'esclusione, l'elenco diventa più lungo. A un certo punto diventa necessario rivedere l'elenco e verificare che ognuno di questi utenti sia ancora idoneo all'esclusione. La gestione dell'elenco delle esclusioni da un punto di vista tecnico può essere relativamente semplice, ma chi prende le decisioni aziendali e come ci si può assicurare che tutto ciò sia controllabile? Tuttavia, se si configura l'esclusione usando un gruppo di Azure AD, è possibile usare le verifiche di accesso come controllo compensativo, al fine di promuovere la visibilità e ridurre il numero di utenti esclusi.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Come creare un gruppo di esclusione nei criteri di accesso condizionale

Seguire questa procedura per creare un nuovo gruppo di Azure AD e criteri di accesso condizionale non applicabili al gruppo.

### <a name="create-an-exclusion-group"></a>Creare un gruppo di esclusione

1. Accedere al portale di Azure.

2. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory** e quindi su **Gruppi**.

3. Nel menu in alto fare clic su **Nuovo gruppo** per aprire il riquadro del gruppo.

4. Selezionare **Sicurezza** nell'elenco **Tipo gruppo**. Specificare un nome e una descrizione.

5. Assicurarsi di impostare il tipo di **Appartenenza** su **Assegnato**.

6. Selezionare gli utenti che devono far parte di questo gruppo di esclusione e quindi fare clic su **Crea**.

![Riquadro del nuovo gruppo in Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Creare criteri di accesso condizionale che escludono il gruppo

È ora possibile creare criteri di accesso condizionale che usano il gruppo di esclusione.

1. Nel menu di spostamento di sinistra fare clic su **Azure Active Directory** e quindi su **Accesso condizionale** per aprire il pannello **Criteri**.

2. Fare clic su **Nuovi criteri** per aprire il riquadro **Nuovo**.

3. Specificare un nome.

4. In Assegnazioni fare clic su **Utenti e gruppi**.

5. Nella scheda **Includi** selezionare **Tutti gli utenti**.

6. Nella scheda **Escludi** aggiungere un segno di spunta per **Utenti e gruppi** e quindi fare clic su **Selezionare gli utenti esclusi**.

7. Selezionare il gruppo di esclusione creato.

   > [!NOTE] 
   > Come procedura consigliata, è consigliabile escludere almeno un account amministratore dai criteri durante i test per assicurarsi di non rimanere bloccati fuori dal tenant.

8. Continuare con la configurazione dei criteri di accesso condizionale in base ai requisiti dell'organizzazione.

![Selezionare il riquadro degli utenti esclusi nell'accesso condizionale](./media/conditional-access-exclusion/select-excluded-users.png)
  
Di seguito vengono illustrati due esempi in cui è possibile usare le verifiche di accesso per gestire le esclusioni nei criteri di accesso condizionale.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Esempio 1: Verifica di accesso per gli utenti che accedono da paesi o aree bloccate

Si supponga che esistano criteri di accesso condizionale per bloccare l'accesso da determinati paesi o aree. I criteri includono un gruppo escluso dai criteri. Ecco una verifica di accesso consigliata per controllare i membri del gruppo.

> [!NOTE] 
> Per creare le verifiche di accesso è richiesto il ruolo Amministratore globale o Amministratore utenti.

1. La verifica verrà eseguita ogni settimana.

2. Non è prevista la fine, per assicurarsi di mantenere sempre aggiornato il gruppo di esclusione.

3. Tutti i membri di questo gruppo saranno inclusi nell'ambito per la revisione.

4. Poiché ogni utente dovrà attestare di avere ancora la necessità di accedere dai paesi o dalle aree bloccate, è necessario che l'utente sia ancora membro del gruppo.

5. Se l'utente non risponde alla richiesta di revisione, verrà automaticamente rimosso dal gruppo e non avrà più accesso al tenant durante i viaggi in questi paesi o aree.

6. Abilitare le notifiche tramite posta elettronica in modo che gli utenti siano a conoscenza dell'inizio e del completamento della verifica di accesso.

    ![Creare un riquadro di verifica di accesso per l'esempio 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Esempio 2: Verifica di accesso per gli utenti che accedono con l'autenticazione legacy

Si supponga che esistano criteri di accesso condizionale che bloccano l'accesso agli utenti che usano l'autenticazione legacy e versioni precedenti del client e includono un gruppo escluso dai criteri. Ecco una verifica di accesso consigliata per controllare i membri del gruppo.

1. Questa verifica dovrà essere periodica.

2. Tutti i membri del gruppo dovranno essere verificati.

3. Può essere configurata in modo da elencare i responsabili delle unità aziendali come revisori selezionati.

4. Applicare automaticamente i risultati e rimuovere gli utenti non approvati per continuare a usare i metodi di autenticazione legacy.

5. Potrebbe essere utile abilitare le raccomandazioni, in modo che i revisori di gruppi di grandi dimensioni possano prendere facilmente le decisioni necessarie.

6. Abilitare le notifiche tramite posta elettronica, in modo che gli utenti ricevano notifica dell'inizio e del completamento della verifica di accesso.

    ![Creare un riquadro di verifica di accesso per l'esempio 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Se esistono molti gruppi di esclusione e pertanto è necessario creare più verifiche di accesso, è ora disponibile un'API nell'endpoint beta di Microsoft Graph che consente di crearli e gestirli a livello di codice. Per iniziare, vedere le [informazioni di riferimento per l'API delle verifiche di accesso di Azure AD](/graph/api/resources/accessreviews-root?view=graph-rest-beta) e [Example of retrieving Azure AD access reviews via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096) (Esempio di recupero delle verifiche di accesso di Azure AD tramite Microsoft Graph).

## <a name="access-review-results-and-audit-logs"></a>Risultati delle verifiche di accesso e log di controllo

Ora che tutti gli elementi sono pronti (gruppo, criteri di accesso condizionale e verifiche di accesso), è possibile monitorare e tenere traccia dei risultati di queste verifiche.

1. Nel portale di Azure aprire il pannello **Verifiche di accesso**.

2. Aprire il controllo e il programma creati per gestire il gruppo di esclusione.

3. Fare clic su **Risultati** per vedere chi è stato approvato per rimanere nell'elenco e chi è stato rimosso.

    ![Risultati delle verifiche di accesso che mostrano gli utenti approvati](./media/conditional-access-exclusion/access-reviews-results.png)

4. Fare quindi clic su **Log di controllo** per visualizzare le azioni eseguite durante la verifica.

    ![Log di controllo delle verifiche di accesso con l'elenco delle azioni](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Gli amministratori IT sanno che gestire gruppi di esclusione per i criteri è talvolta inevitabile. Tuttavia, la gestione dei gruppi, la verifica regolare da parte del titolare dell'azienda o degli utenti stessi, così come il controllo di queste modifiche possono essere semplificati grazie alle verifiche di accesso di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)