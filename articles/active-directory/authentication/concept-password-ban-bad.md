---
title: Vietate in modo dinamico le password - Azure Active Directory
description: Escludere le password vulnerabili dall'ambiente con le password escluse in modo dinamico di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c043b2ed1a626e362d7edd1a83429aa14046f8ac
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703066"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminare le password non appropriate nell'organizzazione

Leader del settore indicano di non utilizzare la stessa password in più posizioni, per renderli complessi e per non renderla un'azione semplice come "Password123". In che modo le organizzazioni possono garantisce che gli utenti seguono procedure consigliate? Come può è assicurarsi che gli utenti non usano password vulnerabili, o persino le variazioni nella password vulnerabili?

Il passaggio iniziale in presenza di password più complesse è fornire indicazioni agli utenti. Le indicazioni correnti di Microsoft su questo argomento sono disponibili nella pagina Web a cui si accede tramite il collegamento seguente:

[Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance) (Indicazioni di Microsoft sulle password)

Materiale sussidiario buona è importante, ma anche con che sappiamo che molti utenti finirà comunque la scelta di password vulnerabili. Protezione di Password di AD Azure consente di proteggere l'organizzazione da rilevare e bloccare password vulnerabili note e le relative varianti, nonché se lo si desidera bloccare condizioni debole aggiuntive che sono specifiche dell'organizzazione.

