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
A user with one or more active risk detections. 

### <a name="atypical-sign-in-location"></a>Posizione di accesso atipica
Accesso da una posizione geografica non tipica per l'utente specifico, utenti simili o il tenant.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
A security module of Azure Active Directory that provides a consolidated view into risk detections and potential vulnerabilities affecting an organization’s identities.

### <a name="conditional-access"></a>Accesso condizionale
Criteri per la protezione dell'accesso alle risorse. Conditional Access rules are stored in the Azure Active Directory and are evaluated by Azure AD before granting access to the resource.  Le regole includono la limitazione dell'accesso in base alla posizione dell'utente, all'integrità del dispositivo o al metodo di autenticazione dell'utente.

### <a name="credentials"></a>Credenziali
Informazioni che includono l'identificazione e la prova dell'identificazione usata per ottenere l'accesso alle risorse locali e di rete. Tra le credenziali sono inclusi nomi utente e password, smart card e certificati.

### <a name="event"></a>Event
Record di un'attività in Azure Active Directory.

### <a name="false-positive-risk-detection"></a>False-positive (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection was investigated and was incorrectly flagged as a risk detection.

### <a name="identity"></a>Identità
Persona o entità che deve essere verificata tramite autenticazione, in base a criteri quali password o certificato.

### <a name="identity-risk-detection"></a>Identity risk detection
Azure AD event that was flagged as anomalous by Identity Protection, and may indicate that an identity has been compromised.

### <a name="ignored-risk-detection"></a>Ignored (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection is closed without taking a remediation action.

### <a name="impossible-travel-from-atypical-locations"></a>Trasferimento impossibile con posizioni atipiche
A risk detection triggered when two sign-ins for the same user are detected, where at least one of them is from an atypical sign-in location, and where the time between the sign-ins is shorter than the minimum time it would take to physically travel between these locations.  

### <a name="investigation"></a>Analisi
The process of reviewing the activities, logs, and other relevant information related to a risk detection to decide whether remediation or mitigation steps are necessary, understand if and how the identity was compromised, and understand how the compromised identity was used.

### <a name="leaked-credentials"></a>Credenziali perse
A risk detection triggered when current user credentials (user name and password) are found posted publicly in the Dark   web by our researchers.

### <a name="mitigation"></a>Mitigazione
Azione che consente di limitare o eliminare la possibilità che un utente malintenzionato sfrutti un'identità o un dispositivo compromesso senza ripristinare l'identità o il dispositivo a uno stato sicuro. A mitigation does not resolve previous risk detections associated with the identity or device.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Metodo di autenticazione che richiede due o più fattori di autenticazione. Può includere un elemento di cui l'utente è in possesso, ad esempio un certificato, un'informazione nota all'utente, ad esempio nomi utente, password o passphrase, attributi fisici, ad esempio un'impronta digitale, e attributi personali, ad esempio una firma personale.

### <a name="offline-detection"></a>Rilevamento offline
Rilevamento di anomalie e valutazione del rischio di un evento, ad esempio un tentativo di accesso dopo il fatto, per un evento già accaduto.

### <a name="policy-condition"></a>Condizione dei criteri
Parte dei criteri di sicurezza che definiscono le entità (gruppi, utenti, app, piattaforme dei dispositivi, stati dei dispositivi, intervalli di indirizzi IP, tipi di client) inclusi nei criteri o esclusi da essi.

### <a name="policy-rule"></a>Regola dei criteri
The part of a security policy that describes the circumstances that would trigger the policy, and the actions taken when the policy is triggered.

### <a name="prevention"></a>Prevenzione
Azione per prevenire i danni arrecati all'organizzazione derivanti dall'uso non corretto di un'identità o di un dispositivo che si sospetta o si ritiene sia compromesso. A prevention action does not secure the device or identity, and does not resolve previous risk detections.

### <a name="privileged-user"></a>Con privilegi (utente)
A user that at the time of a risk detection, had permanent or temporary admin permissions to one or more resources in Azure Active Directory, such as a Global Administrator, Billing Administrator, Service Administrator, User administrator, and Password Administrator. 

### <a name="real-time"></a>Tempo reale
Vedere Rilevamento in tempo reale.

### <a name="real-time-detection"></a>Rilevamento in tempo reale
Rilevamento di anomalie e valutazione del rischio di un evento, ad esempio un tentativo di accesso prima che all'evento sia consentito di procedere.

### <a name="remediated-risk-detection"></a>Remediated (risk detection)
A risk detection status set automatically by Identity Protection, indicating that the risk detection was remediated using the standard remediation action for this type of risk detection. For example, when the user password is reset, many risk detections that indicate that the previous password was compromised are automatically remediated.

### <a name="remediation"></a>Correzione
Azione che consente di proteggere un'identità o un dispositivo che in precedenza è stato ritenuto o sospettato essere compromesso. A remediation action restores the identity or device to a safe state, and resolves previous risk detections associated with the identity or device.

### <a name="resolved-risk-detection"></a>Resolved (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the user took an appropriate remediation action outside Identity Protection, and that the risk detection should be considered closed.

### <a name="risk-detection-status"></a>Risk detection status
A property of a risk detection, indicating whether the event is active, and if closed, the reason for closing it.

### <a name="risk-detection-type"></a>Tipo di rilevamento dei rischi
A category for the risk detection, indicating the type of anomaly that caused the event to be considered risky.

### <a name="risk-level-risk-detection"></a>Risk level (risk detection)
An indication (High, Medium, or Low) of the severity of the risk detection to help Identity Protection users prioritize the actions they take to reduce the risk to their organization. 

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
A risk detection triggered after a successful sign-in from IP address that has been identified as an anonymous proxy IP address.

### <a name="sign-in-from-infected-device"></a>Accesso da dispositivo infetto
A risk detection triggered when a sign-in originates from an IP address, which is known to be used by one or more compromised devices, which are actively attempting to communicate with a bot server.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Accesso da indirizzo IP con attività sospetta
A risk detection triggered after a successful sign-in from an IP address with a high number of failed login attempts across multiple user accounts over a short period of time.

### <a name="sign-in-from-unfamiliar-location"></a>Accesso da posizione non nota
A risk detection triggered when a user successfully signs in from a new location (IP, Latitude/Longitude, and ASN).

### <a name="sign-in-risk"></a>Rischio di accesso
Vedere Livello di rischio (accesso)

### <a name="sign-in-risk-policy"></a>Criteri di rischio di accesso
A Conditional Access policy that evaluates the risk to a specific sign-in and applies mitigations based on predefined conditions and rules.

### <a name="user-compromise-risk"></a>Rischio di compromissione dell'utente
Vedere Livello di rischio (compromissione dell'utente)

### <a name="user-risk"></a>Rischio utente
Vedere Livello di rischio (compromissione dell'utente)

### <a name="user-risk-policy"></a>Criteri di rischio utente
A Conditional Access policy that considers the sign-in and applies mitigations based on predefined conditions and rules.

### <a name="users-flagged-for-risk"></a>Utenti contrassegnati per il rischio
Users that have risk detections, which are either active or remediated

### <a name="vulnerability"></a>Vulnerabilità
Configurazione o condizione in Azure Active Directory che rende la directory vulnerabile ad attacchi o minacce.

## <a name="see-also"></a>Vedi anche

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
