---
title: Confronto basato sulle funzionalità dei livelli di Gestione API di Azure | Microsoft Docs
description: Questo articolo mette a confronto i livelli di Gestione API basati sulle funzionalità offerte.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: c06e297d3d81623b7224082cb66f8faa6879205d
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774958"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Confronto basato sulle funzionalità dei livelli di Gestione API di Azure

>[!IMPORTANT]
> Si noti che il livello Developer è per i casi d'uso e le valutazioni non di produzione. Non offre contratti di contratto. 

Ogni [piano tariffario](https://aka.ms/apimpricing) di Gestione API offre un set diverso di funzionalità e di[ capacità](api-management-capacity.md) per unità. La tabella seguente riepiloga le funzionalità chiave disponibili in ciascuno dei livelli. Alcune funzionalità potrebbero funzionare diversamente o avere capacità diverse a seconda del livello. In questi casi le differenze vengono indicate negli articoli della documentazione, i quali descrivono le singole funzionalità.

| Funzionalità                                                                                      | Consumo | Sviluppatore      | Basic          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integrazione di Azure AD<sup>1</sup>                                                             | No                            | Sì            | No             | Yes            | Sì            |
| Supporto della Rete virtuale di Microsoft Azure (VNet)                                                               | No                            | Sì            | No             | No             | Yes            |
| Distribuzione in più aree                                                                      | No                            | No             | No             | No             | Yes            |
| Più nomi di dominio personalizzati                                                                 | No                            | No             | No             | No             | Sì            |
| Portale per sviluppatori<sup>2</sup>                                                                 | No                            | Yes            | Sì            | Sì            | Sì            |
| Cache incorporata                                                                               | No                            | Yes            | Sì            | Sì            | Yes            |
| Analisi incorporata                                                                           | No                            | Yes            | Sì            | Sì            | Sì            |
| [Impostazioni SSL](api-management-howto-manage-protocols-ciphers.md)                             | Yes                            | Sì            | Sì            | Sì            | Sì            |
| [Cache esterna](https://aka.ms/apimbyoc)                                                    | Yes                           | Sì            | Sì            | Sì            | Sì            |
| [Autenticazione con certificato client](api-management-howto-mutual-certificates-for-clients.md) | Yes                | Sì            | Sì            | Sì            | Yes            |
| [Backup e ripristino](api-management-howto-disaster-recovery-backup-restore.md)               | No                            | Yes            | Sì            | Sì            | Sì            |
| [Gestione tramite Git](api-management-configuration-repository-git.md)                        | No                            | Yes            | Sì            | Sì            | Yes            |
| API di gestione diretta                                                                        | No                            | Yes            | Sì            | Sì            | Sì            |
| Metriche e log di Monitoraggio di Azure                                                               | No                | Yes            | Sì            | Sì            | Sì            |
| IP statico                                                               | No                | Yes            | Sì            | Sì            | Yes            |

<sup>1</sup> Abilita l'uso di Azure ad (e Azure ad B2C) come provider di identità per l'accesso utente nel portale per sviluppatori.<br/>
<sup>2</sup> Sono incluse le funzionalità correlate, ad esempio utenti, gruppi, problemi, modelli di posta elettronica e applicazioni, nonché notifiche.<br/>
