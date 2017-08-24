---
title: Creazione di report in Azure Active Directory | Microsoft Docs
description: Offre una panoramica generale della creazione di report in Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 738c8f4a56586b87f03973ec258b0a3023affa60
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="azure-active-directory-reporting"></a>Creazione di report in Azure Active Directory

La creazione di report in Azure Active Directory permette di ottenere informazioni approfondite sullo stato dell'ambiente.  
I dati forniti consentono di:

- Determinare l'utilizzo di app e servizi da parte degli utenti
- Rilevare rischi potenziali che pregiudicano l'integrità dell'ambiente
- Risolvere problemi che influiscono negativamente sulla produttività degli utenti  

L'architettura dei report si basa su due elementi fondamentali:

- Report sulla sicurezza
- Report sull’attività

![Creazione di report](./media/active-directory-reporting-azure-portal/01.png)



## <a name="security-reports"></a>Report sulla sicurezza

I report sulla sicurezza in Azure Active Directory consentono di proteggere le identità dell'organizzazione.  
Azure Active Directory prevede due tipi di report sulla sicurezza:

- **Utenti contrassegnati per il rischio**: il [report sulla sicurezza relativo agli utenti contrassegni per il rischio](active-directory-reporting-security-user-at-risk.md) offre una panoramica degli account utente che potrebbero essere stati compromessi.

- **Accessi a rischio**: il [report sulla sicurezza relativo agli accessi a rischio](active-directory-reporting-security-risky-sign-ins.md) offre una indicazione sui tentativi di accesso che potrebbero essere stati eseguiti da qualcuno che non è il legittimo proprietario di un account utente. 

**Quale licenza di Azure AD è necessaria per accedere a un report sulla sicurezza?**  
Tutte le edizioni di Azure Active Directory offrono report sugli utenti contrassegnati per il rischio e sugli accessi a rischio.  
Tuttavia, il livello di granularità dei report varia a seconda delle edizioni: 

- Nelle edizioni **Azure Active Directory Free e Basic**  è già incluso un elenco degli utenti contrassegnati per il rischio e degli accessi a rischio. 

- Nell'edizione **Azure Active Directory Premium 1** questo modello consente anche di esaminare alcuni degli eventi di rischio sottostanti che sono stati rilevati per ogni report. 

- L'edizione **Azure Active Directory Premium 2** offre informazioni più dettagliate sugli eventi di rischio sottostanti e permette anche di configurare criteri di sicurezza che rispondono automaticamente a livelli di rischio configurati.


## <a name="activity-reports"></a>Report sull’attività

Azure Active Directory prevede due tipi di report sull'attività:

- **Log di controllo**: il [report sull'attività relativo ai log di controllo](active-directory-reporting-activity-audit-logs.md) consente di accedere alla cronologia di ogni attività eseguita nel tenant.

- **Accessi**: il [report sull'attività relativo agli accessi](active-directory-reporting-activity-sign-ins.md) permette di determinare chi ha eseguito le attività segnalate nel report dei log di controllo.



Il **report dei log di controllo** include i record delle attività di sistema per la conformità.
I dati forniti consentono, tra l'altro, di risolvere scenari comuni. Ad esempio:

- Un utente del tenant ha avuto accesso a un gruppo amministrativo e si vuole sapere chi ha concesso tale accesso. 

- Si vuole ottenere un elenco degli utenti che accedono a un'app specifica caricata di recente, per sapere se viene usata.

- Si vuole conoscere il numero di reimpostazioni della password eseguite nel tenant.


**Quale licenza di Azure AD è necessaria per accedere al report dei log di controllo?**  
Il report dei log di controllo è disponibile per le funzionalità per le quali si possiede una licenza. Se è disponibile una licenza per una funzionalità specifica, si ha anche accesso alle relative informazioni del log di controllo.

Per altre informazioni, vedere il **Confronto tra le funzionalità generalmente disponibili nelle edizioni Free, Basic e Premium**  in [Caratteristiche e funzionalità di Azure Active Directory ](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   



Il **report sull'attività relativo agli accessi** permette di rispondere a domande simili alle seguenti:

- Qual è il modello di accesso di un utente?
- Quanti utenti hanno effettuato l'accesso nell'arco di una settimana?
- Qual è lo stato di questi accessi?


**Quale licenza di Azure AD è necessaria per accedere al report sull'attività relativo agli accessi?**  
Per visualizzare il report sull'attività relativo agli accessi, è necessario che al tenant sia associata una licenza di Azure AD Premium


## <a name="programmatic-access"></a>Accesso a livello di codice

Oltre all'interfaccia utente, la creazione di report di Azure Active Directory offre anche l'[accesso a livello di codice](active-directory-reporting-api-getting-started-azure-portal.md) ai dati dei report. I dati di questi report possono essere molto utili per le applicazioni, ad esempio i sistemi SIEM e gli strumenti di controllo e business intelligence. L'API di creazione report di Azure AD fornisce l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui vari tipi di report in Azure Active Directory, vedere:

- [Report sugli utenti contrassegnati per il rischio](active-directory-reporting-security-user-at-risk.md)
- [Report sugli accessi a rischio](active-directory-reporting-security-risky-sign-ins.md)
- [Report dei log di controllo](active-directory-reporting-activity-audit-logs.md)
- [Report dei log di accesso](active-directory-reporting-activity-sign-ins.md)

Per altre informazioni sull'accesso ai dati dei report tramite l'API di creazione report, vedere: 

- [Introduzione all'API di creazione report di Azure Active Directory](active-directory-reporting-api-getting-started-azure-portal.md)


<!--Image references-->
[1]: ./media/active-directory-reporting-azure-portal/ic195031.png
