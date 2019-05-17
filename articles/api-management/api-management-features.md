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
ms.openlocfilehash: d881c8de7ecc32be0ca0cc2c5a82e0d2d51a7054
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780327"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Confronto basato sulle funzionalità dei livelli di Gestione API di Azure

Ogni [piano tariffario](https://aka.ms/apimpricing) di Gestione API offre un set diverso di funzionalità e di[ capacità](api-management-capacity.md) per unità. La tabella seguente riepiloga le funzionalità chiave disponibili in ciascuno dei livelli. Alcune funzionalità potrebbero funzionare diversamente o avere capacità diverse a seconda del livello. In questi casi le differenze vengono indicate negli articoli della documentazione, i quali descrivono le singole funzionalità.

| Funzionalità                                                                                      | <sup>ANTEPRIMA</sup> di consumo | Developer      | Di base          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integrazione di Azure AD<sup>1</sup>                                                             | N.                            | Sì            | No              | Sì            | Sì            |
| Supporto della Rete virtuale di Microsoft Azure (VNet)                                                               | N.                            | Sì            | N.             | N.             | Sì            |
| Distribuzione in più aree                                                                      | N.                            | N.             | N.             | N.             | Sì            |
| Più nomi di dominio personalizzati                                                                 | N.                            | N.             | N.             | N.             | Sì            |
| Portale per sviluppatori<sup>2</sup>                                                                 | N.                            | Sì            | Sì            | Sì            | Sì            |
| Cache incorporata                                                                               | N.                            | Sì            | Sì            | Sì            | Sì            |
| Analisi incorporata                                                                           | N.                            | Sì            | Sì            | Sì            | Sì            |
| [Impostazioni SSL](api-management-howto-manage-protocols-ciphers.md)                             | N.                            | Sì            | Sì            | Sì            | Sì            |
| [Cache esterna](https://aka.ms/apimbyoc)                                                    | Sì                           | Sì            | Sì            | Sì            | Sì            |
| [Autenticazione con certificato client](api-management-howto-mutual-certificates-for-clients.md) | No<sup>3</sup>                | Sì            | Sì            | Sì            | Sì            |
| [Backup e ripristino](api-management-howto-disaster-recovery-backup-restore.md)               | N.                            | Sì            | Sì            | Sì            | Sì            |
| [Gestione tramite Git](api-management-configuration-repository-git.md)                        | N.                            | Sì            | Sì            | Sì            | Sì            |
| API di gestione diretta                                                                        | N.                            | Sì            | Sì            | Sì            | Sì            |
| Metriche e log di Monitoraggio di Azure                                                               | N.<sup>4</sup>                | Sì            | Sì            | Sì            | Sì            |

<sup>1</sup> consente l'uso di Azure Active Directory (e Azure AD B2C) come un'identità provider per l'utente accede da portale per sviluppatori.<br/>
<sup>2</sup> Sono incluse le funzionalità correlate, ad esempio utenti, gruppi, problemi, modelli di posta elettronica e applicazioni, nonché notifiche.<br/>
<sup>3</sup> l'autenticazione del certificato client verrà aggiunto al livello di utilizzo prima della relativa disponibilità generale.<br/>
<sup>4</sup> verrà aggiunto il supporto di monitoraggio di Azure completo per il tipo a consumo.
