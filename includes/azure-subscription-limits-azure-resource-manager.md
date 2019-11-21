---
title: file di inclusione
description: file di inclusione
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: a01455da36e8ab573773fec2a6da1f7903b8ea20
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224369"
---
| Gruppi | Limite predefinito | Limite massimo |
| --- | --- | --- |
| VM per [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |25,000<sup>1</sup> per region. |25,000 per region. |
| Numero totale di core della VM per ogni [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region. | Contattare il supporto tecnico. |
| VM per series, such as Dv2 and F, cores per [subscription](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region. | Contattare il supporto tecnico. |
| [Coadministrators](../articles/billing-add-change-azure-subscription-administrator.md) per subscription |Senza limiti. |Senza limiti. |
| [Account di archiviazione](../articles/storage/common/storage-quickstart-create-account.md) per area per sottoscrizione |250 |250 |
| [Resource groups](../articles/azure-resource-manager/resource-group-overview.md) per subscription |980 |980 |
| [Availability sets](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per subscription |2,000 per region. |2,000 per region. |
| Azure Resource Manager API request size |4,194,304 bytes. |4,194,304 bytes. |
| Tags per subscription<sup>2</sup> |Senza limiti. |Senza limiti. |
| Unique tag calculations per subscription<sup>2</sup> | 10,000 | 10,000 |
| [Servizi cloud](../articles/cloud-services/cloud-services-choose-me.md) per sottoscrizione |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Gruppi di affinità](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) per sottoscrizione |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Subscription-level deployments](../articles/azure-resource-manager/deploy-to-subscription.md) per location | 800<sup>4</sup> | 800 |

<sup>1</sup>Default limits vary by offer category type, such as Free Trial and Pay-As-You-Go, and by series, such as Dv2, F, and G. For example, the default for Enterprise Agreement subscriptions is 350.

<sup>2</sup>You can apply an unlimited number of tags per subscription. The number of tags per resource or resource group is limited to 50. Resource Manager returns a [list of unique tag name and values](/rest/api/resources/tags) in the subscription only when the number of tags is 10,000 or less. You still can find a resource by tag when the number exceeds 10,000.  

<sup>3</sup>These features are no longer required with Azure resource groups and Resource Manager.

<sup>4</sup>If you reach the limit of 800 deployments, delete deployments from the history that are no longer needed. To delete subscription level deployments, use [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) or [az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Virtual machine cores have a regional total limit. They also have a limit for regional per-size series, such as Dv2 and F. These limits are separately enforced. Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30. This subscription can deploy 30 A1 VMs, or 30 D1 VMs, or a combination of the two not to exceed a total of 30 cores. An example of a combination is 10 A1 VMs and 20 D1 VMs.  
> <!-- -->
> 
> 

