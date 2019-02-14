---
title: Informazioni sul punteggio Identity Secure Score - Azure Active Directory (anteprima pubblica) | Microsoft Docs
description: Istruzioni su come usare Identity Secure Score per aumentare la sicurezza del tenant di Azure AD.
services: active-directory
keywords: Identity Secure Score, Azure AD, accesso sicuro alle risorse aziendali
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2018
ms.author: markvi
ms.reviewer: nigu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8617442c811dc2fe8f961038f35ebcf218a58302
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163956"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory-public-preview"></a>Informazioni su Identity Secure Score in Azure Active Directory (anteprima pubblica)

Quanto è sicuro il proprio tenant di Azure AD? Se non si conosce la risposta a questa domanda, leggere questo articolo per informazioni su come usare Identity Secure Score per monitorare e migliorare le condizioni di sicurezza delle identità. 

## <a name="what-is-an-identity-secure-score"></a>Che cos'è un punteggio Identity Secure Score?

Il punteggio Identity Secure Score è un numero compreso tra 1 e 248 che indica il grado di allineamento alle raccomandazioni Microsoft sulle procedure consigliate per la sicurezza.


![Punteggio di sicurezza](./media/identity-secure-score/01.png)



Il punteggio consente di:

- Misurare obiettivamente le condizioni di sicurezza delle identità

- Pianificare miglioramenti per la sicurezza delle identità

- Verificare il successo dei miglioramenti apportati 


È possibile accedere al punteggio e alle informazioni correlate nel dashboard Identity Secure Score. In questo dashboard sono visibili:

- Il punteggio dell'utente

    ![Punteggio di sicurezza](./media/identity-secure-score/02.png)

- Un grafico di confronto

    ![Punteggio di sicurezza](./media/identity-secure-score/03.png)

- Un grafico della tendenza

    ![Punteggio di sicurezza](./media/identity-secure-score/04.png)

- Un elenco di procedure consigliate per la sicurezza delle identità. 

    ![Punteggio di sicurezza](./media/identity-secure-score/05.png)


Applicando le azioni di miglioramento, è possibile:

- Migliorare le condizioni di sicurezza e il punteggio.
 
- Sfruttare i vantaggi delle funzionalità di gestione delle identità di Microsoft. 



## <a name="how-do-i-get-my-secure-score"></a>Come si ottiene il punteggio di sicurezza?

Identity Secure Score è disponibile in tutte le edizioni di Azure Active Directory.

Per accedere al punteggio, passare al [dashboard di panoramica di Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).



## <a name="how-does-it-work"></a>Come funziona?

Ogni 48 ore, Azure esamina la configurazione di sicurezza e confronta le impostazioni con le raccomandazioni sulle procedure consigliate. In base al risultato di questa valutazione, viene calcolato un nuovo punteggio per il tenant. Questo significa che prima che una modifica della configurazione si rifletta nel punteggio possono trascorrere fino a 48 ore. 

Ogni raccomandazione viene misurata in base alla specifica configurazione di Azure AD. Se si usano prodotti di terze parti per applicare una raccomandazione sulle procedure consigliate, è possibile indicarlo nelle impostazioni di un'azione di miglioramento.

![Punteggio di sicurezza](./media/identity-secure-score/07.png)


Inoltre, è possibile impostare le raccomandazioni come da ignorare se non si applicano all'ambiente. Una raccomandazione ignorata non influisce sul calcolo del punteggio. 
 
![Punteggio di sicurezza](./media/identity-secure-score/06.png)



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

### <a name="what-does-not-scored-mean"></a>Cosa significa [Not Scored] (Non conteggiato)?

Le azioni etichettate come [Not Scored] (Non conteggiato) sono quelle che è possibile eseguire nell'organizzazione, ma non verranno calcolate nell'assegnazione del punteggio perché non ancora disponibili nello strumento. Quindi, è comunque possibile migliorare la sicurezza, ma per il momento non se ne ottengono benefici in termini di punteggio.

### <a name="how-often-is-my-score-updated"></a>Con quale frequenza viene aggiornato il punteggio?

Il punteggio viene calcolato una volta al giorno (circa all'1:00 PST). Se si apporta una modifica a un'azione misurata, il punteggio verrà automaticamente aggiornato il giorno successivo. Perché una modifica si rifletta nel punteggio possono essere necessarie fino a 48 ore.


### <a name="my-score-changed-how-do-i-figure-out-why"></a>Da cosa si può capire perché il punteggio è cambiato?

Nella pagina dell'analizzatore del punteggio del [portale di Secure Score](https://securescore.microsoft.com/#!/score), fare clic su un punto dati per un giorno specifico, quindi scorrere verso il basso per visualizzare le azioni completate e incomplete del giorno in questione e vedere cosa è cambiato.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Il punteggio di sicurezza valuta il rischio di violazioni?

In breve, no. Il punteggio di sicurezza non esprime una misura assoluta della probabilità di violazioni del tenant. Esprime la misura in cui sono state adottate funzionalità che mitigano il rischio di una violazione. Nessun servizio può garantire che non avverranno violazioni e il punteggio di sicurezza non deve in alcun modo essere interpretato come una garanzia.

### <a name="how-should-i-interpret-my-score"></a>Come interpretare il punteggio?

Si ricevono punti quando si configurano funzionalità di sicurezza consigliate o si eseguono attività correlate alla sicurezza, ad esempio la lettura dei report. Per alcune azioni viene calcolato un punteggio per il completamento parziale, come l'abilitazione dell'autenticazione a più fattori (MFA) per gli utenti. Il punteggio di sicurezza è direttamente rappresentativo dei servizi di sicurezza Microsoft in uso nell'ambiente. Tenere presente che la sicurezza deve sempre essere bilanciata con la facilità di utilizzo. Tutti i controlli di sicurezza hanno un certo impatto sugli utenti. I controlli con un impatto ridotto non devono avere praticamente alcun effetto sulle operazioni quotidiane degli utenti.

Per visualizzare la cronologia del punteggio, passare alla pagina dell'analizzatore del punteggio nel [portale di Secure Score](https://securescore.microsoft.com/#!/score). Scegliere una data specifica per vedere quali controlli sono stati abilitati per quel giorno e i punti guadagnati per ognuno di essi.


### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>In che modo il punteggio Identity Secure Score è correlato al punteggio Office 365 Secure Score? 

Il punteggio [Office 365 Secure Score](https://docs.microsoft.com/office365/securitycompliance/office-365-secure-score) sta per essere scomposto in cinque diversi punteggi:

- Identità

- Dati

- Dispositivi

- Infrastruttura

- App

Il valore Identity Secure Score rappresenta la parte di Office 365 Secure Score correlata alle identità. Questo significa che le raccomandazioni di Identity Secure Score e quelle per il punteggio Identità in Office 365 Secure Score sono le stesse. 


## <a name="next-steps"></a>Passaggi successivi

Per guardare un video su Office 365 Secure Score, fare clic [qui](https://www.youtube.com/watch?v=jzfpDJ9Kg-A).
 
