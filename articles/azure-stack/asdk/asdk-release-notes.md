---
title: Note sulla versione di Microsoft Azure Stack Development Kit | Microsoft Docs
description: I miglioramenti, correzioni e problemi noti per Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 8268408137653e99863f7803300e6d843c1d9a47
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889308"
---
# <a name="asdk-release-notes"></a>Note sulla versione ASDK

Questo articolo fornisce informazioni sulle modifiche, le correzioni e i problemi noti in Azure Stack Development Kit (ASDK). Se non si conosce quale versione in esecuzione, è possibile [usare il portale per controllare](../azure-stack-updates.md#determine-the-current-version).

Rimanere aggiornati sulle novità di ASDK sottoscrivendo il [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11901095"></a>Build 1.1901.0.95

Vedere le [le informazioni di compilazione importante nelle note sulla versione di Azure Stack](../azure-stack-update-1901.md#build-reference).

### <a name="changes"></a>Modifiche

Questa build include i miglioramenti seguenti per Azure Stack:

- Componenti BGP e NAT a questo punto vengono distribuiti nell'host fisico. Questo elimina la necessità di avere due indirizzi IP pubblici o aziendali per la distribuzione di ASDK e semplifica inoltre la distribuzione.
- I backup possono ora i sistemi integrati di Azure Stack [convalidare](asdk-validate-backup.md) usando la **asdk installer.ps1** script di PowerShell.

### <a name="new-features"></a>Nuove funzionalità

- Per un elenco delle nuove funzionalità in questa versione, vedere [in questa sezione](../azure-stack-update-1901.md#new-features) dello Stack di Azure le note sulla versione.

### <a name="fixed-and-known-issues"></a>Fissi e problemi noti

- Per un elenco di problemi risolti in questa versione, vedere [in questa sezione](../azure-stack-update-1901.md#fixed-issues) dello Stack di Azure le note sulla versione. Per un elenco di problemi noti, vedere [in questa sezione](../azure-stack-update-1901.md#known-issues-post-installation).
- Si noti che [hotfix disponibili di Azure Stack](../azure-stack-update-1901.md#azure-stack-hotfixes) non sono applicabili a ASDK di Stack di Azure.

## <a name="build-118110101"></a>Build 1.1811.0.101

### <a name="changes"></a>Modifiche

Questa build include i miglioramenti seguenti per Azure Stack:  

- È presente un set di nuovo hardware minimo e consigliato e i requisiti software per il ASDK. Queste nuove specifiche sono documentate in [considerazioni sulla pianificazione della distribuzione di Azure Stack](asdk-deploy-considerations.md). Come si è evoluta la piattaforma di Azure Stack, altri servizi sono ora disponibili e potrebbero essere necessarie altre risorse. Le specifiche di un aumento riflettano questi consigli rivisti.

### <a name="new-features"></a>Nuove funzionalità

Per un elenco delle nuove funzionalità in questa versione, vedere [in questa sezione](../azure-stack-update-1811.md#new-features) dello Stack di Azure le note sulla versione.

### <a name="fixed-and-known-issues"></a>Fissi e problemi noti

Per un elenco di problemi risolti in questa versione, vedere [in questa sezione](../azure-stack-update-1811.md#fixed-issues) dello Stack di Azure le note sulla versione. Per un elenco di problemi noti, vedere [in questa sezione](../azure-stack-update-1811.md#known-issues-post-installation).

## <a name="build-11809090"></a>Build 1.1809.0.90

### <a name="new-features"></a>Nuove funzionalità

Per un elenco delle nuove funzionalità in questa versione, vedere [in questa sezione](../azure-stack-update-1809.md#new-features) dello Stack di Azure le note sulla versione.

### <a name="fixed-issues"></a>Problemi risolti

Per un elenco di problemi risolti in questa versione, vedere [in questa sezione](../azure-stack-update-1809.md#fixed-issues).

### <a name="known-issues"></a>Problemi noti

Per un elenco di problemi noti in questa versione, vedere [in questa sezione](../azure-stack-update-1809.md#known-issues-post-installation).