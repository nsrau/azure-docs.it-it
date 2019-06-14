---
title: Studio su Azure Active Directory Identity Protection | Documentazione Microsoft
description: Informazioni su come Azure AD Identity Protection consente di limitare la possibilità di un utente malintenzionato di sfruttare un'identità o un dispositivo compromesso e di proteggere un'identità o un dispositivo che in precedenza è stato sospettato o ritenuto essere compromesso.
services: active-directory
keywords: Azure Active Directory Identity Protection, sicurezza, Cloud Discovery, gestione applicazioni, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24c3af12d35d07796db9255f0ac76dd1389bd013
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108835"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Studio sulla protezione delle identità di Azure Active Directory

Questo studio consente di:

* Inserire i dati nell'ambiente Identity Protection simulando eventi di rischio e vulnerabilità
* Configurare i criteri di accesso condizionale basati sui rischi e testare l'impatto di questi criteri


## <a name="simulating-risk-events"></a>Simulazione di eventi di rischio

Questa sezione riporta i passaggi per la simulazione dei seguenti tipi di eventi di rischio:

* Accessi da indirizzi IP anonimi (facile)
* Accessi da posizioni non note (intermedio)
* Trasferimento impossibile a posizioni atipiche (difficile)

Non è possibile simulare altri eventi di rischio in modo sicuro.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Accessi da indirizzi IP anonimi

