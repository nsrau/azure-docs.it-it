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
ms.date: 04/04/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: ee98aee0873796c2a06db73d3365e3ff9ef87ccf
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915884"
---
# <a name="asdk-release-notes"></a>Note sulla versione ASDK

Questo articolo fornisce informazioni sulle modifiche, le correzioni e i problemi noti in Azure Stack Development Kit (ASDK). Se non si conosce quale versione in esecuzione, è possibile [usare il portale per controllare](../azure-stack-updates.md#determine-the-current-version).

Rimanere aggiornati sulle novità di ASDK sottoscrivendo il [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-1903"></a>Build 1903

Il payload 1903 non include una versione ASDK.

## <a name="build-11902069"></a>Build 1.1902.0.69

### <a name="new-features"></a>Nuove funzionalità

- La compilazione 1902 introduce una nuova interfaccia utente nel portale di Azure Stack amministratore per la creazione di piani, offerte, quote e piani aggiuntivi. Per altre informazioni, incluse le schermate, vedere [creare i piani, offerte e quote](../azure-stack-create-plan.md).

- Per un elenco di altri miglioramenti e modifiche in questa versione, vedere [in questa sezione](../azure-stack-update-1902.md#improvements) in Azure Stack le note sulla versione.

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Problemi noti

- È stato identificato un problema in cui i pacchetti in 1450 byte per un servizio di bilanciamento di carico interno (ILB) vengono eliminati. Il problema è causato dall'impostazione MTU nell'host in corso di un valore troppo basso per supportare i pacchetti incapsulati VXLAN che passano attraverso il ruolo, che a partire da 1901 è stato spostato in host. Esistono almeno due scenari che possono verificarsi in cui è stato descritto il problema manifestarsi:

  - Query SQL per SQL Always On che è dietro un servizio di bilanciamento di carico interno (ILB) e sono più 660 byte.
  - Distribuzioni Kubernetes esito negativo se si tenta di abilitare più schemi.  

  Il problema si verifica quando si dispone di comunicazione tra una macchina virtuale e un bilanciamento del carico interno nella stessa rete virtuale, ma in subnet diverse. È possibile risolvere questo problema eseguendo i comandi seguenti in un prompt dei comandi con privilegi elevati nell'host ASDK:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

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
