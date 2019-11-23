---
title: Update and manage a dynamic group rule and troubleshoot membership - Azure Active Directory | Microsoft Docs
description: How to create a group membership rule in the Azure portal, check status.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c387e2d78adcaebc430073a2a45818c4a0928b9f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422363"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Update a dynamic group to manage membership in Azure Active Directory

In Azure Active Directory (Azure AD), you can use rules to determine group membership based on user or device properties. This article tells how to set up a rule for a dynamic group in the Azure portal.
Dynamic membership is supported for security groups or Office 365 groups. When a group membership rule is applied, user and device attributes are evaluated for matches with the membership rule. When an attribute changes for a user or device, all dynamic group rules in the organization are processed for membership changes. Users and devices are added or removed if they meet the conditions for a group.

## <a name="rule-builder-in-the-azure-portal"></a>Rule builder in the Azure portal

Azure AD provides a rule builder to create and update your important rules more quickly. The rule builder supports the construction up to five expressions. The rule builder makes it easier to form a rule with a few simple expressions, however, it can't be used to reproduce every rule. If the rule builder doesn't support the rule you want to create, you can use the text box.

Here are some examples of advanced rules or syntax for which we recommend that you construct using the text box:

- Rule with more than five expressions
- The Direct reports rule
- Setting [operator precedence](groups-dynamic-membership.md#operator-precedence)
- [Rules with complex expressions](groups-dynamic-membership.md#rules-with-complex-expressions); for example `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> The rule builder might not be able to display some rules constructed in the text box. You might see a message when the rule builder is not able to display the rule. The rule builder doesn't change the supported syntax, validation, or processing of dynamic group rules in any way.

![Add membership rule for a dynamic group](./media/groups-update-rule/update-dynamic-group-rule.png)

For examples of syntax, supported properties, operators, and values for a membership rule, see [Dynamic membership rules for groups in Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>To update a group membership rule

1. Sign in to the [Azure AD admin center](https://aad.portal.azure.com) with an account that is in the Global administrator, Group administrator, Intune administrator, or User administrator role in the tenant.
1. Select **Groups** > **All groups**.
1. Select a group to open its profile.
1. On the profile page for the group, select **Dynamic membership rules**. The rule builder supports up to five expressions. To add more than five expressions, you must use the text box.

   ![Add membership rule for a dynamic group](./media/groups-update-rule/update-dynamic-group-rule.png)

1. To see the custom extension properties available for your membership rule:
   1. Select **Get custom extension properties**
   1. Enter the application ID, and then select **Refresh properties**.
1. After updating the rule, select **Save**.

If the rule you entered isn't valid, an explanation of why the rule couldn't be processed is displayed in an Azure notification in the portal. Read it carefully to understand how to fix the rule.

## <a name="check-processing-status-for-a-rule"></a>Check processing status for a rule

È possibile visualizzare lo stato di elaborazione dell'appartenenza e la data dell'ultimo aggiornamento nella pagina **Panoramica** per il gruppo.
  
  ![display of dynamic group status](./media/groups-create-rule/group-status.png)

I seguenti messaggi di stato possono essere visualizzati per lo stato di **Elaborazione appartenenza**:

- **Valutazione**: la modifica dei gruppi è stata ricevuta e gli aggiornamenti sono in fase di valutazione.
- **Elaborazione**: gli aggiornamenti sono in fase di elaborazione.
- **Aggiornamento completato**: l’elaborazione è stata completata e tutti gli aggiornamenti sono stati apportati.
- **Processing error**:  Processing couldn't be completed because of an error evaluating the membership rule.
- **Aggiornamento sospeso**: regola di appartenenza dinamica, gli aggiornamenti sono stati sospesi dall'amministratore. MembershipRuleProcessingState è impostato su "Paused".

I seguenti messaggi di stato possono essere visualizzati per lo stato dell'**Ultimo aggiornamento dell'appartenenza**:

- **Date and time**: The last time the membership was updated.
- **In corso**: aggiornamenti in corso.
- **Unknown**: The last update time can't be retrieved. The group might be new.

Se si verifica un errore durante l'elaborazione della regola di appartenenza per un gruppo specifico, un avviso viene visualizzato nella parte superiore della **Pagina di panoramica** per il gruppo. Se non è possibile elaborare aggiornamenti in sospeso per l'appartenenza dinamica per tutti i gruppi all'interno del tenant per oltre 24 ore, viene visualizzato un avviso nella parte superiore di **Tutti i gruppi** .

![processing error message alerts](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Passaggi successivi

These articles provide additional information on working with dynamic groups in Azure AD.

- For a complete reference to dynamic rule structure, see [Dynamic membership rule syntax](groups-dynamic-membership.md).
- [Create a static membership group and add members](../fundamentals/active-directory-groups-create-azure-portal.md).
