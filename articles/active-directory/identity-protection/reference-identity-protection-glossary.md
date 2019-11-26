---
title: Glossario di Azure Active Directory Identity Protection
description: Glossario di Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232353"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glossario di Azure Active Directory Identity Protection

### <a name="at-risk-user"></a>A rischio (utente)
Utente con uno o più rilevamenti di rischio attivi. 

### <a name="atypical-sign-in-location"></a>Posizione di accesso atipica
Accesso da una posizione geografica non tipica per l'utente specifico, utenti simili o il tenant.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Un modulo di sicurezza di Azure Active Directory che fornisce una visualizzazione consolidata dei rilevamenti dei rischi e potenziali vulnerabilità che interessano le identità di un'organizzazione.

### <a name="conditional-access"></a>Accesso condizionale
Criteri per la protezione dell'accesso alle risorse. Le regole di accesso condizionale vengono archiviate nel Azure Active Directory e vengono valutate da Azure AD prima di concedere l'accesso alla risorsa.  Le regole includono la limitazione dell'accesso in base alla posizione dell'utente, all'integrità del dispositivo o al metodo di autenticazione dell'utente.

### <a name="credentials"></a>Credenziali
Informazioni che includono l'identificazione e la prova dell'identificazione usata per ottenere l'accesso alle risorse locali e di rete. Tra le credenziali sono inclusi nomi utente e password, smart card e certificati.

### <a name="event"></a>Evento
Record di un'attività in Azure Active Directory.

### <a name="false-positive-risk-detection"></a>Falso positivo (rilevamento del rischio)
Stato di rilevamento del rischio impostato manualmente da un utente di Identity Protection, che indica che il rilevamento del rischio è stato analizzato ed è stato erroneamente contrassegnato come rilevamento dei rischi.

### <a name="identity"></a>Identità
Persona o entità che deve essere verificata tramite autenticazione, in base a criteri quali password o certificato.

### <a name="identity-risk-detection"></a>Rilevamento del rischio di identità
Azure AD evento contrassegnato come anomalo da Identity Protection e potrebbe indicare che un'identità è stata compromessa.

### <a name="ignored-risk-detection"></a>Ignorato (rilevamento del rischio)
Stato di rilevamento del rischio impostato manualmente da un utente di Identity Protection, che indica che il rilevamento dei rischi viene chiuso senza eseguire un'azione di correzione.

### <a name="impossible-travel-from-atypical-locations"></a>Trasferimento impossibile con posizioni atipiche
Rilevamento dei rischi attivato quando vengono rilevati due accessi per lo stesso utente, in cui almeno uno di essi si trova in una posizione di accesso atipica e dove il tempo tra gli accessi è inferiore al tempo minimo necessario per spostarsi fisicamente tra questi posizioni.  

### <a name="investigation"></a>Analisi
Il processo di revisione di attività, log e altre informazioni rilevanti relative a un rilevamento dei rischi per decidere se sono necessarie procedure di correzione o mitigazione, comprendere se e come l'identità è stata compromessa e comprendere il modo in cui l'identità è stata compromessa è stata usata l'identità.

### <a name="leaked-credentials"></a>Credenziali perse
Rilevamento dei rischi attivato quando le credenziali dell'utente corrente (nome utente e password) vengono trovate pubblicate nel Dark Web dai ricercatori.

### <a name="mitigation"></a>Mitigazione
Azione che consente di limitare o eliminare la possibilità che un utente malintenzionato sfrutti un'identità o un dispositivo compromesso senza ripristinare l'identità o il dispositivo a uno stato sicuro. Una mitigazione non risolve i rilevamenti di rischio precedenti associati all'identità o al dispositivo.

### <a name="multi-factor-authentication"></a>Autenticazione a più fattori
Metodo di autenticazione che richiede due o più fattori di autenticazione. Può includere un elemento di cui l'utente è in possesso, ad esempio un certificato, un'informazione nota all'utente, ad esempio nomi utente, password o passphrase, attributi fisici, ad esempio un'impronta digitale, e attributi personali, ad esempio una firma personale.

### <a name="offline-detection"></a>Rilevamento offline
Rilevamento di anomalie e valutazione del rischio di un evento, ad esempio un tentativo di accesso dopo il fatto, per un evento già accaduto.

### <a name="policy-condition"></a>Condizione dei criteri
Parte dei criteri di sicurezza che definiscono le entità (gruppi, utenti, app, piattaforme dei dispositivi, stati dei dispositivi, intervalli di indirizzi IP, tipi di client) inclusi nei criteri o esclusi da essi.

### <a name="policy-rule"></a>Regola dei criteri
Parte di un criterio di sicurezza che descrive le circostanze che attivano i criteri e le azioni intraprese quando viene attivato il criterio.

### <a name="prevention"></a>Prevenzione
Azione per prevenire i danni arrecati all'organizzazione derivanti dall'uso non corretto di un'identità o di un dispositivo che si sospetta o si ritiene sia compromesso. Un'azione di prevenzione non protegge il dispositivo o l'identità e non risolve i rilevamenti dei rischi precedenti.

### <a name="privileged-user"></a>Con privilegi (utente)
Un utente che al momento di un rilevamento dei rischi aveva autorizzazioni di amministratore permanenti o temporanee per una o più risorse in Azure Active Directory, ad esempio un amministratore globale, un amministratore di fatturazione, un amministratore del servizio, un amministratore utente e una password Amministratore. 

### <a name="real-time"></a>Tempo reale
Vedere Rilevamento in tempo reale.

### <a name="real-time-detection"></a>Rilevamento in tempo reale
Rilevamento di anomalie e valutazione del rischio di un evento, ad esempio un tentativo di accesso prima che all'evento sia consentito di procedere.

