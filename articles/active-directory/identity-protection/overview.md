---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Informazioni su come Azure AD Identity Protection consente di limitare la possibilità di un utente malintenzionato di sfruttare un'identità o un dispositivo compromesso e di proteggere un'identità o un dispositivo che in precedenza è stato sospettato o ritenuto essere compromesso.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b89cab41061376fc1d8b4cbffc8fe87b9677688
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125667"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Cos'è Azure Active Directory Identity Protection?

Azure Active Directory [Identity](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology) Protection consente alle organizzazioni di configurare risposte automatiche alle azioni sospette rilevate in relazione alle identità degli utenti.

## <a name="get-started"></a>Attività iniziali

Microsoft protegge le identità basate sul cloud da oltre un decennio. Con Azure Active Directory Identity Protection, è possibile usare nell'ambiente gli stessi sistemi di protezione usati da Microsoft per proteggere le identità.

La maggior parte delle violazioni della sicurezza si verifica quando utenti malintenzionati ottengono l'accesso a un ambiente impadronendosi dell'identità di un utente. Nel corso degli anni gli utenti malintenzionati hanno messo a punto tecniche sempre più efficaci per sfruttare le violazioni di terze parti e sferrare sofisticati attacchi di phishing. L'accesso a un account utente, anche quelli con privilegi limitati, permette agli utenti malintenzionati di accedere immediatamente a risorse aziendali importanti in modo relativamente semplice tramite il movimento laterale.

Di conseguenza, è necessario:

- Proteggere tutte le identità indipendentemente dal livello di privilegi
- Impedire in modo proattivo l'uso improprio delle identità compromesse

Trovare le identità compromesse non è un compito facile. Azure Active Directory usa l'euristica e gli algoritmi adattivi di apprendimento automatico per rilevare anomalie ed eventi imprevisti sospetti che indicano identità potenzialmente compromesse. Sulla base di tali dati, Identity Protection genera report e avvisi che consentono di valutare i problemi rilevati e di adottare le azioni di correzione o mitigazione appropriate.

Azure Active Directory Identity Protection è ben più di un semplice strumento di monitoraggio e reporting. Per proteggere le identità dell'organizzazione, è possibile configurare criteri basati sul rischio che rispondano automaticamente ai problemi rilevati quando viene raggiunto un livello di rischio specificato. Questi criteri, insieme ad altri controlli di accesso condizionale forniti da Azure Active Directory e da [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS), possono applicare il blocco automatico oppure avviare azioni di correzione adattive, che includono la reimpostazione della password e l'imposizione dell'autenticazione a più fattori.

### <a name="identity-protection-capabilities"></a>Funzionalità di Identity Protection

**Rilevamento di vulnerabilità e di account rischiosi:**  

- Raccomandazioni personalizzate per migliorare il comportamento di sicurezza in generale evidenziando le vulnerabilità.
- Calcolo dei livelli di rischio di accesso.
- Calcolo dei livelli di rischio utente.

**Analisi dei rilevamenti dei rischi:**

- invio di notifiche per i rilevamenti di rischi
- Analisi dei rilevamenti di rischi con informazioni pertinenti e contestuali
- Flussi di lavoro di base per tenere traccia delle analisi.
- Accesso semplificato ad azioni di correzione come la reimpostazione della password.

**Criteri di accesso condizionale basati sul rischio:**

- Criteri per mitigare gli accessi rischiosi con il blocco degli accessi o le richieste di autenticazione a più fattori
- Criteri per bloccare o proteggere gli account utente rischiosi.
- Criteri per richiedere la registrazione degli utenti per l'autenticazione a più fattori

## <a name="identity-protection-roles"></a>Ruoli di Identity Protection

Per bilanciare il carico delle attività di gestione per l'implementazione di Identity Protection, è possibile assegnare più ruoli. Azure AD Identity Protection supporta 3 ruoli di directory:

| Ruolo | Operazione consentita | Operazione non consentita |
| :-- | --- | --- |
| Amministratore globale | Accesso completo a Identity Protection, implementazione di Identity Protection| |
| Amministratore della sicurezza | Accesso completo a Identity Protection | Implementazione di Identity Protection, reimpostazione delle password per un utente |
| Ruolo con autorizzazioni di lettura per la sicurezza | Accesso in sola lettura a Identity Protection | Implementazione di Identity Protection, correzione degli utenti, configurazione dei criteri, reimpostazione delle password |

Per altri dettagli, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

## <a name="detection"></a>Rilevamento

### <a name="vulnerabilities"></a>Vulnerabilità

Azure Active Directory Identity Protection analizza la configurazione e rileva le vulnerabilità che possono avere effetto sulle identità dell'utente. Per altri dettagli, vedere [Vulnerabilità rilevate da Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-detections"></a>Rilevamenti dei rischi

Azure Active Directory usa l'euristica e algoritmi adattivi di apprendimento automatico per rilevare azioni sospette correlate alle identità dell'utente. Il sistema crea un record per ogni azione sospetta rilevata. Questi record sono anche noti come rilevamenti dei rischi.  
Per altre informazioni, vedere [Rilevamenti dei rischi di Azure Active Directory](../active-directory-identity-protection-risk-events.md).

## <a name="investigation"></a>Analisi

L'esperienza con Identity Protection inizia in genere dal relativo dashboard.

![Correzione](./media/overview/1000.png "Correzione")

Il dashboard consente di accedere a:

- Report, ad esempio **Utenti contrassegnati per il rischio**, **Rilevamenti dei rischi** e **Vulnerabilità**
- Impostazioni come la configurazione dei **criteri di sicurezza**, delle **notifiche** e della **registrazione per l'autenticazione a più fattori**

Si tratta in genere del punto di partenza dell'analisi, ovvero del processo di analisi di attività, log e altre informazioni pertinenti relative al rilevamento di un rischio, per decidere se sono necessarie procedure di correzione o mitigazione, se e come l'identità è stata compromessa e come è stata usata l'identità compromessa.

È possibile collegare le attività di analisi alle [notifiche](notifications.md) che Azure Active Directory Protection invia per posta elettronica.

## <a name="policies"></a>Criteri

Per implementare risposte automatiche, Azure Active Directory Identity Protection offre tre criteri:

- [Criteri di registrazione per l'autenticazione a più fattori](howto-mfa-policy.md)
- [Criteri di rischio utente](howto-user-risk-policy.md)
- [Criteri di rischio di accesso](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Channel 9: Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Abilitazione di Azure Active Directory Identity Protection](enable.md)
