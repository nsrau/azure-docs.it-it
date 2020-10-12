---
title: Analizzare i rischi in Azure Active Directory Identity Protection
description: Informazioni su come analizzare utenti, rilevamenti e accessi a rischio in Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3610fa5ea442e5ccb15255d8a1ddcee211871aeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84463944"
---
# <a name="how-to-investigate-risk"></a>Procedura: Analizzare gli eventi di rischio

Identity Protection offre alle organizzazioni tre report che possono usare per analizzare i rischi relativi alle identità nell'ambiente in uso. Questi report sono **Utenti a rischio**, **Accessi a rischio** e **Rilevamento di rischi**. L'analisi degli eventi è fondamentale per comprendere meglio e identificare eventuali punti deboli nella strategia di sicurezza.

Tutti e tre i report consentono il download degli eventi in formato CSV per un'ulteriore analisi all'esterno del portale di Azure. I report utenti a rischio e accessi a rischio consentono di scaricare le 2.500 voci più recenti, mentre il report sui rilevamenti di rischi consente di scaricare i 5.000 record più recenti.

Le organizzazioni possono sfruttare i vantaggi offerti dalle integrazioni dell'API Microsoft Graph per aggregare i dati con altre origini a cui possono accedere come organizzazione.

I tre report sono disponibili nel **portale di Azure** > **Azure Active Directory** > **Sicurezza**.

## <a name="navigating-the-reports"></a>Esplorazione dei report

Ogni report viene avviato con un elenco di tutti i rilevamenti relativi al periodo indicato nella parte superiore del report. Ogni report consente l'aggiunta o la rimozione di colonne in base alle preferenze dell'amministratore. Gli amministratori possono scegliere di scaricare i dati in formato CSV o JSON. I report possono essere filtrati usando i filtri disponibili nella parte superiore del report.

La selezione di singole voci può abilitare voci aggiuntive nella parte superiore del report, ad esempio la possibilità di confermare un accesso come compromesso o sicuro, confermare un utente come compromesso oppure ignorare il rischio utente.

La selezione di singole voci espande una finestra dei dettagli sotto i rilevamenti. La visualizzazione dei dettagli consente agli amministratori di analizzare ed eseguire azioni su ogni rilevamento. 

![Report di Identity Protection di esempio che mostra gli accessi a rischio e i dettagli](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Utenti a rischio

Con le informazioni contenute nel report utenti a rischio gli amministratori possono trovare:

- Quali utenti sono a rischio, per quali utenti sono stati risolti i rischi o sono stati ignorati i rischi
- Dettagli sui rilevamenti
- Cronologia di tutti gli accessi a rischio
- Cronologia rischio
 
Gli amministratori possono quindi scegliere di intervenire su questi eventi. E possono scegliere di:

- Reimpostare la password dell'utente
- Confermare la compromissione dell'utente
- Ignorare il rischio utente
- Impedire all'utente di accedere
- Eseguire ulteriori analisi usando Azure ATP

## <a name="risky-sign-ins"></a>Accessi a rischio

Il report degli accessi a rischio contiene dati filtrabili per gli ultimi 30 giorni (1 mese).

Con le informazioni contenute nel report accessi a rischio gli amministratori possono trovare:

- Quali accessi sono classificati come a rischio, quali sono stati confermati come compromessi, quali sono stati confermati come sicuri, quali sono stati ignorati o corretti.
- Livelli di rischio in tempo reale e aggregati associati ai tentativi di accesso.
- Tipi di rilevamento attivati
- Criteri di accesso condizionale applicati
- Dettagli di MFA
- Informazioni sul dispositivo
- Informazioni sull'applicazione
- Informazioni sulla posizione

Gli amministratori possono quindi scegliere di intervenire su questi eventi. E possono scegliere di:

- Confermare l'accesso compromesso
- Confermare l'accesso sicuro

> [!NOTE] 
> Identity Protection valuta il rischio per tutti i flussi di autenticazione, sia interattivi che non interattivi. Tuttavia, il report degli accessi mostra solo gli accessi interattivi. È possibile che vengano visualizzati accessi a rischio che si sono verificati in accessi non interattivi, ma l'accesso non viene visualizzato nel report degli accessi di Azure AD.

## <a name="risk-detections"></a>Rilevamenti dei rischi

Il report dei rilevamenti di rischi contiene dati filtrabili per gli ultimi 90 giorni (3 mesi).

Con le informazioni contenute nel report rilevamenti di rischi gli amministratori possono trovare:

- Informazioni su ogni rilevamento di rischi, incluso il tipo.
- Altri rischi attivati contemporaneamente
- Posizione del tentativo di accesso
- Collegamento a informazioni dettagliate in Microsoft Cloud App Security (MCAS).

Gli amministratori possono quindi scegliere di tornare al report utenti a rischio o accessi a rischio per eseguire azioni in base alle informazioni raccolte.

## <a name="next-steps"></a>Passaggi successivi

- [Criteri disponibili per attenuare i rischi](concept-identity-protection-policies.md)

- [Abilitare i criteri di rischio per accessi e utenti](howto-identity-protection-configure-risk-policies.md)
