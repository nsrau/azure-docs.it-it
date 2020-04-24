---
title: Che cos'è il Punteggio di sicurezza identità? -Azure Active Directory
description: Come è possibile usare il Punteggio sicuro per l'identità per migliorare il comportamento di sicurezza della directory
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523110"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Informazioni su Identity Secure Score in Azure Active Directory

Quanto è sicuro il proprio tenant di Azure AD? Se non si è in grado di rispondere a questa domanda, in questo articolo viene illustrato come il Punteggio sicuro di identità consente di monitorare e migliorare il comportamento di sicurezza delle identità.

## <a name="what-is-an-identity-secure-score"></a>Che cos'è un punteggio Identity Secure Score?

Il Punteggio di sicurezza identità è un numero compreso tra 1 e 223 che funge da indicatore per l'allineamento con le procedure consigliate di Microsoft per la sicurezza. Ogni azione di miglioramento nel punteggio di identità sicuro è adattata alla configurazione specifica.  

![Punteggio di sicurezza](./media/identity-secure-score/identity-secure-score-overview.png)

Il punteggio consente di:

- Misurare obiettivamente le condizioni di sicurezza delle identità
- Pianificare miglioramenti per la sicurezza delle identità
- Verificare il successo dei miglioramenti apportati

È possibile accedere al punteggio e alle informazioni correlate nel dashboard Identity Secure Score. In questo dashboard sono visibili:

- Punteggio di identità sicuro
- Un grafico di confronto che Mostra come il Punteggio di identità sicuro viene confrontato con altri tenant nello stesso settore e dimensioni simili
- Un grafico di tendenza che Mostra come il Punteggio di identità sicuro è cambiato nel tempo
- Un elenco di possibili miglioramenti

Applicando le azioni di miglioramento, è possibile:

- Migliorare la postura di sicurezza e il Punteggio
- Sfrutta le funzionalità disponibili per la tua organizzazione come parte degli investimenti per le identità

## <a name="how-do-i-get-my-secure-score"></a>Come si ottiene il punteggio di sicurezza?

Il Punteggio di sicurezza identità è disponibile in tutte le edizioni di Azure AD. Le organizzazioni possono accedere al Punteggio sicuro dell'identità dall' **portale di Azure** > **Azure Active Directory** > **Security** > **Punteggio sicuro**per l'identità di sicurezza.

## <a name="how-does-it-work"></a>Come funziona?

Ogni 48 ore, Azure esamina la configurazione di sicurezza e confronta le impostazioni con le raccomandazioni sulle procedure consigliate. In base al risultato della valutazione, viene calcolato un nuovo punteggio per la directory. È possibile che la configurazione di sicurezza non sia completamente allineata con le procedure consigliate e che le azioni di miglioramento siano parzialmente soddisfatte. In questi scenari verrà assegnata solo una parte del punteggio massimo disponibile per il controllo.

Ogni raccomandazione viene misurata in base alla specifica configurazione di Azure AD. Se si utilizzano prodotti di terze parti per consentire una raccomandazione di procedure consigliate, è possibile indicare questa configurazione nelle impostazioni di un'azione di miglioramento. È anche possibile impostare raccomandazioni da ignorare se non si applicano all'ambiente. Una raccomandazione ignorata non influisce sul calcolo del punteggio.

