---
title: Manage your organization's access to apps & groups - Azure AD
description: Learn how to perform an access review to manage security access for your organization's apps and groups from the My Apps portal.
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
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Perform an access review from the My Apps portal

È possibile usare l'account aziendale o dell'istituto di istruzione con il portale **App personali** basato sul Web per visualizzare e avviare molte delle app dell'organizzazione basate sul cloud, per aggiornare alcune informazioni dell'account e del profilo, per visualizzare le informazioni sui **gruppi** e per eseguire **verifiche di accesso** per le app e i gruppi. Se non si ha accesso al portale **App personali**, è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Questo contenuto è destinato agli utenti. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Gestire le verifiche di accesso

If your administrator has given you permission to perform your own access reviews, you can manage your groups or apps access from the **Access reviews** tile on the **My Apps** portal page.

>[!Note]
>If you don't see the **Access reviews** tile it either means that you don't have permission to perform access reviews, or that you don't have any pending reviews waiting for your approval. If you think you should have access to the tile, contact your Help desk for assistance.

### <a name="to-perform-your-access-reviews"></a>To perform your access reviews

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Open your web browser and go to https://myapps.microsoft.com, or use the link provided by your organization. For example, you might be directed to a customized page for your organization, such as https://myapps.microsoft.com/contoso.com.

    The **Apps** page appears, showing all the cloud-based apps owned by your organization and available for you to use.

    ![Apps page in the My Apps portal](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Select the **Access reviews** tile to see a list of access reviews waiting for your approval.

    ![Access reviews page with pending access reviews for the organization](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Select **Begin review** to start your access review.

5. Review your access and determine whether it's still necessary.

    ![Access review page, showing the review details](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >If you're an administrator, and allowed to review your organization's access to groups and apps, you'll see a different page. For more information about reviewing groups or apps for your organization, see [Review access to groups or applications in Azure AD Access Reviews](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Select **Yes** to keep your access or **No** to remove your access.

    If you select **Yes**, you might need to specify a justification in the **Reason** box.

    ![Access review page, showing the Reason box with sample text](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Selezionare **Submit** (Invia).

    Your access review is complete and you return to the **My Apps** portal.

    >[!Note]
    >You can change your access at any time until your access review period ends. If you remove your access to an app or group, it's not removed immediately. The removal happens when the access review period ends or when an administrator closes the review.

## <a name="next-steps"></a>Passaggi successivi

- [Access and use apps on the My Apps portal](my-apps-portal-end-user-access.md).

- [Change your profile information](my-apps-portal-end-user-update-profile.md).

- [View and update your groups-related information](my-apps-portal-end-user-groups.md).