### <a name="remediated-risk-detection"></a>Corretti (rilevamento del rischio)
Stato di rilevamento del rischio impostato automaticamente da Identity Protection, che indica che il rilevamento dei rischi è stato risolto utilizzando l'azione di correzione standard per questo tipo di rilevamento dei rischi. Ad esempio, quando viene reimpostata la password utente, molti rilevamenti dei rischi che indicano che la password precedente è stata compromessa vengono risolti automaticamente.

### <a name="remediation"></a>Correzione
Azione che consente di proteggere un'identità o un dispositivo che in precedenza è stato ritenuto o sospettato essere compromesso. Un'azione di correzione ripristina l'identità o il dispositivo a uno stato sicuro e risolve i rilevamenti di rischio precedenti associati all'identità o al dispositivo.

### <a name="resolved-risk-detection"></a>Risolto (rilevamento del rischio)
Stato di rilevamento del rischio impostato manualmente da un utente di Identity Protection, che indica che l'utente ha eseguito un'azione di correzione appropriata all'esterno di Identity Protection e che il rilevamento dei rischi deve essere considerato chiuso.

### <a name="risk-detection-status"></a>Stato di rilevamento del rischio
Proprietà di un rilevamento dei rischi, che indica se l'evento è attivo e se chiuso, il motivo per la chiusura.

### <a name="risk-detection-type"></a>Tipo di rilevamento dei rischi
Categoria per il rilevamento dei rischi, che indica il tipo di anomalia che ha causato l'evento come rischioso.

### <a name="risk-level-risk-detection"></a>Livello di rischio (rilevamento del rischio)
Indicazione (alta, media o bassa) della gravità del rilevamento dei rischi per consentire agli utenti di Identity Protection di assegnare una priorità alle azioni eseguite per ridurre i rischi per la propria organizzazione. 

### <a name="risk-level-sign-in"></a>Livello di rischio (accesso)
Può essere Alto, Medio o Basso e indica la probabilità che per un accesso specifico qualcun altro stia tentando di usare l'identità dell'utente.

### <a name="risk-level-user-compromise"></a>Livello di rischio (compromissione dell'utente)
Può essere Alto, Medio o Basso e indica la probabilità che un'identità sia stata compromessa.

### <a name="risk-level-vulnerability"></a>Livello di rischio (vulnerabilità)
Può essere Alto, Medio o Basso e indica la gravità della vulnerabilità per consentire agli utenti di Identity Protection di assegnare correttamente le priorità alle azioni da eseguire al fine di ridurre i rischi per l'organizzazione.

### <a name="secure-identity"></a>Protezione (identità)
Indica di eseguire un'azione di correzione, ad esempio una modifica della password o la ricreazione di un'immagine del computer, per ripristinare un'identità potenzialmente compromessa a uno stato non compromesso.

### <a name="security-policy"></a>Criteri di sicurezza
Raccolta di regole e condizioni per i criteri. È possibile applicare criteri a entità come utenti, gruppi, app, dispositivi, piattaforme dei dispositivi, stati dei dispositivi, intervalli di indirizzi IP e tipi di client OAuth 2.0. Quando un criterio è abilitato, questo viene valutato ogni volta che a un'entità inclusa nel criterio viene rilasciato un token per una risorsa.

### <a name="sign-in-v"></a>Accedere (v)
Consente di eseguire l'autenticazione a un'identità in Azure Active Directory.

### <a name="sign-in-n"></a>Accesso (s)
Processo o azione di autenticazione di un'identità in Azure Active Directory ed evento che acquisisce questa operazione.

### <a name="sign-in-from-anonymous-ip-address"></a>Accesso da indirizzo IP anonimo
Rilevamento dei rischi attivato dopo un accesso riuscito dall'indirizzo IP che è stato identificato come indirizzo IP proxy anonimo.

### <a name="sign-in-from-infected-device"></a>Accesso da dispositivo infetto
Rilevamento dei rischi attivato quando un accesso proviene da un indirizzo IP, che è noto come usato da uno o più dispositivi compromessi che tentano attivamente di comunicare con un server bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Accesso da indirizzo IP con attività sospetta
Rilevamento dei rischi attivato dopo un accesso riuscito da un indirizzo IP con un numero elevato di tentativi di accesso non riusciti tra più account utente in un breve periodo di tempo.

### <a name="sign-in-from-unfamiliar-location"></a>Accesso da posizione non nota
Rilevamento dei rischi attivato quando un utente accede correttamente da una nuova posizione (IP, Latitudine/Longitudine e ASN).

### <a name="sign-in-risk"></a>Rischio di accesso
Vedere Livello di rischio (accesso)

### <a name="sign-in-risk-policy"></a>Configura
Criteri di accesso condizionale che valutano il rischio per un accesso specifico e applicano le mitigazioni in base a condizioni e regole predefinite.

### <a name="user-compromise-risk"></a>Rischio di compromissione dell'utente
Vedere Livello di rischio (compromissione dell'utente)

### <a name="user-risk"></a>Rischio utente
Vedere Livello di rischio (compromissione dell'utente)

### <a name="user-risk-policy"></a>Criteri di rischio utente
Criteri di accesso condizionale che considerano l'accesso e applicano le mitigazioni in base a condizioni e regole predefinite.

### <a name="users-flagged-for-risk"></a>Utenti contrassegnati per il rischio
Utenti con rilevamento dei rischi, attivi o corretti

### <a name="vulnerability"></a>Vulnerabilità
Configurazione o condizione in Azure Active Directory che rende la directory vulnerabile ad attacchi o minacce.

## <a name="see-also"></a>Vedere anche

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
