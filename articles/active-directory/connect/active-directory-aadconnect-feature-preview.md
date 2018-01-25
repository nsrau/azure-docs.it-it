---
title: "Funzionalità in anteprima di Azure AD Connect | Documentazione Microsoft"
description: "Questo argomento descrive in maggiore dettaglio le funzionalità in anteprima di Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 7173c87dec980130992438954650227c16ad7292
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="more-details-about-features-in-preview"></a>Altre informazioni sulle funzionalità in anteprima
Questo argomento descrive come usare le funzionalità attualmente in anteprima.

## <a name="group-writeback"></a>Writeback dei gruppi
L'opzione per il writeback dei gruppi nelle funzionalità facoltative consente il writeback dei **gruppi di Office 365** in una foresta in cui è installato Exchange. Si tratta di un gruppo che viene sempre gestito nel cloud. Se Exchange è disponibile in locale, è possibile eseguire il writeback di questi gruppi in locale in modo che gli utenti con una cassetta postale di Exchange locale possano inviare e ricevere messaggi di posta elettronica da questi gruppi.

Altre informazioni sui gruppi di Office 365 e su come usarli sono disponibili [qui](http://aka.ms/O365g).

Un gruppo di Office 365 viene rappresentato come gruppo di distribuzione in AD DS locale. Per riconoscere questo nuovo tipo di gruppo, nel server di Exchange locale deve essere installato l'aggiornamento cumulativo 8 di Exchange 2013 (rilasciato a marzo 2015) o Exchange 2016.

**Note durante l'anteprima**

* Attualmente l'attributo della rubrica non è popolato nell'anteprima. Senza questo attributo, il gruppo non è visibile nell'elenco indirizzi globale. Il modo più semplice per popolare questo attributo consiste nell'usare il cmdlet di PowerShell per Exchange `update-recipient`.
* Solo le foreste con lo schema di Exchange sono destinazioni valide per i gruppi. Se non è stata rilevata alcuna versione di Exchange, il writeback dei gruppi non può essere abilitato.
* Attualmente sono supportate solo le distribuzioni in organizzazioni di Exchange a foresta singola. Se sono presenti più organizzazioni di Exchange locali, è necessaria una soluzione GALSync locale per visualizzare questi gruppi nelle altre foreste.
* La funzionalità di writeback dei gruppi non gestisce gruppi di protezione o gruppi di distribuzione.

> [!NOTE]
> Per il writeback dei gruppi è necessaria una sottoscrizione di Azure AD Premium.
> 
>

## <a name="user-writeback"></a>Writeback degli utenti
> [!IMPORTANT]
> La funzionalità di anteprima di writeback degli utenti è stata rimossa nell'aggiornamento di agosto 2015 di Azure AD Connect. Se questa funzionalità è stata abilitata, è necessario disabilitarla.
>
>

## <a name="next-steps"></a>Passaggi successivi
Continuare l'[Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
