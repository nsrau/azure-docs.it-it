---
title: Simulazione dei rilevamenti dei rischi in Azure AD Identity Protection
description: Informazioni su come simulare il rilevamento dei rischi in Identity Protection
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886935"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulazione dei rilevamenti dei rischi in Identity Protection

Gli amministratori potrebbero voler simulare i rischi nel proprio ambiente per ottenere i seguenti elementi:

- Popolare i dati nell'ambiente di protezione delle identità simulando rilevamenti di rischio e vulnerabilità.
- Impostare criteri di accesso condizionale basati sui rischi e verificare l'impatto di questi criteri.

In questo articolo vengono forniti i passaggi per simulare i seguenti tipi di rilevamento dei rischi:

- Indirizzo IP anonimo (facile)
- Proprietà di accesso sconosciute (moderato)
- Viaggia atipica (difficile)

Altri rilevamenti dei rischi non possono essere simulati in modo sicuro.

Ulteriori informazioni su ogni rilevamento dei rischi possono essere trovate nell'articolo, [Che cos'è](concept-identity-protection-risks.md)il rischio .

## <a name="anonymous-ip-address"></a>Indirizzo IP anonimo

Per completare la procedura seguente, sono necessari:

- [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) per simulare indirizzi IP anonimi. Potrebbe essere necessario usare una macchina virtuale, se l'organizzazione limita l'uso di Tor Browser.
- Un account di test non ancora registrato per Azure Multi-Factor Authentication.

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
- Azure Multi-Factor Authentication abilitato.

**Per simulare un accesso da una posizione non nota, seguire questa procedura**:

1. Quando si accede con l'account di test, non superare la richiesta di autenticazione a più fattori (MFA) non superando la richiesta di autenticazione a più fattori.
2. Utilizzando la nuova VPN, individuare [https://myapps.microsoft.com](https://myapps.microsoft.com) e immettere le credenziali dell'account di test.

L'accesso viene visualizzato nel dashboard Identity Protection entro 10-15 minuti.

## <a name="atypical-travel"></a>Trasferimento atipico

La simulazione della condizione di viaggio atipica è difficile perché l'algoritmo utilizza l'apprendimento automatico per espellere i falsi positivi, ad esempio i viaggi atipici da dispositivi familiari o gli emissioni di accesso da VPN utilizzati da altri utenti nella directory. Inoltre, l'algoritmo richiede una cronologia di accesso di 14 giorni e 10 accessi dell'utente prima di iniziare a generare rilevamenti dei rischi. A causa dei modelli di apprendimento automatico complessi e delle regole precedenti, è possibile che i passaggi seguenti non portino a un rilevamento dei rischi. È possibile replicare questi passaggi per più account azure AD per simulare questo rilevamento.

**Per simulare un rilevamento atipico dei rischi di viaggio, attenersi alla seguente procedura:**

1. Utilizzando il browser standard, accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Immettere le credenziali dell'account per il quale si desidera generare un rilevamento atipo dei rischi di viaggio.
3. Modificare l'agente utente. È possibile modificare l'agente utente in Microsoft Edge da Strumenti di sviluppo (F12).
4. Modificare l'indirizzo IP. È possibile modificare l'indirizzo IP usando una VPN, un componente aggiuntivo Tor o creando una nuova macchina virtuale in Azure in un data center diverso.
5. Accedere utilizzando [https://myapps.microsoft.com](https://myapps.microsoft.com) le stesse credenziali di prima e pochi minuti dopo l'accesso precedente.

L'accesso viene visualizzato nel dashboard Identity Protection entro 2-4 ore.

## <a name="testing-risk-policies"></a>Test delle politiche di rischio

In questa sezione vengono forniti i passaggi per testare l'utente e i criteri di rischio di accesso creati nell'articolo [Procedura: configurare e abilitare i criteri di rischio](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Criteri di rischio utente

Per testare i criteri di sicurezza per il rischio utente, seguire questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare a**Panoramica****della sicurezza di** >  **Azure Active Directory** > .
1. Selezionare **Configura criteri di rischio utente**.
   1. In **Assegnazioni**
      1. **Utenti:** scegliere **Tutti gli utenti** o Seleziona individui e **gruppi** se si limita l'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dal criterio.
      1. **Condizioni Rischio** - **utente** Raccomandazione da Microsoft è di impostare questa opzione su **Alta**.
   1. In **Controlli**
      1. **Accesso** - Il consiglio di Microsoft è quello di **consentire l'accesso** e richiedere la **modifica della password**.
   1. **Imponi criteri** - **disattivati**
   1. **Salva:** questa azione consente di tornare alla pagina **Panoramica.**
1. Aumentare il rischio per l'utente di un account di test simulando, ad esempio, uno dei rilevamenti dei rischi alcune volte.
1. Attendere alcuni minuti e quindi verificare che il rischio sia elevato per l'utente. In caso contrario, simulare più rilevamenti dei rischi per l'utente.
1. Tornare ai criteri di rischio e impostare **Applica criteri** su **Attivato** e **Salva** modifica dei criteri.
1. È ora possibile testare l'accesso condizionale basato sul rischio dell'utente eseguendo l'accesso utilizzando un utente con un livello di rischio elevato.

### <a name="sign-in-risk-security-policy"></a>Criteri di sicurezza per il rischio di accesso

Per testare i criteri di rischio di accesso, seguire questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare a**Panoramica****della sicurezza di** >  **Azure Active Directory** > .
1. Selezionare **Configura criteri di rischio di accesso**.
   1. In **Assegnazioni**
      1. **Utenti:** scegliere **Tutti gli utenti** o Seleziona individui e **gruppi** se si limita l'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dal criterio.
      1. **Condizioni** - **di rischio di accesso** Consiglia a Microsoft è di impostare questa opzione su Medio e **superiore**.
   1. In **Controlli**
      1. **Accesso** - Si consiglia a Microsoft di **consentire l'accesso** e **richiedere l'autenticazione**a più fattori .
   1. **Applica criteri** - **su**
   1. **Salva:** questa azione consente di tornare alla pagina **Panoramica.**
1. È ora possibile testare l'accesso condizionale basato sul rischio di accesso eseguendo l'accesso utilizzando una sessione rischiosa, ad esempio tramite il browser Tor. 

## <a name="next-steps"></a>Passaggi successivi

- [Cos'è il rischio?](concept-identity-protection-risks.md)

- [Procedura: Configurare e abilitare i criteri di rischioHow To: Configure and enable risk policies](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