![Ignorare o contrassegnare l'azione come coperta da terze parti](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>A cosa serve?

Il punteggio di sicurezza consente di:

- Misurare obiettivamente le condizioni di sicurezza delle identità
- Pianificare miglioramenti per la sicurezza delle identità
- Verificare il successo dei miglioramenti apportati

## <a name="what-you-should-know"></a>Informazioni utili

### <a name="who-can-use-the-identity-secure-score"></a>Chi può usare Identity Secure Score?

Identity Secure Score può essere usato dai ruoli seguenti:

- Amministratore globale
- Amministratore della sicurezza
- Ruoli con autorizzazioni di lettura per la sicurezza

### <a name="how-are-controls-scored"></a>Come vengono assegnati i controlli?

I controlli possono essere classificati in due modi. Per alcuni viene assegnato un punteggio in modalità binaria: si ottiene il 100% del punteggio se la funzionalità o l'impostazione è configurata in base alle raccomandazioni. Gli altri punteggi vengono calcolati come percentuale della configurazione totale. Se, ad esempio, la raccomandazione di miglioramento indica che si otterranno 30 punti se si proteggono tutti gli utenti con l'autenticazione a più fattori e si dispone solo di 5 di 100 utenti totali protetti, verrà assegnato un punteggio parziale circa 2 punti (5 protected/100 Total * 30 max pts = 2 PTS partial score).

### <a name="what-does-not-scored-mean"></a>Cosa significa [Not Scored] (Non conteggiato)?

Le azioni etichettate come [Not Scored] (Non conteggiato) sono quelle che è possibile eseguire nell'organizzazione, ma non verranno calcolate nell'assegnazione del punteggio perché non ancora disponibili nello strumento. Quindi, è comunque possibile migliorare la sicurezza, ma per il momento non se ne ottengono benefici in termini di punteggio.

### <a name="how-often-is-my-score-updated"></a>Con quale frequenza viene aggiornato il punteggio?

Il punteggio viene calcolato una volta al giorno (circa all'1:00 PST). Se si apporta una modifica a un'azione misurata, il punteggio verrà automaticamente aggiornato il giorno successivo. Perché una modifica si rifletta nel punteggio possono essere necessarie fino a 48 ore.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Da cosa si può capire perché il punteggio è cambiato?

Visita il [Centro sicurezza di Microsoft 365](https://security.microsoft.com/), dove troverai il Punteggio Microsoft Secure completo. È possibile visualizzare facilmente tutte le modifiche apportate al Punteggio sicuro esaminando le modifiche approfondite nella scheda cronologia.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Il Punteggio sicuro misura il rischio di essere violato?

In breve, no. Il Punteggio sicuro non esprime una misura assoluta della probabilità che si ottenga una violazione. Esprime la misura in cui sono state adottate funzionalità che mitigano il rischio di una violazione. Nessun servizio può garantire che non venga violato e che il Punteggio sicuro non venga interpretato come una garanzia in alcun modo.

### <a name="how-should-i-interpret-my-score"></a>Come interpretare il punteggio?

Si ricevono punti quando si configurano funzionalità di sicurezza consigliate o si eseguono attività correlate alla sicurezza, ad esempio la lettura dei report. Per alcune azioni viene calcolato un punteggio per il completamento parziale, come l'abilitazione dell'autenticazione a più fattori (MFA) per gli utenti. Il Punteggio sicuro è direttamente rappresentativo dei servizi di sicurezza Microsoft usati. Tenere presente che la sicurezza deve essere bilanciata con l'usabilità. Tutti i controlli di sicurezza hanno un certo impatto sugli utenti. I controlli con un impatto ridotto non devono avere praticamente alcun effetto sulle operazioni quotidiane degli utenti.

Per visualizzare la cronologia dei punteggi, passare al [Centro sicurezza Microsoft 365](https://security.microsoft.com/) ed esaminare il Punteggio globale Microsoft sicuro. È possibile fare clic su Visualizza cronologia per esaminare le modifiche apportate al Punteggio sicuro generale. Scegliere una data specifica per vedere quali controlli sono stati abilitati per quel giorno e i punti guadagnati per ognuno di essi.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>In che modo il punteggio Identity Secure Score è correlato al punteggio Office 365 Secure Score?

Il [Punteggio Microsoft Secure](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) contiene cinque categorie di controllo e Punteggio distinte:

- Identità
- Data
- Dispositivi
- Infrastruttura
- App

Il Punteggio Identity Secure rappresenta la parte Identity del Punteggio Microsoft Secure. Questa sovrapposizione significa che le raccomandazioni per il Punteggio di sicurezza identità e il Punteggio di identità in Microsoft sono le stesse.

## <a name="next-steps"></a>Passaggi successivi

[Scopri di più su Microsoft Secure Score](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
