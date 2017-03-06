---
title: Report di sicurezza per gli utenti a rischio nel portale di Azure Active Directory, anteprima | Microsoft Docs
description: Informazioni sul report di sicurezza per gli utenti a rischio nel portale di Azure Active Directory, anteprima
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 349109e0c12a1394f96529a94ab884eeb451d242
ms.openlocfilehash: 48c504a9ed5bc4ef9f0bff889df031962c5bf6e8
ms.lasthandoff: 02/22/2017


---
# <a name="users-at-risk-security-report-in-the-azure-active-directory-portal---preview"></a>Report di sicurezza per gli utenti a rischio nel portale di Azure Active Directory, anteprima

I report di sicurezza nell'[anteprima](active-directory-preview-explainer.md) di Azure Active Directory permettono di ottenere informazioni approfondite sulla probabile presenza di account utente compromessi nell'ambiente. 

Azure Active Directory rileva le azioni sospette correlate agli account utente. Per ogni azione rilevati viene creato un record denominato *evento di rischio*. Per altre informazioni, vedere [Azure Active Directory risk events](active-directory-identity-protection-risk-events.md) (Eventi di rischio di Azure Active Directory). 

Gli eventi di rischio rilevati vengono usati per calcolare:

- **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. Per informazioni dettagliate, vedere [Accessi a rischio](active-directory-identityprotection.md#risky-sign-ins). 

- **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. Per informazioni dettagliate, vedere [Utenti contrassegnati per il rischio](active-directory-identityprotection.md#users-flagged-for-risk).  

Nel portale di Azure, è possibile trovare i report di sicurezza nel pannello di **Azure Active Directory** nella sezione **Sicurezza**.  

![Accessi a rischio](./media/active-directory-reporting-security-user-at-risk/10.png)

## <a name="azure-active-directory-free-and-basic-edition"></a>Versione gratuita e di base di Azure Active Directory

Il report per gli utenti a rischio nella versione gratuita e di base di Azure Active Directory offre un elenco degli account utente che potrebbero essere stati compromessi. 


![Accessi a rischio](./media/active-directory-reporting-security-user-at-risk/03.png)

Selezionando un utente verrà aperto il pannello dei dati utente correlati.
Analizzare la cronologia di accesso degli utenti a rischio e reimpostare la password, se necessario.

![Accessi a rischio](./media/active-directory-reporting-security-user-at-risk/46.png)

## <a name="azure-active-directory-premium-editions"></a>Versione Premium di Azure Active Directory

Il report per gli utenti a rischio della versione Premium di Azure Active Directory offre:

- Un [elenco di account di utenti](active-directory-identityprotection.md#users-flagged-for-risk) che potrebbero essere stati compromessi 

- informazioni aggregate sui [tipi di evento di rischio](active-directory-identity-protection-risk-events.md) che sono stati rilevati

- Un'opzione per scaricare il report

- avere un'opzione per configurare i [criteri per la correzione del rischio per gli utenti](active-directory-identityprotection.md#user-risk-security-policy)  


![Accessi a rischio](./media/active-directory-reporting-security-user-at-risk/71.png)

Quando si seleziona un utente, si ottiene la visualizzazione di un report dettagliato per questo utente che consente di:

- Aprire le visualizzazioni di tutti gli accessi

- Reimpostare la password dell'utente

- Eliminare tutti gli eventi

- Ricercare la causa degli eventi a rischio segnalati per l'utente. 


![Accessi a rischio](./media/active-directory-reporting-security-user-at-risk/324.png)


Per ricercare la causa di un evento di rischio, selezionarlo dall'elenco.  
Verrà visualizzato il pannello **Dettagli** per questo evento di rischio. Nel pannello **Dettagli**, è possibile [chiudere manualmente un evento di rischio](active-directory-identityprotection.md#closing-risk-events-manually) o riattivare un evento di rischio chiuso manualmente. 


![Accessi a rischio](./media/active-directory-reporting-security-user-at-risk/325.png)



## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni in merito, vedere [Azure Active Directory Identity Protection](active-directory-identityprotection.md).


