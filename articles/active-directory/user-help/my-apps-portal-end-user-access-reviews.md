---
title: Eseguire una verifica di accesso dal portale App personali - Azure Active Directory | Microsoft Docs
description: Informazioni su come visualizzare e gestire l'accesso di sicurezza per le App e i gruppi dell'organizzazione.
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
ms.openlocfilehash: 551bbe4e994e15688c59fd395ab762e14927dfda
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482853"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Eseguire una verifica di accesso dal portale App personali
È possibile usare l'account aziendale o dell'istituto di istruzione con il portale **App personali** basato sul Web per visualizzare e avviare molte delle app dell'organizzazione basate sul cloud, per aggiornare alcune informazioni dell'account e del profilo, per visualizzare le informazioni sui **gruppi** e per eseguire **verifiche di accesso** per le app e i gruppi. Se non si ha accesso al portale **App personali**, è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Questo contenuto è destinato agli utenti. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Gestire le verifiche di accesso
Se l'amministratore ha assegnato l'autorizzazione per eseguire il proprio le verifiche di accesso, è possibile gestire l'accesso App o i gruppi dal **verifiche di accesso** dei riquadri per il **My Apps** pagina del portale.

>[!Note]
>Se non viene visualizzato il **verifiche di accesso** riquadro entrambi significa che non si è autorizzati a eseguire le verifiche di accesso o che non sono presenti in sospeso in attesa di approvazione revisioni. Se si ritiene che è necessario avere accesso al riquadro, contattare il supporto tecnico per assistenza.

### <a name="to-perform-your-access-reviews"></a>Per eseguire le verifiche di accesso

1.  Accedere all'account aziendale o dell'istituto di istruzione.

2.  Aprire il web browser e passare a https://myapps.microsoft.com, oppure usare il collegamento fornito dall'organizzazione. Ad esempio, si potrebbe essere indirizzati a una pagina personalizzata per l'organizzazione, ad esempio https://myapps.microsoft.com/contoso.com.

    Il **app** viene visualizzata la pagina che mostra tutte le app basate su cloud proprietà da parte dell'organizzazione e disponibili per l'utilizzo.

    ![Pagina delle app nel portale App personali](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Selezionare il **verifiche di accesso** riquadro per visualizzare un elenco di accesso vengono esaminati in attesa di approvazione.

    ![Pagina con in sospeso le verifiche di accesso per l'organizzazione delle verifiche di accesso](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Selezionare **inizia la verifica** per avviare la verifica di accesso.

5. Verificare l'accesso e determinare se è comunque necessario.

    ![Pagina verifica di accesso, che visualizza i dettagli di revisione](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Se si ha un amministratore e consentito di verificare l'accesso dell'organizzazione a gruppi e App, si noterà una pagina diversa. Per altre informazioni sulla verifica gruppi o App per l'organizzazione, vedere [verificare l'accesso a gruppi o applicazioni in verifiche di accesso AD Azure](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Selezionare **Yes** mantenere l'accesso oppure **No** di rimuovere l'accesso.

    Se si seleziona **Yes**, potrebbe essere necessario specificare una giustificazione nel **motivo** casella.

    ![Controllare l'accesso pagina, che mostra la finestra di motivo con testo di esempio](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Selezionare **Submit** (Invia).

    La verifica di accesso è stata completata ed è restituire il **My Apps** portale.

    >[!Note]
    >È possibile modificare l'accesso in qualsiasi momento fino a quando l'accesso esaminare periodo. Se si rimuove l'accesso a un'app o un gruppo, non viene rimosso immediatamente. La rimozione si verifica quando l'accesso controlla fine o quando un amministratore si chiude la revisione. 

## <a name="next-steps"></a>Passaggi successivi

- [Accedere e usare le app nel portale App personali](my-apps-portal-end-user-access.md).

- [Modificare le informazioni sul profilo](my-apps-portal-end-user-update-profile.md).

- [Visualizzare e aggiornare le informazioni correlate a gruppi](my-apps-portal-end-user-groups.md).