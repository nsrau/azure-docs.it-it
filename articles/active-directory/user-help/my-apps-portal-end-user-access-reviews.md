---
title: Gestire l'accesso dell'organizzazione ad app e gruppi - Azure AD
description: Informazioni su come eseguire una verifica di accesso per gestire l'accesso sicuro per le app e i gruppi dell'organizzazione dal portale App personali.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/19/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: fbea4ef39b970f667c5dddec52f56a428d401f65
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277466"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Eseguire una verifica di accesso dal portale App personali

È possibile usare l'account aziendale o dell'istituto di istruzione con il portale basato sul Web **App personali** per eseguire verifiche di accesso per le app e i gruppi. Le verifiche di accesso consentono di gestire gli accessi non aggiornati o modificare i requisiti di accesso e garantire che vengano esaminati e aggiornati.

Se non si ha accesso al portale **App personali**, è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Questo contenuto è destinato agli utenti di **App personali**. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](../manage-apps/index.yml).

## <a name="manage-access-reviews"></a>Gestire le verifiche di accesso

Se l'amministratore ha concesso l'autorizzazione per eseguire le proprie verifiche di accesso, è possibile gestire l'accesso a gruppi o app dal riquadro **Verifiche di accesso** nella pagina del portale **App personali**.

>[!Note]
>Se il riquadro **Verifiche di accesso** non è visualizzato, significa che non si è autorizzati a eseguire le verifiche di accesso o che non sono presenti verifiche in sospeso in attesa di approvazione. Se si ritiene di dover accedere al riquadro, contattare il supporto tecnico per assistenza.

## <a name="to-perform-your-access-reviews"></a>Per eseguire le verifiche di accesso

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Aprire il Web browser e passare a https://myapps.microsoft.com o usare il collegamento fornito dall'organizzazione. È ad esempio possibile che si venga indirizzati a una pagina personalizzata per l'organizzazione come https://myapps.microsoft.com/contoso.com.

    Verrà visualizzata la pagina **App**, che mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

    ![Pagina App nel portale App personali](media/my-apps-portal/my-apps-home.png)

3. Selezionare il riquadro **Verifiche di accesso** per visualizzare un elenco di verifiche di accesso in attesa di approvazione.

    ![Pagina Verifiche di accesso con le verifiche di accesso in sospeso per l'organizzazione](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Selezionare **Inizia la verifica** per avviare la verifica di accesso.

5. Verificare l'accesso e determinare se è ancora necessario.

    ![Pagina Verifica di accesso che mostra i dettagli della verifica](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Se si è un amministratore e si è autorizzati a verificare l'accesso dell'organizzazione a gruppi e app, verrà visualizzata una pagina diversa. Per altre informazioni su come verificare i gruppi o le app per l'organizzazione, vedere [Verificare l'accesso a gruppi o applicazioni nelle verifiche di accesso di Azure AD](../governance/perform-access-review.md).

6. Selezionare **Sì** per mantenere l'accesso o **No** per rimuoverlo.

    Se si seleziona **Sì**, potrebbe essere necessario specificare una giustificazione nella casella **Motivo**.

    ![Pagina Verifica di accesso che mostra la casella Motivo con un testo di esempio](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Selezionare **Submit** (Invia).

    La verifica di accesso è stata completata e si verrà reindirizzati al portale **App personali**.

    >[!Note]
    >È possibile modificare l'accesso in qualsiasi momento fino al termine del periodo di verifica di accesso. Se si rimuove l'accesso a un'app o a un gruppo, questo non viene rimosso immediatamente. La rimozione si verifica al termine del periodo di verifica di accesso o quando un amministratore chiude la revisione.

## <a name="next-steps"></a>Passaggi successivi

- [Accedere e usare le app nel portale App personali](my-apps-portal-end-user-access.md)
- [Cambiare le informazioni del profilo](my-apps-portal-end-user-update-profile.md)
- [Visualizzare e aggiornare le informazioni relative ai gruppi](my-apps-portal-end-user-groups.md)