---
title: Versioni e piani a consumo di Azure MFA | Microsoft Docs
description: Informazioni sul client Multi-Factor Authentication e sui diversi metodi e versioni disponibili. Informazioni dettagliate su ogni piano a consumo
keywords: 
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: kgremban
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 525ca7733d509b6831e6c5ab7a6f3a5ce5250156
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Come ottenere Azure Multi-Factor Authentication

Quando si tratta di proteggere gli account, la verifica in due passaggi dovrebbe essere lo standard in tutta l'organizzazione. Questa funzionalità è importante soprattutto per gli account amministrativi con accesso con privilegi alle risorse. Per questo motivo, Microsoft offre le funzionalità di base della verifica in due passaggi agli amministratori di Office 365 e di Azure, senza costi aggiuntivi. Per aggiornare le funzionalità per gli amministratori o estendere la verifica in due passaggi agli altri utenti, è possibile acquistare Azure Multi-Factor Authentication. 

Questo articolo illustra la differenza tra le versioni offerte agli amministratori e la versione completa di Azure MFA. Se si è pronti per distribuire l'offerta completa di Azure MFA, l'ultima sezione descrive le opzioni di implementazione e come Microsoft calcola il consumo.

>[!IMPORTANT]
>Questo articolo è una guida che spiega i diversi modi di acquistare Azure Multi-Factor Authentication. Per dettagli specifici sui prezzi e sulla fatturazione, è consigliabile vedere sempre la [pagina dei prezzi di Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versioni disponibili di Azure Multi-Factor Authentication

La tabella seguente descrive le differenze tra le tre versioni di Multi-Factor Authentication:

| Versione | Descrizione |
| --- | --- |
| Multi-Factor Authentication per Office 365 |Questa versione funziona solo con le applicazioni di Office 365 e viene gestita dal portale di Office 365. Gli amministratori possono [proteggere le risorse di Office 365 con la verifica in due passaggi](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Questa versione è inclusa in una sottoscrizione di Office 365. |
| Multi-Factor Authentication per amministratori di Azure | Gli amministratori globali di tenant di Azure possono abilitare la verifica in due passaggi per gli account di amministratore senza costi aggiuntivi.|
| Azure Multi-Factor Authentication | Azure Multi-Factor Authentication, definito spesso la versione "completa", offre la più ampia gamma di funzionalità. Mette a disposizione opzioni di configurazione aggiuntive tramite il [portale di Azure classico](https://manage.windowsazure.com), segnalazione avanzata e il supporto per un intervallo di applicazioni locali e cloud. Azure Multi-Factor Authentication è incluso nei [piani Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) nei [piani Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing) e può essere distribuito nel cloud o localmente. |

## <a name="feature-comparison-of-versions"></a>Confronto tra le funzionalità delle versioni
La tabella seguente indica un elenco delle funzionalità disponibili nelle varie versioni di Azure Multi-Factor Authentication.

> [!NOTE]
> Questa tabella di confronto descrive le funzionalità incluse in ogni versione di Multi-Factor Authentication. Se si ha il servizio Azure Multi-Factor Authentication completo, alcune funzionalità potrebbero non essere disponibili a seconda che si usi [MFA nel cloud o MFA in locale](multi-factor-authentication-get-started.md).


| Funzionalità | Multi-Factor Authentication per Office 365 | Multi-Factor Authentication per amministratori di Azure | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| Protezione degli account amministratore con MFA |● |● (solo per l'account amministratore globale) |● |
| App per dispositivi mobili come secondo fattore |● |● |● |
| Chiamata telefonica come secondo fattore |● |● |● |
| SMS come secondo fattore |● |● |● |
| Password di app per i client che non supportano MFA |● |● |● |
| Controllo amministrativo sui metodi di verifica |● |● |● |
| Modalità PIN | | |● |
| Avviso di illecito | | |● |
| Report MFA | | |● |
| Bypass monouso | | |● |
| Messaggi di saluto personalizzati per le telefonate | | |● |
| ID chiamante personalizzato per le telefonate | | |● |
| IP attendibili | | |● |
| Memorizzazione di MFA per dispositivi attendibili |● |● |● |
| SDK MFA | | |● (richiede un provider Multi-Factor Authentication e una sottoscrizione completa di Azure) |
| MFA per applicazioni locali | | |● |

## <a name="how-to-get-azure-multi-factor-authentication"></a>Come ottenere Azure Multi-Factor Authentication
Se si desidera avere a disposizione tutte le funzionalità di Azure Multi-Factor Authentication, ci sono varie opzioni:

### <a name="option-1---mfa-licenses"></a>Opzione 1: licenze MFA

Acquistare le licenze di Azure Multi-Factor Authentication e assegnarle agli utenti in Azure Active Directory. 

Se si usa questa opzione, creare un provider Azure Multi-Factor Authentication solo se è necessario fornire la verifica in due passaggi per alcuni utenti privi di licenze. In caso contrario, il servizio potrebbe essere fatturato due volte.

### <a name="option-2---bundled-licenses-that-include-mfa"></a>Opzione 2: licenze in bundle che includono MFA

Acquistare licenze che includono Azure Multi-Factor Authentication, ad esempio Azure Active Directory Premium o Enterprise Mobility + Security, e assegnarle agli utenti in Azure Active Directory. 

Se si usa questa opzione, è consigliabile creare un provider Azure Multi-Factor Authentication solo se è necessario fornire la verifica in due passaggi anche per alcuni utenti privi di licenze. In caso contrario, il servizio potrebbe essere fatturato due volte. 

### <a name="option-3---mfa-consumption-based-model"></a>Opzione 3: modello in base al consumo di MFA

Creare un provider Azure Multi-Factor Authentication all'interno di una sottoscrizione di Azure. I provider Azure MFA sono risorse di Azure fatturate sulla base del contratto Enterprise, usando i fondi degli impegni monetari di Azure o sulla carta di credito come tutte le altre risorse di Azure. Questi provider possono essere creati solo nelle sottoscrizioni complete di Azure, non nelle sottoscrizione limitate di Azure con un limite di spesa di $ 0. Le sottoscrizioni limitate vengono create quando si attivano le licenze, come nelle opzioni 1 e 2. 

Quando si usa un provider di Azure Multi-Factor Authentication, sono disponibili due modelli di uso fatturati tramite la sottoscrizione di Azure:  

1. **Per utente**: per le aziende che vogliono abilitare la verifica in due passaggi per un numero fisso di dipendenti che richiedono regolarmente l'autenticazione. La fatturazione per utente si basa sul numero di utenti abilitati per MFA nel tenant di Azure AD e in Azure MFA Server. Se gli utenti sono abilitati per MFA sia in Azure AD che in Azure MFA Server e la sincronizzazione dei domini (Azure AD Connect) è abilitata, viene considerato il set di utenti più numeroso. Se la sincronizzazione dei domini non è abilitata, viene calcolata la somma di tutti gli utenti abilitati per MFA in Azure AD e in Azure MFA Server. La fatturazione viene ripartita e segnalata ogni giorno al sistema commerciale. 

  > [!NOTE]
  > Esempio di fatturazione 1: oggi si hanno 5.000 utenti abilitati per l'autenticazione a più fattori. Il sistema MFA divide il numero per 31 e segnala 161,29 utenti per quel giorno. Il giorno dopo si abilitano altri 15 utenti, quindi il sistema MFA segnala 161,77 utenti per quel giorno. Alla fine del ciclo di fatturazione, il numero totale di utenti fatturati sulla base della sottoscrizione di Azure arriva a circa 5.000. 
  >
  > Esempio di fatturazione 2: alcuni utenti hanno le licenze, altri invece no, quindi è disponibile un provider Azure MFA per utente per compensare la differenza. Nel tenant esistono 4.500 licenze Enterprise Mobility + Security, ma 5.000 utenti abilitati per MFA. Alla sottoscrizione di Azure vengono fatturati 500 utenti, ripartiti e segnalati ogni giorno come 16,13 utenti. 

2. **Per autenticazione**: per le aziende che vogliono abilitare la verifica in due passaggi per un gruppo esteso di utenti che non richiedono regolarmente l'autenticazione. La fatturazione si basa sul numero di richieste di verifica in due passaggi, indipendentemente dal fatto che tali verifiche riescano o vengano negate. Questa fatturazione viene visualizzata nel rendiconto sull'utilizzo di Azure in pacchetti di 10 autenticazioni e viene segnalata ogni giorno. 

  > [!NOTE]
  > Esempio di fatturazione 3: oggi il servizio Azure MFA ha ricevuto 3.105 richieste di verifica in due passaggi. Alla sottoscrizione di Azure vengono fatturati 310,5 pacchetti di autenticazione. 

È importante notare che, anche con licenze Azure MFA, continua a essere fatturata la configurazione in base al consumo. Se si configura un provider Azure MFA per autenticazione, viene fatturata ogni richiesta di verifica in due passaggi, anche quelle eseguite dagli utenti con licenza. Se si configura un provider Azure MFA per utente in un dominio non collegato al tenant di Azure AD, viene fatturato ogni utente abilitato anche se gli utenti hanno licenze in Azure AD. 

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui prezzi, vedere [Multi-Factor Authentication Prezzi](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Scegliere se distribuire Azure MFA [nel cloud o in locale](multi-factor-authentication-get-started.md)
