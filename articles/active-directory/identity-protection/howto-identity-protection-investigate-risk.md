---
title: Analizzare il rischio Azure Active Directory Identity ProtectionInvestigate risk Azure Active Directory Identity Protection
description: Informazioni su come analizzare utenti, rilevamenti e accessi rischiosi in Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253481"
---
# <a name="how-to-investigate-risk"></a>Procedura: analizzare il rischioHow To: Investigate risk

Identity Protection fornisce alle organizzazioni tre report che possono utilizzare per analizzare i rischi di identità nel proprio ambiente. Questi report sono gli **utenti rischiosi,** **gli invii rischiosi**e **i rilevamenti dei rischi**. L'analisi degli eventi è fondamentale per comprendere meglio e identificare eventuali punti deboli della strategia di sicurezza.

Tutti e tre i rapporti consentono il download di eventi in . Formato CSV per un'ulteriore analisi all'esterno del portale di Azure.CSV format for further analysis outside of the Azure portal. I report rischiosi per gli utenti e gli emissioni di accesso consentono di scaricare le voci 2500 più recenti, mentre il rapporto sui rilevamenti dei rischi consente di scaricare i 5000 record più recenti.

Le organizzazioni possono sfruttare le integrazioni dell'API Microsoft Graph per aggregare i dati con altre origini a cui possono avere accesso come organizzazione.

I tre report si trovano nel **portale** > di Azure**Sicurezza****di Azure Active Directory** > .

## <a name="navigating-the-reports"></a>Esplorazione dei report

Ogni report viene avviato con un elenco di tutti i rilevamenti per il periodo visualizzato nella parte superiore del report. Ogni report consente l'aggiunta o la rimozione di colonne in base alle preferenze dell'amministratore. Gli amministratori possono scegliere di scaricare i dati in . CSV o . Formato JSON. I report possono essere filtrati utilizzando i filtri nella parte superiore del report.

La selezione di singole voci può consentire voci aggiuntive nella parte superiore del report, ad esempio la possibilità di confermare un accesso come compromesso o sicuro, confermare un utente come compromesso o ignorare il rischio per l'utente.

La selezione di singole voci espande una finestra dei dettagli sotto i rilevamenti. La visualizzazione dei dettagli consente agli amministratori di analizzare ed eseguire azioni su ogni rilevamento. 

![Esempio di report sulla protezione dell'identità con gli invii e i dettagli rischiosi](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Utenti a rischio

Con le informazioni fornite dal rapporto degli utenti rischiosi, gli amministratori possono trovare:

- Quali utenti sono a rischio, hanno avuto un rischio recuperato o hanno subito un rischio di licenziamento?
- Dettagli sui rilevamenti
- Cronologia di tutti gli inadementi rischiosi
- Cronologia rischio
 
Gli amministratori possono quindi scegliere di eseguire un'azione su questi eventi. Gli amministratori possono scegliere di:

- Reimpostare la password utente
- Conferma compromissione utente
- Ignorare il rischio utente
- Impedire l'accesso dell'utente
- Analizzare ulteriormente l'uso di Azure ATPInvestigate further using Azure ATP

## <a name="risky-sign-ins"></a>Accessi a rischio

Il report di accesso rischioso contiene dati filtrabili per gli ultimi 30 giorni (1 mese).

Con le informazioni fornite dal report di accesso rischioso, gli amministratori possono trovare:

- Gli invii classificati come a rischio, confermati compromessi, confermati sicuri, licenziati o corretti.
- Livelli di rischio aggregati e in tempo reale associati ai tentativi di accesso.
- Tipi di rilevamento attivati
- Criteri di accesso condizionale applicati
- Dettagli sull'autenticazione a più fattori
- Informazioni sul dispositivo
- Informazioni sull'applicazione
- Informazioni sulla posizione

Gli amministratori possono quindi scegliere di eseguire un'azione su questi eventi. Gli amministratori possono scegliere di:

- Conferma compromissione dell'accesso
- Confermare la sicurezza dell'accesso

## <a name="risk-detections"></a>Rilevamenti dei rischi

Il rapporto sui rilevamenti dei rischi contiene dati filtrabili per gli ultimi 90 giorni (3 mesi).

Con le informazioni fornite dal rapporto sui rilevamenti dei rischi, gli amministratori possono trovare:

- Informazioni su ogni rilevamento dei rischi, incluso il tipo.
- Altri rischi attivati contemporaneamente
- Percorso del tentativo di accesso
- Collegamento a ulteriori dettagli da Microsoft Cloud App Security (MCAS).

Gli amministratori possono quindi scegliere di tornare al report di rischio o di accesso dell'utente per eseguire azioni in base alle informazioni raccolte.

## <a name="next-steps"></a>Passaggi successivi

- [Criteri disponibili per attenuare i rischi](concept-identity-protection-policies.md)

- [Abilitare i criteri di accesso e di rischio per gli utenti](howto-identity-protection-configure-risk-policies.md)
