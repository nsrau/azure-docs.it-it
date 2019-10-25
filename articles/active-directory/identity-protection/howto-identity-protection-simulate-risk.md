---
title: Simulazione del rilevamento del rischio in Azure AD Identity Protection
description: Informazioni su come simulare i rilevamenti di rischio in Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50e406e74c0b78f41830c4ea7c5b10830002ed3
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72886935"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulazione del rilevamento del rischio in Identity Protection

È possibile che gli amministratori desiderino simulare il rischio nell'ambiente per ottenere gli elementi seguenti:

- Popola i dati nell'ambiente Identity Protection simulando i rilevamenti e le vulnerabilità dei rischi.
- Configurare i criteri di accesso condizionale basati sul rischio e verificare l'effetto di questi criteri.

Questo articolo illustra i passaggi per simulare i tipi di rilevamento dei rischi seguenti:

- Indirizzo IP anonimo (facile)
- Proprietà di accesso non note (moderato)
- Corsa atipica (difficile)

Non è possibile simulare altri rilevamenti del rischio in modo sicuro.

Altre informazioni su ogni rilevamento dei rischi sono disponibili nell'articolo relativo al [rischio](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Indirizzo IP anonimo

Per completare la procedura seguente, sono necessari:

- [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) per simulare indirizzi IP anonimi. Potrebbe essere necessario usare una macchina virtuale, se l'organizzazione limita l'uso di Tor Browser.
- Un account di test non ancora registrato per Multi-Factor Authentication di Azure.

**Per simulare un accesso da un IP anonimo, seguire questa procedura**:

1. Usando [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en), passare a [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Immettere le credenziali dell'account da visualizzare nel report **Accessi da indirizzi IP anonimi** .

L'accesso viene visualizzato nel dashboard Identity Protection entro 10-15 minuti. 

## <a name="unfamiliar-sign-in-properties"></a>Proprietà di accesso insolite

Per simulare posizioni non note, è necessario accedere da una posizione e da un dispositivo mai usati prima con l'account di test.

Con la procedura seguente si usano gli elementi seguenti, appena creati:

- Connessione VPN, per simulare la nuova posizione.
- Macchina virtuale, per simulare un nuovo dispositivo.

Per completare la procedura seguente, è necessario usare un account utente con:

- Una cronologia di accesso di almeno 30 giorni.
- Multi-Factor Authentication di Azure abilitato.

**Per simulare un accesso da una posizione non nota, seguire questa procedura**:

1. Quando si accede con l'account di test, non si verificano problemi di autenticazione a più fattori (multi-factor authentication) senza superare la richiesta di autenticazione a più fattori.
2. Usando la nuova rete VPN, passare a [https://myapps.microsoft.com](https://myapps.microsoft.com) e immettere le credenziali dell'account di test.

L'accesso viene visualizzato nel dashboard Identity Protection entro 10-15 minuti.

## <a name="atypical-travel"></a>Trasferimento atipico

La simulazione della condizione di viaggio atipica è difficile perché l'algoritmo usa Machine Learning per eliminare i falsi positivi, ad esempio la corsa atipica da dispositivi noti o gli accessi da VPN usati da altri utenti nella directory. Inoltre, l'algoritmo richiede una cronologia di accesso di 14 giorni e 10 account di accesso dell'utente prima di iniziare la generazione dei rilevamenti dei rischi. Grazie ai modelli di apprendimento automatico complessi e alle regole sopra riportate, è possibile che la procedura seguente non provochi un rilevamento dei rischi. Per simulare questo rilevamento, potrebbe essere necessario replicare questi passaggi per più account Azure AD.

**Per simulare un rilevamento dei rischi di viaggio atipico, seguire questa procedura**:

1. Usando il browser standard, passare a [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Immettere le credenziali dell'account per cui si vuole generare un rilevamento dei rischi di viaggio atipico.
3. Modificare l'agente utente. È possibile modificare l'agente utente in Microsoft Edge da Strumenti di sviluppo (F12).
4. Modificare l'indirizzo IP. È possibile modificare l'indirizzo IP usando una VPN, un componente aggiuntivo Tor o creando una nuova macchina virtuale in Azure in un data center diverso.
5. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com) con le stesse credenziali usate in precedenza ed entro pochi minuti dall'accesso precedente.

L'accesso viene visualizzato nel dashboard Identity Protection entro 2-4 ore.

## <a name="testing-risk-policies"></a>Test di criteri di rischio

Questa sezione illustra i passaggi per testare l'utente e i criteri di rischio di accesso creati nell'articolo [procedura: configurare e abilitare i criteri di rischio](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Criteri di rischio utente

Per testare i criteri di sicurezza per il rischio utente, seguire questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare a **Azure Active Directory** > **sicurezza** > **Panoramica**.
1. Selezionare **Configura criteri di rischio utente**.
   1. In **assegnazioni**
      1. **Utenti** : scegliere **tutti gli utenti** o **selezionare singoli utenti e gruppi** in caso di limitazione dell'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dai criteri.
      1. **Condizioni** - la raccomandazione di Microsoft per il **rischio utente** è quella di impostare questa opzione su **alta**.
   1. Sotto **controlli**
      1. **Accesso** : Microsoft consiglia di **consentire l'accesso** e **richiedere la modifica della password**.
   1. **Applicare i criteri** - **disattivato**
   1. **Salva** : questa azione consente di tornare alla pagina **Panoramica** .
1. Elevare il rischio utente di un account di test, ad esempio, simulando una delle rilevazioni dei rischi alcune volte.
1. Attendere alcuni minuti e quindi verificare che il rischio sia elevato per l'utente. In caso contrario, simulare un maggior numero di rilevamenti di rischio per l'utente.
1. Tornare ai criteri di rischio e impostare **Applica criterio** **su on** e **salvare** la modifica dei criteri.
1. È ora possibile testare l'accesso condizionale basato sul rischio utente eseguendo l'accesso con un utente con un livello di rischio elevato.

### <a name="sign-in-risk-security-policy"></a>Criteri di sicurezza per il rischio di accesso

Per testare i criteri di rischio di accesso, seguire questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare a **Azure Active Directory** > **sicurezza** > **Panoramica**.
1. Selezionare **Configura criteri di rischio di accesso**.
   1. In **assegnazioni**
      1. **Utenti** : scegliere **tutti gli utenti** o **selezionare singoli utenti e gruppi** in caso di limitazione dell'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dai criteri.
      1. **Condizioni** - la raccomandazione Microsoft per il **rischio di accesso** è quella di impostare questa opzione su **media e versioni successive**.
   1. Sotto **controlli**
      1. **Accesso** : Microsoft consiglia di **consentire l'accesso** e **richiedere l'autenticazione**a più fattori.
   1. **Imponi - criteri** **in**
   1. **Salva** : questa azione consente di tornare alla pagina **Panoramica** .
1. È ora possibile testare l'accesso condizionale basato sul rischio di accesso eseguendo l'accesso con una sessione rischiosa (ad esempio, usando il browser Tor). 

## <a name="next-steps"></a>Passaggi successivi

- [Che cosa sono i rischi?](concept-identity-protection-risks.md)

- [Procedura: configurare e abilitare i criteri di rischio](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
