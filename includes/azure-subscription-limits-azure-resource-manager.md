---
title: File di inclusione
description: File di inclusione
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 10/19/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: a1645a8471f75f1d56f3e61c0adfc3fadee14560
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553614"
---
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| VM per [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> per ogni area. |25.000 per ogni area. |
| Numero totale di core della VM per ogni [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per ogni area. | Contattare il supporto tecnico. |
| Macchina virtuale per ogni serie, ad esempio Dv2 e F, core per ogni [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per ogni area. | Contattare il supporto tecnico. |
| [Ai coamministratori](../articles/billing-add-change-azure-subscription-administrator.md) per ogni sottoscrizione |Senza limiti. |Senza limiti. |
| [Account di archiviazione](../articles/storage/common/storage-quickstart-create-account.md) per area per sottoscrizione |200 |200<sup>2</sup> |
| [Gruppi di risorse](../articles/azure-resource-manager/resource-group-overview.md) per ogni sottoscrizione |980 |980 |
| [Set di disponibilità](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per ogni sottoscrizione |2.000 per area. |2.000 per area. |
| Dimensioni delle richieste API di gestione risorse di Azure |4,194,304 byte. |4,194,304 byte. |
| Tag per sottoscrizione<sup>3</sup> |Senza limiti. |Senza limiti. |
| Calcoli di tag univoco per sottoscrizione<sup>3</sup> | 10,000 | 10,000 |
| [Servizi cloud](../articles/cloud-services/cloud-services-choose-me.md) per sottoscrizione |N/A<sup>4</sup> |N/A<sup>4</sup> |
| [Gruppi di affinità](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) per sottoscrizione |N/A<sup>4</sup> |N/A<sup>4</sup> |
| [Le distribuzioni a livello di sottoscrizione](../articles/azure-resource-manager/deploy-to-subscription.md) per ogni percorso | 800 | 800 |

<sup>1</sup>i limiti predefiniti variano dal tipo di categoria di offerta, ad esempio pagamento a consumo e versione di valutazione gratuita e dalla serie, ad esempio Dv2, F e G.

<sup>2</sup>sono inclusi gli account di archiviazione sia Standard e Premium. Se è necessario più di 200 account di archiviazione, inviare una richiesta tramite [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il team di archiviazione di Azure esamina il caso aziendale e può approvare fino a 250 account di archiviazione.

<sup>3</sup>È possibile applicare un numero illimitato di tag per sottoscrizione. Il numero di tag per risorsa o per gruppo di risorse è limitato a 15. Resource Manager restituisce un [elenco di valori e il nome di tag univoco](/rest/api/resources/tags) nella sottoscrizione sola quando il numero di tag è 10.000 o meno. È comunque possibile trovare una risorsa dal tag quando il numero è superiore a 10.000.  

<sup>4</sup>queste funzionalità non sono più necessarie con i gruppi di risorse di Azure e Resource Manager.

> [!NOTE]
> Memorie centrali delle macchine virtuali hanno un limite totale a livello di area. Dispongono anche di un limite per la serie per ogni dimensione a livello di area, ad esempio Dv2 e F. Questi limiti vengono applicati separatamente. Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30. Questa sottoscrizione è possibile distribuire 30 VM A1 o 30 VM D1 o una combinazione delle due che non superi un totale di 30 Core. Un esempio di una combinazione è 10 VM A1 e 20 VM D1.  
> <!-- -->
> 
> 

