---
title: Domande frequenti sulla protezione password di Azure AD locale
description: Domande frequenti sulla protezione password di Azure AD locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 62bda3a1c9cb9d53578c2d471b9e63d1f0873234
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663299"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Anteprima: protezione password di Azure AD locale - Domande frequenti

|     |
| --- |
| La protezione password di Azure AD è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="general-questions"></a>Domande generali

**D: quando sarà disponibile a livello generale la protezione password di Azure AD?**

La versione disponibile a livello generale è prevista per il primo trimestre del 2019 (prima della fine del mese di marzo 2019). Grazie a tutti coloro che ad oggi hanno fornito commenti e suggerimenti sulla funzionalità - lo apprezziamo molto!

**D: la protezione password di Azure AD locale è supportata nei cloud non pubblici?**

No, la protezione password di Azure AD locale è supportata solo nel cloud pubblico.

**D: in che modo è possibile applicare i vantaggi della protezione password di Azure AD a un sottoinsieme di utenti in locale?**

Non supportati. Dopo la distribuzione e l'abilitazione, la protezione password di Azure AD non prevede alcuna discriminazione: tutti gli utenti ottengono uguali vantaggi per la sicurezza.

**D: l'installazione della protezione password di Azure AD è supportata in affiancamento ad altri prodotti basati su filtri password?**

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
