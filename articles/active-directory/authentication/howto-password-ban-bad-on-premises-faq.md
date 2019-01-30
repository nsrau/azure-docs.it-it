---
title: Domande frequenti sulla protezione password di Azure AD locale
description: Domande frequenti sulla protezione password di Azure AD locale
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 9046090a45d52a283d28d1c4cc79b44503cb5a9d
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427594"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Anteprima: protezione password di Azure AD locale - Domande frequenti

|     |
| --- |
| La protezione password di Azure AD è una funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="general-questions"></a>Domande generali

**D: quando sarà disponibile a livello generale la protezione password di Azure AD?**

La data di disponibilità generale non è ancora stata annunciata.

**D: la protezione password di Azure AD locale è supportata nei cloud non pubblici?**

No, la protezione password di AD Azure locale è supportata solo nel cloud pubblico.

**D: come è possibile applicare i vantaggi della protezione password di Azure AD a un sottoinsieme di utenti in locale?**

Non supportati. Dopo la distribuzione e l'abilitazione, la protezione password di Azure AD non discrimina e tutti gli utenti ottengono uguali vantaggi per la sicurezza.

**D: è supportata l'installazione della protezione password di Azure AD in affiancamento ad altri prodotti basati su filtri password?**

Sì. Il supporto di più DLL di filtro password registrate è una funzionalità di base di Windows e non è specifico della protezione password di Azure AD. Tutte le DLL di filtro password registrate devono concordare, prima che venga accettata una password.

**D: perché è necessario il servizio DFSR per la replica di sysvol?**

Il servizio Replica file (la tecnologia precedente a DFSR) include molti problemi noti e non è completamente supportato nelle versioni più recenti di Windows Server Active Directory. La protezione password di Azure AD non verrà testata nei domini configurati con il servizio Replica file.

Per altre informazioni, vedere gli articoli seguenti:

[The Case for Migrating sysvol replication to DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr) (Caso della migrazione della replica di sysvol a DFSR)

[The End is Nigh for FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs) (La fine è vicina per il servizio Replica file)

**D: perché è necessario un riavvio per installare o aggiornare il software dell'agente del controller di dominio?**

Questo requisito dipende dal comportamento di base di Windows.

**D: è disponibile un modo per configurare un agente del controller di dominio per l'uso di un server proxy specifico?**

 No.

## <a name="next-steps"></a>Passaggi successivi

In caso di domande relative alla protezione password di Azure AD locale senza risposte in questo articolo, inviarle tramite i commenti di seguito.

[Distribuire la protezione delle password di Azure AD](howto-password-ban-bad-on-premises-deploy.md)
