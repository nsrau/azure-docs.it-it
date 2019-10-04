---
title: File di inclusione
description: File di inclusione
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: f85605610727ef2c1e1987b7ef93a41ce2417a25
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "69626351"
---
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| VM per [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> per area. |25.000 per area. |
| Numero totale di core della VM per ogni [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per area. | Contattare il supporto tecnico. |
| VM per serie, ad esempio dv2 e F, Core per [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per area. | Contattare il supporto tecnico. |
| [Coamministratori](../articles/billing-add-change-azure-subscription-administrator.md) per sottoscrizione |Senza limiti. |Senza limiti. |
| [Account di archiviazione](../articles/storage/common/storage-quickstart-create-account.md) per area per sottoscrizione |250 |250 |
| [Gruppi di risorse](../articles/azure-resource-manager/resource-group-overview.md) per sottoscrizione |980 |980 |
| [Set di disponibilità](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per sottoscrizione |2\.000 per area. |2\.000 per area. |
| Dimensioni richieste API Azure Resource Manager |4\.194.304 byte. |4\.194.304 byte. |
| Tag per sottoscrizione<sup>2</sup> |Senza limiti. |Senza limiti. |
| Calcoli di tag univoci per sottoscrizione<sup>2</sup> | 10,000 | 10,000 |
| [Servizi cloud](../articles/cloud-services/cloud-services-choose-me.md) per sottoscrizione |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Gruppi di affinità](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) per sottoscrizione |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Distribuzioni a livello di sottoscrizione](../articles/azure-resource-manager/deploy-to-subscription.md) per località | 800<sup>4</sup> | 800 |

<sup>1</sup> I limiti predefiniti variano in base al tipo di categoria di offerta, ad esempio versione di valutazione gratuita e pagamento in base al consumo, e per serie, ad esempio dv2, F e G. Ad esempio, il valore predefinito per le sottoscrizioni Enterprise Agreement è 350.

<sup>2</sup> È possibile applicare un numero illimitato di tag per ogni sottoscrizione. Il numero di tag per risorsa o gruppo di risorse è limitato a 50. Gestione risorse restituisce un [elenco di nomi e valori di tag univoci](/rest/api/resources/tags) nella sottoscrizione solo se il numero di tag è 10.000 o meno. È ancora possibile trovare una risorsa in base al tag quando il numero supera 10.000.  

<sup>3</sup> Queste funzionalità non sono più necessarie con i gruppi di risorse di Azure e Gestione risorse.

<sup>4</sup> Se si raggiunge il limite di 800 distribuzioni, eliminare le distribuzioni dalla cronologia che non sono più necessarie. Per eliminare le distribuzioni a livello di sottoscrizione, usare [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) o [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> I core della macchina virtuale hanno un limite totale per l'area. Hanno anche un limite per le serie locali per ogni dimensione, ad esempio dv2 e F. Questi limiti vengono applicati separatamente. Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30. Questa sottoscrizione può distribuire 30 VM a1 o 30 VM D1 o una combinazione dei due che non superi un totale di 30 core. Un esempio di combinazione è 10 VM a1 e 20 VM D1.  
> <!-- -->
> 
> 

