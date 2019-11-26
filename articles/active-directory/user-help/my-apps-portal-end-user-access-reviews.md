---
title: Gestire l'accesso dell'organizzazione alle app & gruppi-Azure AD
description: Informazioni su come eseguire una verifica di accesso per gestire l'accesso alla sicurezza per le app e i gruppi dell'organizzazione dal portale delle app personali.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 232f4b6fde84b7e1cd706b62e0ba9a0998b4171e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231830"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Eseguire una verifica di accesso dal portale app personali

È possibile usare l'account aziendale o dell'istituto di istruzione con il portale **App personali** basato sul Web per visualizzare e avviare molte delle app dell'organizzazione basate sul cloud, per aggiornare alcune informazioni dell'account e del profilo, per visualizzare le informazioni sui **gruppi** e per eseguire **verifiche di accesso** per le app e i gruppi. Se non si ha accesso al portale **App personali**, è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Questo contenuto è destinato agli utenti. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Gestire le verifiche di accesso

Se l'amministratore ha concesso l'autorizzazione per eseguire le proprie verifiche di accesso, è possibile gestire l'accesso ai gruppi o alle app dal riquadro verifiche di **accesso** nella pagina del portale delle **app personali** .

>[!Note]
>Se non viene visualizzato il riquadro verifiche di **accesso** , significa che non si dispone dell'autorizzazione per eseguire le verifiche di accesso o che non sono presenti verifiche in sospeso in attesa di approvazione. Se si ritiene di avere accesso al riquadro, contattare il supporto tecnico per assistenza.

### <a name="to-perform-your-access-reviews"></a>Per eseguire le verifiche di accesso

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Aprire il Web browser e passare a https://myapps.microsoft.como usare il collegamento fornito dall'organizzazione. Ad esempio, si potrebbe essere indirizzati a una pagina personalizzata per l'organizzazione, ad esempio https://myapps.microsoft.com/contoso.com.

    Viene visualizzata la pagina **app** , che Mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

    ![Pagina app nel portale app personali](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Selezionare il riquadro verifiche di **accesso** per visualizzare un elenco di verifiche di accesso in attesa dell'approvazione.

    ![Pagina delle verifiche di accesso con verifiche di accesso in sospeso per l'organizzazione](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Selezionare **inizia Revisione** per avviare la verifica dell'accesso.

5. Controllare l'accesso e determinare se è ancora necessario.

    ![Pagina Verifica di accesso, che mostra i dettagli della verifica](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Se si è un amministratore e si è autorizzati a esaminare l'accesso dell'organizzazione a gruppi e app, verrà visualizzata una pagina diversa. Per altre informazioni su come rivedere i gruppi o le app per l'organizzazione, vedere [esaminare l'accesso a gruppi o applicazioni in Azure ad verifiche di accesso](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Selezionare **Sì** **per impedire** l'accesso.

    Se si seleziona **Sì**, potrebbe essere necessario specificare una giustificazione nella casella **motivo** .

    ![Pagina Verifica di accesso, che mostra la casella motivo con testo di esempio](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Selezionare **Submit** (Invia).

    La verifica di accesso è stata completata e si torna al portale **app personali** .

    >[!Note]
    >È possibile modificare l'accesso in qualsiasi momento fino al termine del periodo di verifica dell'accesso. Se si rimuove l'accesso a un'app o a un gruppo, questo non viene rimosso immediatamente. La rimozione si verifica al termine del periodo di verifica dell'accesso o quando un amministratore chiude la revisione.

## <a name="next-steps"></a>Passaggi successivi

- [Accedere e usare le app nel portale app personali](my-apps-portal-end-user-access.md).

- [Modificare le informazioni del profilo](my-apps-portal-end-user-update-profile.md).

- [Visualizzare e aggiornare le informazioni relative ai gruppi](my-apps-portal-end-user-groups.md).
