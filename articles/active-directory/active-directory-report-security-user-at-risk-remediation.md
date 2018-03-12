---
title: Report di sicurezza Utenti contrassegnati per il rischio nel portale di Azure Active Directory | Microsoft Docs
description: Informazioni sul report di sicurezza Utenti contrassegnati per il rischio nel portale di Azure Active Directory
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ed6201e9edcef39b14b948b6b2f6e0b5da01ec60
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Correggere gli utenti contrassegnati per il rischio nel portale di Azure Active Directory

I report di sicurezza in Azure Active Directory (Azure AD) permettono di ottenere informazioni approfondite sulla probabile presenza di account utente compromessi nell'ambiente. Un [utente contrassegnato per il rischio](active-directory-identityprotection.md#users-flagged-for-risk) è indicativo di un account utente che potrebbe essere stato compromesso.

Microsoft si impegna a contribuire alla protezione degli ambienti degli utenti. Nell'ambito di tale impegno, Microsoft effettua un monitoraggio costante per rilevare eventuali attività insolite o coerenti con modelli di attacco noti. 


Se vengono rilevate attività insolite che possono indicare l'accesso non autorizzato ad alcuni account degli utenti, si riceverà una notifica per poter intervenire. La ricezione di notifiche non indica che i sistemi di Microsoft siano stati compromessi.
 

## <a name="azure-active-directory-report-access"></a>Accesso ai report di Azure Active Directory

È possibile esaminare gli utenti contrassegnati per il rischio tramite un report online di Azure Active Directory. Se non si ha una sottoscrizione di Azure, è possibile eseguire gratuitamente la procedura di sottoscrizione all'indirizzo [http://aka.ms/AccessAAD](http://aka.ms/AccessAAD).  
Al termine dell'operazione, è possibile usare le credenziali di Office 365 per accedere all'interfaccia di amministrazione di Azure. Si noti che con la sottoscrizione Basic, il livello di dettaglio è limitato. La sottoscrizione Premium di Azure mette a disposizione un maggior livello di dati e analisi. Per altre informazioni, vedere [Report di sicurezza Utenti contrassegnati per il rischio nel portale di Azure Active Directory](active-directory-reporting-security-user-at-risk.md).

Dopo aver attivato l'accesso ad Azure AD, si viene reindirizzati al [portale di Azure AD](https://portal.azure.com). Per visualizzare direttamente il report, passare all'URL [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk).

**Per accedere ai report Utenti contrassegnati per il rischio nel Centro di amministrazione di Office 365:**

1.  Nel riquadro di spostamento a sinistra fare clic su **Interfacce di amministrazione**. 
2.  Fare clic su **Azure AD**.
3.  Accedere all'**interfaccia di amministrazione di Azure Active Directory**.
4.  Se nella parte superiore della pagina viene visualizzato il banner **Passare al nuovo portale**, fare clic sul collegamento.
4.  Nel menu di spostamento sinistro fare clic su **Azure Active Directory**. 
5.  Nel riquadro di spostamento fare clic su **Utenti contrassegnati per il rischio** in **Sicurezza**.

Esaminare le informazioni visualizzate qui. È consigliabile reimpostare la password per gli account elencati qui. 

## <a name="remediation-actions"></a>Azioni correttive

Eseguire queste operazioni per correggere gli account interessati e proteggere l'ambiente:

1.  [Convalidare](http://aka.ms/MFAValid) le informazioni corrette per la reimpostazione della password self-service e l'autenticazione a più fattori. 
2.  [Abilitare](http://aka.ms/MFAuth) l'autenticazione a più fattori per tutti gli utenti. 
3.  Usando questo [script di correzione](http://aka.ms/remediate) per ogni account interessato, è possibile eseguire automaticamente i passaggi seguenti: 

    a. Reimpostare la password per proteggere l'account e terminare le sessioni attive.

    b. Rimuovere i delegati delle cassette postali.

    c. Disabilitare le regole di inoltro della posta a domini esterni.

    d. Rimuovere la proprietà di inoltro globale della posta nella cassetta postale.

    e. Abilitare l'autenticazione a più fattori per l'account utente.

    f. Impostare una complessità della password elevata per l'account.

    g. Abilitare il controllo delle cassette postali.

    h. Generare un log di controllo da sottoporre all'esame dell'amministratore.

4. Analizzare il tenant di Office 365 e altre infrastrutture IT, esaminando anche tutte le impostazioni del tenant, gli account utente e le impostazioni di configurazione per utente per individuare eventuali modifiche. Cercare eventuali indicatori di metodi di persistenza e di intrusi che potrebbero aver sfruttato un appiglio iniziale per ottenere le credenziali VPN o l'accesso ad altre risorse aziendali. 

5.  Nell'ambito dell'analisi, valutare se è opportuno o necessario informare le autorità, incluse le forze dell'ordine.

È anche opportuno:

- Leggere e implementare queste [indicazioni](http://aka.ms/fixaccount) sulla gestione delle attività anomale. 
- [Abilitare la pipeline di controllo](http://aka.ms/improvesecurity) per agevolare l'analisi dell'attività nella tenancy. Al termine dell'operazione, l'archivio di controllo avvia la compilazione di tutti i log attività. A questo punto è anche possibile sfruttare le funzionalità di [ricerca e analisi del centro sicurezza e conformità](http://aka.ms/sccsearch). 
- Usare questo [script](http://aka.ms/mailboxaudit1) per abilitare il controllo delle cassette postali per tutti gli account. 
- Esaminare le autorizzazioni delegate e le regole di inoltro della posta per tutte le cassette postali. Per eseguire questa attività è possibile usare questo [script di PowerShell](http://aka.ms/delegateforwardrules). 



## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni in merito, vedere [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