Per altre informazioni su questo evento di rischio, vedere [Accessi da indirizzi IP anonimi](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Per completare la procedura seguente, sono necessari:

- [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) per simulare indirizzi IP anonimi. Potrebbe essere necessario usare una macchina virtuale, se l'organizzazione limita l'uso di Tor Browser.
- Un account di test che non è stato ancora registrato per l'autenticazione a più fattori.

**Per simulare un accesso da un IP anonimo, seguire questa procedura**:

1. Usando [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en), passare a [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Immettere le credenziali dell'account da visualizzare nel report **Accessi da indirizzi IP anonimi** .

L'accesso viene visualizzato nel dashboard Identity Protection entro 10-15 minuti. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Accessi da posizioni non note

Per altre informazioni su questo evento di rischio, vedere [Accessi da posizioni non note](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

Per simulare posizioni non note, è necessario accedere da una posizione e da un dispositivo mai usati prima con l'account di test.

Con la procedura seguente si usano gli elementi seguenti, appena creati:

- Connessione VPN, per simulare la nuova posizione.

- Macchina virtuale, per simulare un nuovo dispositivo.

Per completare la procedura seguente, è necessario usare un account utente con:

- Una cronologia di accesso di almeno 30 giorni.
- Autenticazione a più fattori abilitata.


**Per simulare un accesso da una posizione non nota, seguire questa procedura**:

1. Quando si accede con l'account di test, fare in modo che la richiesta di verifica dell'autenticazione a più fattori non riesca.
2. Usando la nuova rete VPN, passare a [https://myapps.microsoft.com](https://myapps.microsoft.com) e immettere le credenziali dell'account di test.
   

L'accesso viene visualizzato nel dashboard Identity Protection entro 10-15 minuti.

### <a name="impossible-travel-to-atypical-location"></a>Trasferimento impossibile a posizioni atipiche

Per altre informazioni su questo evento di rischio, vedere [Trasferimento impossibile a posizioni atipiche](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

La condizione di trasferimento impossibile è difficile da simulare perché l'algoritmo usa Machine Learning per eliminare i falsi positivi, ad esempio il trasferimento impossibile da dispositivi noti o l'accesso da VPN usate da altri utenti nella directory. Per iniziare a generare eventi di rischio, l'algoritmo richiede anche una cronologia di accesso di 14 giorni e 10 accessi per l'utente. A causa dei complessi modelli di Machine Learning coinvolti e delle regole sopra descritte, è possibile che con la procedura seguente non venga rilevato alcun evento di rischio. Per pubblicare questo evento di rischio, è consigliabile replicare la procedura per più account Azure AD.


**Per simulare un trasferimento impossibile a posizioni atipiche, seguire questa procedura**:

1. Usando il browser standard, passare a [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Immettere le credenziali dell'account per cui si vuole generare un evento di rischio trasferimento impossibile.
3. Modificare l'agente utente. Per modificare l'agente utente in Internet Explorer, usare Strumenti di sviluppo. Per modificare l'agente utente in Firefox o Chrome, usare un componente aggiuntivo di selezione dell'agente utente.
4. Modificare l'indirizzo IP. È possibile modificare l'indirizzo IP usando una VPN, un componente aggiuntivo Tor o avviare un nuovo computer in Azure in un altro data center.
5. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com) con le stesse credenziali usate in precedenza ed entro pochi minuti dall'accesso precedente.

L'accesso viene visualizzato nel dashboard Identity Protection entro 2-4 ore.

## <a name="simulating-vulnerabilities"></a>Simulazione di vulnerabilità
Le vulnerabilità sono punti deboli in un ambiente Azure AD che possono essere sfruttati da un utente malintenzionato. In Azure AD Identity Protection sono attualmente visibili 3 tipi di vulnerabilità che consentono di sfruttare altre funzionalità di Azure AD. Tali vulnerabilità verranno visualizzate automaticamente nel dashboard Identity Protection dopo aver impostato le funzionalità seguenti.

* Azure AD [Multi-Factor Authentication](../authentication/multi-factor-authentication.md)
* Azure AD [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/).
* Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md). 


## <a name="testing-security-policies"></a>Test dei criteri di sicurezza

Questa sezione illustra i passaggi per testare i criteri di sicurezza per il rischio utente e il rischio di accesso.


### <a name="user-risk-security-policy"></a>Criteri di sicurezza per il rischio utente

Per altre informazioni, vedere [Come configurare i criteri di rischio utente](howto-user-risk-policy.md).

![Rischio utente](./media/playbook/02.png " - Studio")


**Per testare i criteri di sicurezza per il rischio utente, seguire questa procedura**:

1. Accedere a [https://portal.azure.com](https://portal.azure.com) con credenziali di amministratore globale per il tenant.
2. Passare a **Identity Protection**. 
3. Nella pagina **Azure AD Identity Protection** fare clic su **Criteri di rischio utente**.
4. Nella sezione **Assegnazioni** selezionare gli utenti (e i gruppi) desiderati e il livello di rischio utente.

    ![Rischio utente](./media/playbook/03.png " - Studio")

5. Nella sezione Controlli selezionare il controllo di accesso desiderato, ad esempio Richiedi modifica password.
5. Come valore di **Imponi criteri**, selezionare **Disattivato**.
6. Alzare di livello il rischio di utente di un account di test, ad esempio, simulando alcune volte uno degli eventi di rischio.
7. Attendere alcuni minuti e poi verificare che il livello utente sia Medio. Se non lo è, simulare altri eventi di rischio per l'utente.
8. Come valore di **Imponi criteri**, selezionare **Attivato**.
9. È ora possibile testare utente basati sul rischio di accesso condizionale effettuando l'accesso usando un utente con un livello di rischio elevato.
    
    

### <a name="sign-in-risk-security-policy"></a>Criteri di sicurezza per il rischio di accesso

Per altre informazioni, vedere [Come configurare i criteri di rischio di accesso](howto-sign-in-risk-policy.md).

![Rischio di accesso](./media/playbook/01.png " - Studio")


**Per testare i criteri di rischio di accesso, seguire questa procedura:**

1. Accedere a [https://portal.azure.com](https://portal.azure.com) con credenziali di amministratore globale per il tenant.

2. Passare ad **Azure AD Identity Protection**.

3. Nella pagina principale di **Azure AD Identity Protection** fare clic su **Criteri di rischio di accesso**. 

4. Nella sezione **Assegnazioni** selezionare gli utenti (e i gruppi) desiderati e il livello di rischio di accesso.

    ![Rischio di accesso](./media/playbook/04.png " - Studio")


5. Nella sezione **Controlli** selezionare il controllo di accesso desiderato, ad esempio **Richiedi autenticazione a più fattori**. 

6. Come valore di **Imponi criteri**, selezionare **Attivato**.

7. Fare clic su **Save**.

8. È ora possibile testare l'accesso condizionale basati sul rischio di Accedi effettuando l'accesso usando una sessione a rischio (ad esempio, usando il browser Tor). 

 




## <a name="see-also"></a>Vedere anche

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

