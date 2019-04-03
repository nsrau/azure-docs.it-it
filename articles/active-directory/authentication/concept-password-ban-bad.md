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
ms.openlocfilehash: cc2c125df2e3455b0e90919dbca92fe497a4b1b7
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884347"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminare le password non appropriate nell'organizzazione

Leader del settore suggeriscono di non usare la stessa password in più posizioni e di usare password complesse e non semplici come Password123. In che modo le organizzazioni possono garantire che gli utenti seguano queste linee guida? Come possono assicurarsi che gli utenti non usino password comuni o password coinvolte in violazioni dei dati avvenute di recente?

## <a name="global-banned-password-list"></a>Elenco globale di password escluse

Microsoft cerca sempre di anticipare le mosse dei criminali informatici. Il team di Azure AD Identity Protection è pertanto continuamente alla ricerca di password usate comunemente e compromesse. Queste password ritenute troppo comuni vengono quindi bloccate attraverso un elenco globale di password escluse. I criminali informatici usano strategie simili negli attacchi, quindi Microsoft non rende disponibile il contenuto di questo elenco pubblicamente. Queste password vulnerabili vengono bloccate prima di diventare una minaccia reale per i clienti Microsoft. Per altre informazioni sulle attività volte a garantire la sicurezza, vedere il [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="custom-banned-password-list"></a>Elenco password personalizzate escluse

Alcune organizzazioni potrebbero voler aumentare ulteriormente la sicurezza personalizzando l'elenco globale di password escluse tramite l'aggiunta di voci all'elenco personalizzato di password escluse Microsoft. Clienti aziendali come Contoso possono quindi scegliere di bloccare le varianti del nome dell'organizzazione, termini aziendali specifici o altre voci.

L'elenco personalizzato di password escluse e la possibilità di abilitare l'integrazione di Active Directory locale vengono gestiti tramite il portale di Azure.

![Modificare l'elenco personalizzato di password vietate in metodi di autenticazione](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Scenari ibridi locali

La protezione degli account solo cloud è utile, ma molte organizzazioni gestiscono scenari ibridi, incluso Windows Server Active Directory locale. È possibile installare Azure AD password di protezione per Windows Server Active Directory gli agenti in locale estendere gli elenchi delle password vietate nell'infrastruttura esistente. In questo caso, utenti e amministratori che modificano, impostano o reimpostano le password in locale devono rispettare gli stessi criteri password degli utenti solo cloud.

## <a name="how-are-passwords-evaluated"></a>Come vengono valutate le password

Ogni volta che un utente modifica o reimposta la password, viene verificata la complessità della nuova password confrontandola rispetto all'elenco globale e all'elenco personalizzato di password escluse (se configurati).

Anche se la password di un utente contiene una password esclusa, è possibile che venga comunque accettata se la password complessiva è sufficientemente complessa. Per una password appena configurata sono previsti i passaggi seguenti per valutarne il livello di complessità generale e determinare se può essere accettata o meno.

### <a name="step-1-normalization"></a>Passaggio 1: Normalizzazione

Per prima cosa, una nuova password viene sottoposta a un processo di normalizzazione, in cui viene eseguito il mapping di un piccolo set di password escluse a un set più ampio di password potenzialmente vulnerabili.

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

Nessuna delle password precedenti corrisponde esattamente alla password esclusa "abcdef", ma poiché si trovano tutte a un distanza di edit di 1 rispetto al token escluso 'abcdef', sono tutte considerate come una corrispondenza di "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Corrispondenza delle sottostringhe (su termini specifici)

Sulla password normalizzata viene applicata la corrispondenza delle sottostringhe per verificare il nome e il cognome dell'utente e il nome del tenant (la corrispondenza del nome del tenant non viene applicata quando si convalida una password in un controller di dominio di Active Directory).

Esempio: si supponga che un utente di nome John Doe voglia reimpostare la password su "J0hn123fb". Dopo la normalizzazione, la password diventerebbe "john123fb". La corrispondenza delle sottostringhe rileva che la password contiene il nome dell'utente "John". Anche se "J0hn123fb" non è contenuto in modo specifico negli elenchi delle password escluse, la corrispondenza delle sottostringhe ha identificato il termine "John" nella password, che viene quindi rifiutata.

#### <a name="score-calculation"></a>Calcolo del punteggio

Il passaggio successivo consiste nell'identificare le eventuali istanze di password escluse presenti nella nuova password normalizzata dell'utente. Quindi:

1. A ogni password vietata presente nella password di un utente viene assegnato un punto,
2. così come a ogni carattere univoco rimanente.
3. Una password deve contenere almeno 5 punti per poter essere accettata.

Nei due esempi successivi, si supponga che Contoso usi la protezione delle password di Azure AD e che "contoso" si trovi nell'elenco personalizzato. Si supponga inoltre che nell'elenco globale sia presente "blank".

Esempio: un utente modifica la password in "C0ntos0Blank12"

Dopo la normalizzazione, la password diventa "contosoblank12". Il processo di corrispondenza rileva che la password contiene due password escluse: contoso e blank. Alla password viene quindi assegnato un punteggio:

[contoso] + [vuota] + [1] + [2] = 4 punti poiché questa password è in 5 punti, verranno rifiutata.

Esempio: un utente modifica la password in "ContoS0Bl@nkf9!".

Dopo la normalizzazione, la password diventa "contosoblankf9!". Il processo di corrispondenza rileva che la password contiene due password escluse: contoso e blank. Alla password viene quindi assegnato un punteggio:

[contoso] + [blank] + [f] + [9] + [!] = 5 punti. Il punteggio della password è pari a 5 punti e la password viene quindi accettata.

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

* [Configurare l'elenco personalizzato password escluse](howto-password-ban-bad.md)
* [Abilitare Azure AD password protection agenti locali](howto-password-ban-bad-on-premises-deploy.md)
