---
title: Report di sicurezza in Azure Active Directory Gratuito e Azure Active Directory Basic (anteprima) | Documentazione Microsoft
description: Elenca i vari report disponibili per l&quot;anteprima di Azure Active Directory
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/19/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: fa5a6dab1e76d62956cbfdd9b8b0f64c014696db
ms.openlocfilehash: c1a3953c79cfac9d6f55b38971ee2b79ff4244eb


---
# <a name="security-reporting-in-the-azure-active-directory-free-and-basic-edition---preview"></a>Report di sicurezza in Azure Active Directory Gratuito e Azure Active Directory Basic (anteprima)

I report di sicurezza nell'[anteprima](active-directory-preview-explainer.md) di Azure Active Directory permettono di ottenere informazioni approfondite sulla probabile presenza di account utente compromessi nell'ambiente. 

Azure Active Directory rileva le azioni sospette correlate agli account utente. Per ogni azione rilevati viene creato un record denominato *evento di rischio*. Per altre informazioni, vedere [Azure Active Directory risk events](active-directory-identity-protection-risk-events.md) (Eventi di rischio di Azure Active Directory). 

Gli eventi di rischio rilevati vengono usati per calcolare:

- **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. Per informazioni dettagliate, vedere [Accessi a rischio](active-directory-identityprotection.md#risky-sign-ins). 

- **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. Per informazioni dettagliate, vedere [Utenti contrassegnati per il rischio](active-directory-identityprotection.md#users-flagged-for-risk).  


## <a name="risky-sign-ins-report"></a>Report sugli accessi a rischio

Azure Active Directory Gratuito e Basic includono un elenco di accessi a rischio rilevati e segnalati per gli utenti. Il report sugli eventi di rischio include le informazioni seguenti:

- **Utente**: nome dell'utente usato durante l'operazione di accesso.
- **IP**: indirizzo IP del dispositivo usato per connettersi ad Azure Active Directory.
- **Località**: località usata per connettersi ad Azure Active Directory.
- **Ora di accesso**: ora in cui è stato eseguito l'accesso.
- **Stato**: stato dell'accesso.

Il report offre la possibilità di scaricare i dati del report.

![Creazione di report](./media/active-directory-reporting-security-azure-portal-free-basic/01.png)

In base all'analisi dell'accesso a rischio è possibile inviare feedback ad Azure Active Directory eseguendo queste azioni:

- Risolvi
- Contrassegna come falso positivo
- Ignora
- Riattiva

![Creazione di report](./media/active-directory-reporting-security-azure-portal-free-basic/21.png)

Per informazioni dettagliate, vedere [Chiusura manuale degli eventi di rischio](active-directory-identityprotection.md#closing-risk-events-manually).


## <a name="users-at-risk-report"></a>Report sugli utenti a rischio

Azure Active Directory Gratuito include un elenco degli account utente che potrebbero essere stati compromessi. 


![Creazione di report](./media/active-directory-reporting-security-azure-portal-free-basic/03.png)

Facendo clic su un utente nell'elenco verrà aperto il pannello dei dati utente correlato.
Analizzare la cronologia di accesso degli utenti a rischio e reimpostare la password, se necessario.

![Creazione di report](./media/active-directory-reporting-security-azure-portal-free-basic/46.png)



## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla creazione di report di Azure Active Directory, vedere [Guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).
- Per altre informazioni in merito, vedere [Azure Active Directory Identity Protection](active-directory-identityprotection.md).




<!--HONumber=Jan17_HO3-->


