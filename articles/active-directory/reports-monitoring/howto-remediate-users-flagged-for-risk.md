---
title: Utenti contrassegnati per il rischio nel portale di Azure Active Directory | Microsoft Docs
description: Informazioni sul report di sicurezza Utenti contrassegnati per il rischio nel portale di Azure Active Directory
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1412cacd453de01f1937fbcf5e712afdac88def
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989709"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Correggere gli utenti contrassegnati per il rischio nel portale di Azure Active Directory

I report di sicurezza in Azure Active Directory (Azure AD) permettono di misurare la probabile presenza di account utente compromessi nell'ambiente. Un utente contrassegnato per il rischio è indicativo di un account utente che potrebbe essere stato compromesso.

Microsoft si impegna a contribuire alla protezione degli ambienti degli utenti. Nell'ambito di tale impegno, Microsoft effettua un monitoraggio costante per rilevare eventuali attività insolite o coerenti con modelli di attacco noti. 

Se vengono rilevate attività insolite che possono indicare l'accesso non autorizzato ad alcuni account degli utenti, si riceverà una notifica per poter intervenire. Ciò non significa che i sistemi Microsoft sono stati compromessi.

## <a name="access-the-users-flagged-for-risk-report"></a>Accedere al report Utenti contrassegnati per il rischio

È possibile esaminare gli utenti contrassegnati per il rischio tramite il [report degli utenti a rischio](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) nel portale di Azure. Se non si dispone di Azure AD, è possibile registrarsi gratuitamente all'indirizzo[https://aka.ms/AccessAAD](https://aka.ms/AccessAAD). 

Nel report Utenti contrassegnati per il rischio è possibile eseguire le azioni seguenti per ogni utente:

- Genera password provvisoria
- Richiedere all'utente la reimpostazione sicura della password al successivo accesso.
- Eliminare il rischio utente senza eseguire alcuna azione correttiva.

Per altre informazioni, vedere il [report sulla sicurezza Utenti contrassegnati per il rischio](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Sottoscrizione di Azure AD per i clienti di Office 365

È anche possibile usare le credenziali di Office 365 per accedere all'**interfaccia di amministrazione di Azure**. Dopo aver attivato l'accesso ad Azure AD, si viene reindirizzati al portale di Azure AD. Con la sottoscrizione di livello Basic, la quantità di dettagli forniti nei report è limitata. La sottoscrizione Premium di Azure mette a disposizione un maggior livello di dati e analisi.

Per accedere ai report sugli **utenti contrassegnati per il rischio** nell'interfaccia di amministrazione di Microsoft 365:

1.  Nel riquadro di spostamento a sinistra selezionare **Interfacce di amministrazione**. 
2.  Selezionare **Azure AD**.
3.  Accedere all'**interfaccia di amministrazione di Azure Active Directory**.
4.  Se nella parte superiore della pagina viene visualizzato il banner **Passare al nuovo portale**, selezionare il collegamento.
4.  Nel menu di spostamento sinistro selezionare **Azure Active Directory**. 
5.  Nel riquadro di spostamento selezionare **Utenti contrassegnati per il rischio** nella sezione **Sicurezza**.

## <a name="remediation-actions"></a>Azioni correttive

Eseguire queste operazioni per correggere gli account interessati e proteggere l'ambiente:

1.  [Convalidare le informazioni corrette](https://aka.ms/MFAValid) per la reimpostazione della password self-service e l'autenticazione a più fattori. 
2.  [Abilitare l'autenticazione a più fattori](https://aka.ms/MFAuth) per tutti gli utenti. 
3.  Usare questo [script di correzione](https://aka.ms/remediate) per ogni account interessato per eseguire automaticamente i passaggi seguenti: 

    a. Reimpostare la password per proteggere l'account e terminare le sessioni attive.

    b. Rimuovere i delegati delle cassette postali.

    c. Disabilitare le regole di inoltro della posta a domini esterni.

    d. Rimuovere la proprietà di inoltro globale della posta nella cassetta postale.

    e. Abilitare l'autenticazione a più fattori per l'account utente.

    f. Impostare una complessità della password elevata per l'account.

    g. Abilitare il controllo delle cassette postali.

    h. Generare un log di controllo da sottoporre all'esame dell'amministratore.

4. Analizzare il tenant di Office 365 e altre infrastrutture IT, esaminando anche tutte le impostazioni del tenant, gli account utente e le impostazioni di configurazione per utente per individuare eventuali modifiche. Cercare eventuali indicatori di metodi di persistenza e di intrusi che potrebbero aver sfruttato un appiglio iniziale per ottenere le credenziali VPN o l'accesso ad altre risorse aziendali. 

5.  Nell'ambito dell'analisi valutare se è opportuno informare le autorità, incluse le forze dell'ordine.

È anche opportuno:

- Leggere e implementare queste [indicazioni sulla gestione delle attività anomale](https://aka.ms/fixaccount). 
- [Abilitare la pipeline di controllo](https://aka.ms/improvesecurity) per agevolare l'analisi dell'attività nel tenant. Al termine dell'operazione, l'archivio di controllo avvia la compilazione dei log attività. A questo punto è anche possibile sfruttare [le risorse di ricerca e analisi del centro sicurezza e conformità](https://aka.ms/sccsearch). 
- Usare questo [script per abilitare il controllo delle cassette postali](https://aka.ms/mailboxaudit1) per tutti gli account. 
- Esaminare le autorizzazioni delegate e le regole di inoltro della posta per tutte le cassette postali. Per eseguire questa attività è possibile usare questo [script di PowerShell](https://aka.ms/delegateforwardrules). 

## <a name="next-steps"></a>Passaggi successivi

* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
* [Utenti contrassegnati per il rischio](concept-user-at-risk.md)
