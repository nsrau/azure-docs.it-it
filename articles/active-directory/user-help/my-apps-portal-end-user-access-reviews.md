---
title: Gestire l'accesso dell'organizzazione alle app & gruppi - Azure ADManage your organization's access to apps & groups - Azure AD
description: Informazioni su come eseguire una verifica dell'accesso per gestire l'accesso di sicurezza per le app e i gruppi dell'organizzazione dal portale delle app personali.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c71195b247af6d5046d88d3e6918a660eddf09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062380"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Eseguire una verifica di accesso dal portale App personali

È possibile usare l'account aziendale o dell'istituto di istruzione con il portale **App personali** basato sul Web per eseguire verifiche di accesso per le app e i gruppi. Le verifiche di accesso consentono di gestire l'accesso obsoleto o di modificare i requisiti di accesso e di verificarne la revisione e l'aggiornamento.

Se non si ha accesso al portale **App personali**, è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Questo contenuto è destinato agli utenti di **App personali**. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Gestire le verifiche di accesso

Se l'amministratore ha concesso l'autorizzazione per eseguire le proprie verifiche di accesso, è possibile gestire l'accesso a gruppi o app dal riquadro Verifiche di **accesso** nella pagina del portale **App** personali.

>[!Note]
>Se il riquadro **Revisioni** di accesso non è visualizzato, significa che non si dispone dell'autorizzazione per eseguire le verifiche di accesso o che non sono presenti revisioni in sospeso in attesa della approvazione. Se si ritiene di dover accedere al riquadro, contattare l'helpdesk per assistenza.

## <a name="to-perform-your-access-reviews"></a>Per eseguire le verifiche di accesso

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Aprire il Web browser https://myapps.microsoft.come passare a o utilizzare il collegamento fornito dall'organizzazione. Ad esempio, si potrebbe essere indirizzati a una https://myapps.microsoft.com/contoso.compagina personalizzata per l'organizzazione, ad esempio .

    Viene visualizzata la pagina **App** che mostra tutte le app basate su cloud di proprietà dell'organizzazione e disponibili per l'uso.

    ![Pagina App nel portale App personali](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Selezionare il riquadro **Revisioni** di accesso per visualizzare un elenco di verifiche di accesso in attesa dell'approvazione.

    ![Pagina delle verifiche di accesso con le verifiche di accesso in sospeso per l'organizzazione](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Selezionare **Avvia revisione** per avviare la verifica di accesso.

5. Esaminare l'accesso e determinare se è ancora necessario.

    ![Pagina di revisione di accesso, che mostra i dettagli della revisione](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Se sei un amministratore e sei autorizzato a controllare l'accesso della tua organizzazione a gruppi e app, vedrai una pagina diversa. Per altre informazioni sulla revisione di gruppi o app per l'organizzazione, vedere [Esaminare l'accesso a gruppi o applicazioni nelle verifiche](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)di accesso di Azure AD.

6. Selezionare **Sì** per mantenere l'accesso o **No** per rimuoverlo.

    Se si seleziona **Sì**, potrebbe essere necessario specificare una giustificazione nella casella **Motivo.**

    ![Pagina di verifica di Access, che mostra la casella Motivo con testo di esempio](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Selezionare **Submit**.

    La verifica dell'accesso è completa e si torna al portale **App personali.**

    >[!Note]
    >È possibile modificare l'accesso in qualsiasi momento fino al termine del periodo di verifica dell'accesso. Se rimuovi l'accesso a un'app o a un gruppo, questo non viene rimosso immediatamente. La rimozione avviene quando termina il periodo di verifica dell'accesso o quando un amministratore chiude la revisione.

## <a name="next-steps"></a>Passaggi successivi

- [Accedere alle app nel portale delle app personali](my-apps-portal-end-user-access.md)
- [Cambiare le informazioni del profilo](my-apps-portal-end-user-update-profile.md)
- [Visualizzare e aggiornare le informazioni relative ai gruppi](my-apps-portal-end-user-groups.md)