Per altre informazioni sulle attività volte a garantire la sicurezza, vedere il [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Elenco globale di password escluse

Il team di Azure AD Identity Protection analizza costantemente i dati di telemetria di Azure AD della protezione dati cercando valori per le password usate comunemente debole o compromesse o, in particolare, il "Weak" basare termini che vengono spesso utilizzati come base per le password vulnerabili. Quando vengono rilevati tali condizioni debole, viene aggiunto all'elenco globale delle password da escludere. Il contenuto dell'elenco globale delle password vietate non basato su qualsiasi origine dati esterna. L'elenco globale delle password vietate è basato interamente sui risultati in corso di analisi e telemetria di sicurezza di Azure AD.

Quando una nuova password viene modificata o reimpostata per tutti gli utenti in qualsiasi tenant di Azure AD, la versione corrente dell'elenco globale delle password vietate viene usata come chiave di input durante la convalida la complessità della password. Questa convalida comporta password molto più complesse per tutti i clienti di Azure AD.

> [!NOTE]
> Inoltre, criminali informatici usano strategie simili nei propri attacchi. Di conseguenza Microsoft non pubblica il contenuto di questo elenco pubblicamente.

## <a name="custom-banned-password-list"></a>Elenco password personalizzate escluse

Alcune organizzazioni potrebbe essere necessario migliorare ulteriormente la protezione aggiungendo le proprie personalizzazioni sopra l'elenco globale delle password vietate in ciò che Microsoft chiama l'elenco di password vietate personalizzato. Microsoft consiglia che le condizioni aggiunte a questo elenco sono principalmente interessate in base alle esigenze aziendali specifiche, ad esempio:

- Nomi di marchi
- Nomi di prodotto
- Percorsi (ad esempio, ad esempio, sede centrale dell'azienda)
- Termini interni specifico della società
- Abbreviazioni specifici sono vale a dire.

Una volta termini vengono aggiunti all'elenco personalizzato di password vietate, verranno aggiunti all'elenco globale delle password da escludere durante la convalida delle password.

> [!NOTE]
> L'elenco di password vietate personalizzato è limitato alla presenza di un massimo di termini di 1000. Non è progettato per bloccare gli elenchi di dimensioni estremamente grandi di password. Per sfruttare completamente i vantaggi dell'elenco personalizzato di password vietate, Microsoft consiglia di rivedere innanzitutto e comprendere l'algoritmo di valutazione della password (vedere [modo in cui vengono valutate le password](concept-password-ban-bad.md#how-are-passwords-evaluated)) prima di aggiungere nuove condizioni per il elenco personalizzato da escludere. Informazioni sul modo in cui il funzionamento dell'algoritmo consentirà alle aziende di rilevare e bloccare un numero elevato di password vulnerabili e dalle relative varianti in modo efficiente.

Ad esempio: prendere in considerazione un cliente denominato "Contoso", che si basa a Londra e che rende un prodotto denominato "Widget". Per un cliente, sarebbe dispendioso, nonché meno sicura per tentare di bloccare le variazioni specifiche di questi termini, ad esempio:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ...etcetera

In alternativa, è molto più efficiente e sicuro per bloccare solo il chiave base termini:

- "Contoso"
- "Londra"
- "Widget"

L'algoritmo di convalida delle password quindi blocca automaticamente le varianti debole e combinazioni di quello precedente.

L'elenco personalizzato di password escluse e la possibilità di abilitare l'integrazione di Active Directory locale vengono gestiti tramite il portale di Azure.

![Modificare l'elenco personalizzato di password vietate in metodi di autenticazione](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Password spray attacchi e gli elenchi delle password compromesso di terze parti

Una chiave vantaggio di protezione di Azure AD password è di difesa contro gli attacchi spray password. La maggior parte degli attacchi spray non tentano di attacco qualsiasi account singoli specificato più di alcune volte poiché tale comportamento aumenta notevolmente le probabilità di rilevamento, tramite il blocco degli account o altri mezzi. La maggior parte degli attacchi spray password pertanto si basano sull'invio solo un numero ridotto delle password note più debole rispetto a ogni account in un'organizzazione. Questa tecnica consente all'utente malintenzionato di cercare rapidamente un account compromesso facilmente mentre nello stesso momento evitare potenziali valori di soglia di rilevamento.

Protezione tramite password di Azure AD è progettato per bloccare in modo efficiente tutte le password vulnerabili note che possono essere usati negli attacchi spray password basati sui dati di telemetria reali sicurezza come illustrato da Azure AD.  Microsoft è a conoscenza dei siti Web di terze parti che enumerano milioni di password che sono stati compromessi di precedenti violazioni della sicurezza noti pubblicamente. È comune per i prodotti di terze parti password convalida deve essere basato sul confronto di attacchi di forza bruta con milioni di password. Microsoft ritiene che queste tecniche non sono il modo migliore per migliorare la complessità della password globale assegnato strategie più comuni usate da utenti malintenzionati spray password.

> [!NOTE]
> Il Microsoft elenco globale delle password vietate non è in alcun modo su tutti i dati di terze parti le origini, inclusi gli elenchi delle password compromessa.

Anche se l'elenco da escludere globale di Microsoft è ridotta rispetto ad alcuni elenchi di operazioni bulk di terze parti, gli effetti di sicurezza sono amplificati dal fatto che viene originato dai dati di telemetria di protezione effettivi in attacchi spray password effettiva, oltre al fatto che Microsoft algoritmo di convalida delle password Usa tecniche di corrispondenza fuzzy intelligente. Il risultato finale è che in modo efficiente rileverà e bloccare milioni delle password vulnerabili più comune perché non venga usato nell'azienda. I clienti che scelgono di aggiungere le condizioni specifiche dell'organizzazione all'elenco di password vietate personalizzati possono beneficiare lo stesso algoritmo.

## <a name="on-premises-hybrid-scenarios"></a>Scenari ibridi locali

La protezione degli account solo cloud è utile, ma molte organizzazioni gestiscono scenari ibridi, incluso Windows Server Active Directory locale. È possibile installare Azure AD password di protezione per Windows Server Active Directory gli agenti in locale estendere gli elenchi delle password vietate nell'infrastruttura esistente. In questo caso, utenti e amministratori che modificano, impostano o reimpostano le password in locale devono rispettare gli stessi criteri password degli utenti solo cloud.

## <a name="how-are-passwords-evaluated"></a>Come vengono valutate le password

Ogni volta che un utente modifica o reimposta la password, viene verificata la complessità della nuova password confrontandola rispetto all'elenco globale e all'elenco personalizzato di password escluse (se configurati).

Anche se la password di un utente contiene una password esclusa, è possibile che venga comunque accettata se la password complessiva è sufficientemente complessa. Per una password appena configurata sono previsti i passaggi seguenti per valutarne il livello di complessità generale e determinare se può essere accettata o meno.

### <a name="step-1-normalization"></a>Passaggio 1: Normalizzazione

Per prima cosa, una nuova password viene sottoposta a un processo di normalizzazione, Questa tecnica consente un piccolo set di password da escludere per eseguire il mapping a un set molto più ampio di potenzialmente vulnerabili.

La normalizzazione si articola in due parti.  Nella prima fase, tutte le lettere maiuscole vengono convertite in lettere minuscole,  mentre nella seconda viene eseguita la sostituzione dei caratteri, ad esempio:  

| Lettera originale  | Lettera sostituita |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

Esempio: si supponga che la password "blank" sia compresa tra le password escluse e che un utente tenti di modificarla in "Bl@nK". Anche se "Bl@nk" non è espressamente vietata, il processo di normalizzazione converte questa password in "blank", ovvero in una password esclusa.

### <a name="step-2-check-if-password-is-considered-banned"></a>Passaggio 2: Verifica che la password non sia considerata esclusa

#### <a name="fuzzy-matching-behavior"></a>Comportamento della corrispondenza fuzzy

Sulla password normalizzata viene applicata la corrispondenza fuzzy per verificare che non contenga una password presente nell'elenco globale o nell'elenco personalizzato di password escluse. Il processo di corrispondenza è basato su una distanza di edit di un (1) confronto.  

Esempio: si supponga che la password "abcdef" sia compresa tra le password escluse e che un utente tenti di modificarla in una delle password seguenti:

'abcdeg'    * (ultimo carattere modificato da 'f' a 'g')* 'abcdefg'   * '(g' aggiunto alla fine)* 'abcde'     * ('f' finale eliminata)*

Nessuna delle password precedenti corrisponde esattamente alla password esclusa "abcdef", Tuttavia, poiché ogni esempio è all'interno di una distanza di modifica 1 del termine da escludere 'abcdef', questi sono tutti considerati come una corrispondenza per "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Corrispondenza delle sottostringhe (su termini specifici)

Sulla password normalizzata viene applicata la corrispondenza delle sottostringhe per verificare il nome e il cognome dell'utente e il nome del tenant (la corrispondenza del nome del tenant non viene applicata quando si convalida una password in un controller di dominio di Active Directory).

Esempio: si supponga che abbiamo un utente, Pol, chi desidera reimpostare la password per "P0l123fb". Dopo la normalizzazione, questa password diverrebbe "pol123fb". Corrispondenza nelle sottostringhe rileva che la password contiene il nome dell'utente prima "Pol". Anche se "P0l123fb" non è stata specificamente su entrambi gli elenchi delle password vietate, sottostringa corrispondente trovato "Pol" nella password. che viene quindi rifiutata.

#### <a name="score-calculation"></a>Calcolo del punteggio

Il passaggio successivo consiste nell'identificare le eventuali istanze di password escluse presenti nella nuova password normalizzata dell'utente. Quindi:

1. A ogni password vietata presente nella password di un utente viene assegnato un punto,
2. così come a ogni carattere univoco rimanente.
3. Una password deve contenere almeno cinque (5) punti per poter essere accettata.

Nei due esempi successivi, si supponga che Contoso usi la protezione delle password di Azure AD e che "contoso" si trovi nell'elenco personalizzato. Si supponga inoltre che nell'elenco globale sia presente "blank".

Esempio: un utente modifica la password in "C0ntos0Blank12"

Dopo la normalizzazione, la password diventa "contosoblank12". Il processo di corrispondenza rileva che la password contiene due password escluse: contoso e blank. Alla password viene quindi assegnato un punteggio:

[contoso] + [vuota] + [1] + [2] = 4 punti poiché questa password è punti in cinque (5), verranno rifiutata.

Esempio: un utente modifica la password in "ContoS0Bl@nkf9!".

Dopo la normalizzazione, la password diventa "contosoblankf9!". Il processo di corrispondenza rileva che la password contiene due password escluse: contoso e blank. Alla password viene quindi assegnato un punteggio:

[contoso] + [vuota] + [f] + [9] + [!] = 5 punti poiché questa password è almeno cinque (5) punti, viene accettato.

   > [!IMPORTANT]
   > L'algoritmo delle password escluse e l'elenco globale possono cambiare in qualsiasi momento in Azure, in base ai processi di ricerca e di analisi di sicurezza in corso. Per il servizio agente controller di dominio locale, gli algoritmi aggiornati diventerà effettiva solo dopo aver reinstallato il software dell'agente controller di dominio.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

|   | Protezione delle password di Azure AD con elenco globale di password escluse | Protezione delle password di Azure AD con elenco password personalizzate escluse|
| --- | --- | --- |
| Solo utenti cloud | Azure AD Free | Azure AD Premium P1 o P2 |
| Utenti sincronizzati da Windows Server Active Directory locale | Azure AD Premium P1 o P2 | Azure AD Premium P1 o P2 |

> [!NOTE]
> Gli utenti di Windows Server Active Directory locale che non sincronizzati con Azure Active Directory anche sfruttare i vantaggi della protezione tramite password di Azure AD basato sulla concessione di licenze esistente per gli utenti sincronizzati.

Informazioni aggiuntive sulle licenze, inclusi i costi, sono disponibili nella pagina [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Quando un utente cerca di reimpostare una password usando una stringa che verrà esclusa, viene visualizzato il messaggio di errore seguente:

La password contiene una parola o una frase o segue uno schema che la rende facile da indovinare. Riprovare con una password diversa.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'elenco personalizzato di password escluse](howto-password-ban-bad.md)
- [Abilitare gli agenti di protezione password di Azure AD in locale](howto-password-ban-bad-on-premises-deploy.md)
