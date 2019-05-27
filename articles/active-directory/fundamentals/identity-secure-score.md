---
title: Che cos'è il punteggio sicuro di identità? - Azure Active Directory
description: Come è possibile utilizzare il punteggio sicuro di identità per aumentare la sicurezza della directory
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988709"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Informazioni su Identity Secure Score in Azure Active Directory

Quanto è sicuro il proprio tenant di Azure AD? Se non si sa come rispondere a questa domanda, questo articolo illustra come il punteggio sicuro di identità consente di monitorare e migliorare le condizioni di sicurezza di identità.

## <a name="what-is-an-identity-secure-score"></a>Che cos'è un punteggio Identity Secure Score?

Il punteggio sicuro di identità è compreso tra 1 e 223 che funziona come un indicatore per come allineati con cui si hanno le procedure consigliate di Microsoft per sicurezza. Ogni azione miglioramento punteggio sicuro di identità personalizzata in base a una configurazione specifica.  

![Punteggio di sicurezza](./media/identity-secure-score/identity-secure-score-overview.png)

Il punteggio consente di:

- Misurare obiettivamente le condizioni di sicurezza delle identità
- Pianificare miglioramenti per la sicurezza delle identità
- Verificare il successo dei miglioramenti apportati

È possibile accedere al punteggio e alle informazioni correlate nel dashboard Identity Secure Score. In questo dashboard sono visibili:

- Il punteggio sicuro di identità
- Un grafico di confronto che mostra come la tua identità secure score confronta con altri tenant nella stessa del settore e dimensioni simili
- Un grafico di tendenza che mostra come il punteggio di sicuro di identità è cambiato nel tempo
- Un elenco di possibili miglioramenti da apportare

Applicando le azioni di miglioramento, è possibile:

- Migliorare le condizioni di sicurezza e il punteggio relativo alla
- Sfrutta le funzionalità disponibili per l'organizzazione nell'ambito dei propri investimenti di identità

## <a name="how-do-i-get-my-secure-score"></a>Come si ottiene il punteggio di sicurezza?

Il punteggio sicuro di identità è disponibile in tutte le edizioni di Azure Active Directory. Per accedere al punteggio, passare al [dashboard di panoramica di Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).

## <a name="how-does-it-work"></a>Come funziona?

Ogni 48 ore, Azure esamina la configurazione di sicurezza e confronta le impostazioni con le raccomandazioni sulle procedure consigliate. In base al risultato di questa versione di valutazione, un nuovo punteggio viene calcolato per la directory. È possibile che la configurazione di sicurezza non è completamente allineata con le procedure consigliate e le azioni di analisi utilizzo software vengono soddisfatte solo parzialmente. In questi scenari, si assegnerà solo in una parte del punteggio di massima disponibile per il controllo.

Ogni raccomandazione viene misurata in base alla specifica configurazione di Azure AD. Se si siano utilizzando i prodotti di terze parti per consentire una procedura consigliata, è possibile indicare questa configurazione nelle impostazioni di un'azione di analisi utilizzo software. È anche possibile impostare le raccomandazioni da ignorare se non si applicano all'ambiente. Una raccomandazione ignorata non influisce sul calcolo del punteggio.

![Ignorare o contrassegnare azione come coperto da terze parti](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

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

### <a name="how-are-controls-scored"></a>Modo in cui sono assegnati dei punteggi controlli?

I controlli possono essere classificati in due modi. Alcuni sono assegnati dei punteggi in modo binario - otterrai 100% del punteggio se si dispone della funzionalità o impostazione configurata in base un Consiglio. Altri punteggi vengono calcolati come percentuale della configurazione del totale. Ad esempio, se l'indicazione di miglioramento indicato si ottengono punti 30 è proteggere tutti gli utenti con autenticazione a più fattori e avere solo 5 da 100 utenti totali protetti, si sarebbe un punteggio parziale circa 2 di quanto accade (5 protetti / totale di 100 * 30 pts max = 2 punteggio parziale pts) .

### <a name="what-does-not-scored-mean"></a>Cosa significa [Not Scored] (Non conteggiato)?

Le azioni etichettate come [Not Scored] (Non conteggiato) sono quelle che è possibile eseguire nell'organizzazione, ma non verranno calcolate nell'assegnazione del punteggio perché non ancora disponibili nello strumento. Quindi, è comunque possibile migliorare la sicurezza, ma per il momento non se ne ottengono benefici in termini di punteggio.

### <a name="how-often-is-my-score-updated"></a>Con quale frequenza viene aggiornato il punteggio?

Il punteggio viene calcolato una volta al giorno (circa all'1:00 PST). Se si apporta una modifica a un'azione misurata, il punteggio verrà automaticamente aggiornato il giorno successivo. Perché una modifica si rifletta nel punteggio possono essere necessarie fino a 48 ore.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Da cosa si può capire perché il punteggio è cambiato?

Visitare il [Centro sicurezza di Microsoft 365](https://security.microsoft.com/), in cui è disponibile il punteggio di sicuro Microsoft completato. È possibile visualizzare facilmente tutte le modifiche a proprio punteggio sicuro esaminando le modifiche dettagliate nella scheda Cronologia.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Il punteggio sicuro misura il rischio di introduzione violati?

In breve, no. Il punteggio sicuro non è da considerarsi una misura assoluta del modo in cui probabilmente devono ottenere violato. Esprime la misura in cui sono state adottate funzionalità che mitigano il rischio di una violazione. Nessun servizio può garantire che sarà non violare il punteggio sicuro non deve essere interpretato come una garanzia in alcun modo.

### <a name="how-should-i-interpret-my-score"></a>Come interpretare il punteggio?

Si ricevono punti quando si configurano funzionalità di sicurezza consigliate o si eseguono attività correlate alla sicurezza, ad esempio la lettura dei report. Per alcune azioni viene calcolato un punteggio per il completamento parziale, come l'abilitazione dell'autenticazione a più fattori (MFA) per gli utenti. Il punteggio relativo alla protezione direttamente è rappresentativo dei servizi di sicurezza Microsoft che è utilizzare. Tenere presente che sicurezza deve essere bilanciata con facilità di utilizzo. Tutti i controlli di sicurezza hanno un certo impatto sugli utenti. I controlli con un impatto ridotto non devono avere praticamente alcun effetto sulle operazioni quotidiane degli utenti.

Per visualizzare la cronologia di punteggio, vedere la [Centro sicurezza di Microsoft 365](https://security.microsoft.com/) ed esaminare il punteggio di sicuro Microsoft complessivo. È possibile esaminare le modifiche apportate a complessivo delle punteggio sicura da facendo clic su Visualizza cronologia. Scegliere una data specifica per vedere quali controlli sono stati abilitati per quel giorno e i punti guadagnati per ognuno di essi.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>In che modo il punteggio Identity Secure Score è correlato al punteggio Office 365 Secure Score?

Il [Microsoft secure score](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) contiene cinque categorie di punteggio e distinta controllo:

- Identità
- Dati
- Dispositivi
- Infrastruttura
- App

Il punteggio di sicuro identità rappresenta la parte relativa all'identità del punteggio protetto di Microsoft. Questa sovrapposizione significa che le raccomandazioni per l'identità secure score e il punteggio di identità di Microsoft è gli stessi.

## <a name="next-steps"></a>Passaggi successivi

[Scopri di più sul punteggio protetto Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
