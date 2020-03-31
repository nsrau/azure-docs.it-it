---
title: Che cos'è il punteggio di identità? - Azure Active Directory
description: Come è possibile utilizzare il punteggio di identità sicuro per migliorare la sicurezza della directory
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

Quanto è sicuro il proprio tenant di Azure AD? Se non sai come rispondere a questa domanda, questo articolo spiega come il punteggio di identità sicuro ti aiuta a monitorare e migliorare la tua sicurezza di identità.

## <a name="what-is-an-identity-secure-score"></a>Che cos'è un punteggio Identity Secure Score?

Il punteggio di sicurezza dell'identità è il numero compreso tra 1 e 223 che funge da indicatore per l'allineamento con le procedure consigliate di Microsoft per la sicurezza. Ogni azione di miglioramento nel punteggio di sicurezza dell'identità è adattata alla configurazione specifica.  

![Punteggio di sicurezza](./media/identity-secure-score/identity-secure-score-overview.png)

Il punteggio consente di:

- Misurare obiettivamente le condizioni di sicurezza delle identità
- Pianificare miglioramenti per la sicurezza delle identità
- Verificare il successo dei miglioramenti apportati

È possibile accedere al punteggio e alle informazioni correlate nel dashboard Identity Secure Score. In questo dashboard sono visibili:

- Il tuo punteggio di identità sicuro
- Un grafico di confronto che mostra il confronto tra il punteggio di sicurezza Identity e altri tenant dello stesso settore e dimensioni simili
- Un grafico delle tendenze che mostra come il tuo punteggio di sicurezza Identity è cambiato nel tempo
- Un elenco di possibili miglioramenti

Applicando le azioni di miglioramento, è possibile:

- Migliora la tua sicurezza e il tuo punteggio
- Sfrutta le funzionalità disponibili per la tua organizzazione nell'ambito dei tuoi investimenti di identità

## <a name="how-do-i-get-my-secure-score"></a>Come si ottiene il punteggio di sicurezza?

Il punteggio di identità sicura è disponibile in tutte le edizioni di Azure AD. Le organizzazioni possono accedere al punteggio di sicurezza dell'identità dal**Security** >  **punteggio** > di sicurezza dell'identità del portale di Azure**Azure Active Directory.** > **Identity Secure Score**

## <a name="how-does-it-work"></a>Come funziona?

Ogni 48 ore, Azure esamina la configurazione di sicurezza e confronta le impostazioni con le raccomandazioni sulle procedure consigliate. In base all'esito di questa valutazione, viene calcolato un nuovo punteggio per la directory. È possibile che la configurazione della sicurezza non sia completamente allineata con le indicazioni sulle procedure consigliate e che le azioni di miglioramento siano soddisfatte solo parzialmente. In questi scenari, ti verrà assegnata solo una parte del punteggio massimo disponibile per il controllo.

Ogni raccomandazione viene misurata in base alla specifica configurazione di Azure AD. Se si utilizzano prodotti di terze parti per abilitare una raccomandazione di procedura consigliata, è possibile indicare questa configurazione nelle impostazioni di un'azione di miglioramento. È inoltre possibile impostare i suggerimenti da ignorare se non si applicano all'ambiente. Una raccomandazione ignorata non influisce sul calcolo del punteggio.

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

### <a name="how-are-controls-scored"></a>Come vengono segnati i controlli?

I controlli possono essere segnati in due modi. Alcuni sono segnati in modo binario - si ottiene 100% del punteggio se si dispone della funzione o impostazione configurata in base alla nostra raccomandazione. Gli altri punteggi vengono calcolati come percentuale della configurazione totale. Ad esempio, se la raccomandazione di miglioramento indica che si otterranno 30 punti se si proteggono tutti gli utenti con l'autenticazione a più fattori e si dispone solo di 5 degli utenti totali protetti, verrà assegnato un punteggio parziale di circa 2 punti (5 protetto / 100 totale , 30 pts max , 2 pts punteggio parziale).

### <a name="what-does-not-scored-mean"></a>Cosa significa [Not Scored] (Non conteggiato)?

Le azioni etichettate come [Not Scored] (Non conteggiato) sono quelle che è possibile eseguire nell'organizzazione, ma non verranno calcolate nell'assegnazione del punteggio perché non ancora disponibili nello strumento. Quindi, è comunque possibile migliorare la sicurezza, ma per il momento non se ne ottengono benefici in termini di punteggio.

### <a name="how-often-is-my-score-updated"></a>Con quale frequenza viene aggiornato il punteggio?

Il punteggio viene calcolato una volta al giorno (circa all'1:00 PST). Se si apporta una modifica a un'azione misurata, il punteggio verrà automaticamente aggiornato il giorno successivo. Perché una modifica si rifletta nel punteggio possono essere necessarie fino a 48 ore.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Da cosa si può capire perché il punteggio è cambiato?

Visita il [centro sicurezza Microsoft 365,](https://security.microsoft.com/)dove troverai il tuo punteggio sicuro completo di Microsoft. È possibile visualizzare facilmente tutte le modifiche alla partitura sicura esaminando le modifiche approfondite nella scheda Cronologia.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Il punteggio sicuro misura il mio rischio di violazione?

In breve, no. Il punteggio sicuro non esprime una misura assoluta di quanto è probabile che si per ottenere violato. Esprime la misura in cui sono state adottate funzionalità che mitigano il rischio di una violazione. Nessun servizio può garantire che non sarai violato e il punteggio sicuro non deve essere interpretato come una garanzia in alcun modo.

### <a name="how-should-i-interpret-my-score"></a>Come interpretare il punteggio?

Si ricevono punti quando si configurano funzionalità di sicurezza consigliate o si eseguono attività correlate alla sicurezza, ad esempio la lettura dei report. Per alcune azioni viene calcolato un punteggio per il completamento parziale, come l'abilitazione dell'autenticazione a più fattori (MFA) per gli utenti. Il punteggio sicuro è direttamente rappresentativo dei servizi di sicurezza Microsoft utilizzati. Ricorda che la sicurezza deve essere bilanciata con l'usabilità. Tutti i controlli di sicurezza hanno un certo impatto sugli utenti. I controlli con un impatto ridotto non devono avere praticamente alcun effetto sulle operazioni quotidiane degli utenti.

Per visualizzare la cronologia dei punteggi, visita il [Centro sicurezza Microsoft 365](https://security.microsoft.com/) ed esamina il punteggio sicuro complessivo di Microsoft. È possibile rivedere le modifiche apportate al punteggio sicuro complessivo facendo clic su Visualizza cronologia. Scegliere una data specifica per vedere quali controlli sono stati abilitati per quel giorno e i punti guadagnati per ognuno di essi.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>In che modo il punteggio Identity Secure Score è correlato al punteggio Office 365 Secure Score?

Il [punteggio sicuro Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) contiene cinque categorie di controllo e punteggio distinte:

- Identità
- Dati
- Dispositivi
- Infrastruttura
- App

Il punteggio di identità sicuro rappresenta la parte di identità del punteggio di protezione Microsoft.The identity secure score represents the identity part of the Microsoft secure score. Questa sovrapposizione significa che i consigli per il punteggio di identità sicuro e il punteggio di identità in Microsoft sono gli stessi.

## <a name="next-steps"></a>Passaggi successivi

[Ulteriori informazioni su Microsoft secure Score](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
