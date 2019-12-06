---
title: Password escluse in modo dinamico-Azure Active Directory
description: Escludere le password vulnerabili dall'ambiente con le password escluse in modo dinamico di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848613"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminare le password non appropriate nell'organizzazione

I leader del settore dicono di non usare la stessa password in più punti, per renderla complessa e non renderla semplice come "Password123". In che modo le organizzazioni possono garantire che i loro utenti stiano seguendo le procedure consigliate? In che modo è possibile assicurarsi che gli utenti non utilizzino password vulnerabili o persino varianti di password vulnerabili?

Il passaggio iniziale per avere una password più avanzata consiste nel fornire indicazioni agli utenti. Le indicazioni correnti di Microsoft su questo argomento sono disponibili nella pagina Web a cui si accede tramite il collegamento seguente:

[Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance) (Indicazioni di Microsoft sulle password)

Una guida di qualità è importante, ma anche con il fatto che molti utenti continueranno a scegliere password vulnerabili. Azure AD la protezione con password protegge l'organizzazione rilevando e bloccando le password vulnerabili note e le relative varianti, nonché bloccando facoltativamente ulteriori termini vulnerabili specifici per l'organizzazione.

Per altre informazioni sulle attività volte a garantire la sicurezza, vedere il [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Elenco globale di password escluse

Il team di Azure AD Identity Protection analizza costantemente i dati di telemetria della sicurezza di Azure AD alla ricerca di password vulnerabili o compromesse usate comunemente, o più specificamente, i termini di base deboli che spesso vengono usati come base per le password vulnerabili. Quando vengono trovati tali termini, questi vengono aggiunti all'elenco globale delle password escluse. Il contenuto dell'elenco Global Banned password non è basato su un'origine dati esterna. L'elenco globale delle password escluse si basa interamente sui risultati in corso di Azure AD la telemetria e l'analisi della sicurezza.

Ogni volta che una nuova password viene modificata o reimpostata per tutti gli utenti di qualsiasi tenant in Azure AD, la versione corrente dell'elenco di password Global Banned viene utilizzata come input della chiave per la convalida dell'attendibilità della password. Questa convalida produce password molto più complesse per tutti i clienti Azure AD.

> [!NOTE]
> I criminali informatici usano anche strategie analoghe negli attacchi. Microsoft non pubblica pertanto il contenuto di questo elenco.

## <a name="custom-banned-password-list"></a>Elenco password personalizzate escluse

Alcune organizzazioni potrebbero voler migliorare ulteriormente la protezione aggiungendo le proprie personalizzazioni all'elenco globale delle password escluse in quello che Microsoft chiama l'elenco delle password escluse da quelle personalizzate. Microsoft consiglia che i termini aggiunti a questo elenco siano incentrati principalmente su termini specifici dell'organizzazione, ad esempio:

- Nomi dei marchi
- Nomi dei prodotti
- Località, ad esempio la sede centrale dell'azienda.
- Termini interni specifici della società
- Abbreviazioni con un significato specifico per la società.

Quando i termini vengono aggiunti all'elenco delle password con divieto personalizzato, verranno combinati con i termini nell'elenco Global Banned password durante la convalida delle password.

> [!NOTE]
> L'elenco delle password con divieto personalizzato è limitato a un massimo di 1000 termini. Non è progettato per il blocco di elenchi di password estremamente grandi. Per sfruttare appieno i vantaggi dell'elenco delle password con Banned personalizzato, Microsoft consiglia di esaminare e comprendere prima di tutto l'algoritmo di valutazione delle password (vedere [come vengono valutate le password](concept-password-ban-bad.md#how-are-passwords-evaluated)) prima di aggiungere nuovi termini all'elenco dei dati esclusi. La comprensione del funzionamento dell'algoritmo consente all'azienda di rilevare e bloccare in modo efficiente un numero elevato di password vulnerabili e le relative varianti.

Ad esempio, si consideri un cliente denominato "contoso", che si basa a Londra, e che rende un prodotto denominato "widget". Per un cliente di questo tipo, sarebbe uno spreco e meno sicuro provare a bloccare varianti specifiche di questi termini, ad esempio:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ... eccetera

Al contrario, è molto più efficiente e sicuro bloccare solo i termini di base della chiave:

- "Contoso"
- Londra
- Widget

L'algoritmo di convalida della password bloccherà automaticamente le varianti e le combinazioni vulnerabili del precedente.

L'elenco personalizzato di password escluse e la possibilità di abilitare l'integrazione di Active Directory locale vengono gestiti tramite il portale di Azure.

![Modificare l'elenco di password vietate personalizzate in metodi di autenticazione](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Attacchi spray per la password e elenchi di password compromesse di terze parti

Una chiave Azure AD vantaggio per la protezione delle password è aiutare a difendersi dagli attacchi di spray per le password. La maggior parte degli attacchi spray per le password non tenta di attaccare un determinato account singolo più volte poiché tale comportamento aumenta significativamente la probabilità di rilevamento, tramite il blocco degli account o altri mezzi. La maggior parte degli attacchi spray per le password si basa quindi sull'invio di un numero ridotto di password più vulnerabili note a ogni account di un'azienda. Questa tecnica consente all'autore dell'attacco di cercare rapidamente un account facilmente compromesso, evitando allo stesso tempo soglie di rilevamento potenziali.

Azure AD la protezione con password è progettata per bloccare in modo efficiente tutte le password vulnerabili note che probabilmente verranno usate negli attacchi di spray per le password, in base ai dati di telemetria di sicurezza reali, come illustrato da Azure AD.  Microsoft è a conoscenza dei siti Web di terze parti che enumerano milioni di password compromesse nelle precedenti violazioni della sicurezza note pubblicamente. È comune che i prodotti di convalida della password di terze parti siano basati sul confronto forza bruta rispetto a questi milioni di password. Microsoft ritiene che tali tecniche non siano il modo migliore per migliorare la complessità complessiva delle password in base alle strategie tipiche usate dagli utenti malintenzionati di spray per le password.

> [!NOTE]
> L'elenco delle password Global Banned Microsoft non è basato su alcuna origine dati di terze parti, inclusi gli elenchi di password compromesse.

Anche se l'elenco Microsoft Global Banned è ridotto rispetto ad alcuni elenchi bulk di terze parti, i relativi effetti sulla sicurezza sono amplificati dal fatto che sono originati da dati di telemetria di sicurezza reali sugli effettivi attacchi di tipo password, oltre al fatto che Microsoft l'algoritmo di convalida della password utilizza tecniche di corrispondenza fuzzy. Il risultato finale è che consente di rilevare e bloccare in modo efficiente milioni di password vulnerabili più comuni nell'azienda. I clienti che scelgono di aggiungere termini specifici dell'organizzazione all'elenco delle password con divieto personalizzato traggono vantaggio anche dallo stesso algoritmo.

Altre informazioni sui problemi di sicurezza basati su password possono essere esaminate nella pagina [PA $ $Word non è rilevante](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

## <a name="on-premises-hybrid-scenarios"></a>Scenari ibridi locali

La protezione degli account solo cloud è utile, ma molte organizzazioni gestiscono scenari ibridi, incluso Windows Server Active Directory locale. I vantaggi di sicurezza di Azure AD Password Protection possono anche essere estesi nell'ambiente Windows Server Active Directory tramite l'installazione di agenti locali. Gli utenti e gli amministratori che modificano o reimpostano le password in Active Directory devono ora rispettare gli stessi criteri password degli utenti solo cloud.

## <a name="how-are-passwords-evaluated"></a>Come vengono valutate le password

Ogni volta che un utente modifica o Reimposta la password, la nuova password viene verificata in base alla complessità e alla complessità convalidando l'elenco combinato dei termini degli elenchi delle password globali e personalizzate (se quest'ultimo è configurato).

Anche se la password di un utente contiene una password esclusa, è possibile che venga comunque accettata se la password complessiva è sufficientemente complessa. Per una password appena configurata sono previsti i passaggi seguenti per valutarne il livello di complessità generale e determinare se può essere accettata o meno.

### <a name="step-1-normalization"></a>Passaggio 1: normalizzazione

Per prima cosa, una nuova password viene sottoposta a un processo di normalizzazione, Questa tecnica consente di eseguire il mapping di un piccolo set di password escluse a un set molto più ampio di password potenzialmente vulnerabili.

La normalizzazione si articola in due parti.  Nella prima fase, tutte le lettere maiuscole vengono convertite in lettere minuscole,  mentre nella seconda viene eseguita la sostituzione dei caratteri, ad esempio:  

| Lettera originale  | Lettera sostituita |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

Esempio: si supponga che la password "blank" sia compresa tra le password escluse e che un utente tenti di modificarla in "Bl@nK". Anche se "Bl@nk" non è espressamente vietata, il processo di normalizzazione converte questa password in "blank", ovvero in una password esclusa.

### <a name="step-2-check-if-password-is-considered-banned"></a>Passaggio 2: verificare se la password è considerata vietata

#### <a name="fuzzy-matching-behavior"></a>Comportamento della corrispondenza fuzzy

Sulla password normalizzata viene applicata la corrispondenza fuzzy per verificare che non contenga una password presente nell'elenco globale o nell'elenco personalizzato di password escluse. Il processo di corrispondenza è basato su una distanza di edit di un (1) confronto.  

Esempio: si supponga che la password "abcdef" sia compresa tra le password escluse e che un utente tenti di modificarla in una delle password seguenti:

' abcdeg ' *(ultimo carattere modificato da' f ' a' g ')* ' abcdefg ' *' (g ' aggiunto alla fine)* ' abcde ' *(la fine di ' f ' è stata eliminata dall'estremità)*

Nessuna delle password precedenti corrisponde esattamente alla password esclusa "abcdef", Tuttavia, poiché ogni esempio si trova all'interno di una distanza di modifica di 1 del termine ' abcdef ' vietato, vengono tutti considerati come una corrispondenza con "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Corrispondenza delle sottostringhe (su termini specifici)

Sulla password normalizzata viene applicata la corrispondenza delle sottostringhe per verificare il nome e il cognome dell'utente e il nome del tenant (la corrispondenza del nome del tenant non viene applicata quando si convalida una password in un controller di dominio di Active Directory).

Esempio: si supponga di avere un utente, pol, che vuole reimpostare la password su "P0l123fb". Dopo la normalizzazione, questa password diventerebbe "pol123fb". La corrispondenza della sottostringa trova che la password contiene il nome utente "Pol". Anche se "P0l123fb" non è stato specificamente presente nell'elenco delle password escluse, è stata trovata una corrispondenza di sottostringa "Pol" nella password. che viene quindi rifiutata.

#### <a name="score-calculation"></a>Calcolo del punteggio

Il passaggio successivo consiste nell'identificare le eventuali istanze di password escluse presenti nella nuova password normalizzata dell'utente. Quindi:

1. A ogni password vietata presente nella password di un utente viene assegnato un punto,
2. così come a ogni carattere univoco rimanente.
3. Una password deve essere di almeno cinque (5) punti per poter essere accettata.

Nei due esempi successivi, si supponga che Contoso usi la protezione delle password di Azure AD e che "contoso" si trovi nell'elenco personalizzato. Si supponga inoltre che nell'elenco globale sia presente "blank".

Esempio: un utente modifica la password in "C0ntos0Blank12"

Dopo la normalizzazione, la password diventa "contosoblank12". Il processo di corrispondenza rileva che la password contiene due password escluse: contoso e blank. Alla password viene quindi assegnato un punteggio:

[Contoso] + [blank] + [1] + [2] = 4 punti poiché la password è inferiore a cinque (5) punti, verrà rifiutata.

Esempio: un utente modifica la password in "ContoS0Bl@nkf9!".

Dopo la normalizzazione, la password diventa "contosoblankf9!". Il processo di corrispondenza rileva che la password contiene due password escluse: contoso e blank. Alla password viene quindi assegnato un punteggio:

[Contoso] + [blank] + [f] + [9] + [!] = 5 punti perché questa password è almeno cinque (5) punti, viene accettata.

   > [!IMPORTANT]
   > L'algoritmo delle password escluse e l'elenco globale possono cambiare in qualsiasi momento in Azure, in base ai processi di ricerca e di analisi di sicurezza in corso. Per il servizio agente di controller di dominio locale, gli algoritmi aggiornati diverranno effettive solo dopo la reinstallazione del software dell'agente controller di dominio.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

|   | Protezione delle password di Azure AD con elenco globale di password escluse | Protezione delle password di Azure AD con elenco password personalizzate escluse|
| --- | --- | --- |
| Solo utenti cloud | Azure AD Free | Azure AD Premium P1 o P2 |
| Utenti sincronizzati da Windows Server Active Directory locale | Azure AD Premium P1 o P2 | Azure AD Premium P1 o P2 |

> [!NOTE]
> Gli utenti di Windows Server Active Directory locali che non sono sincronizzati con Azure Active Directory beneficiano anche della protezione Azure AD password basata sulle licenze esistenti per gli utenti sincronizzati.

Informazioni aggiuntive sulle licenze, inclusi i costi, sono disponibili nella pagina [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Quando un utente cerca di reimpostare una password usando una stringa che verrà esclusa, viene visualizzato il messaggio di errore seguente:

La password contiene una parola o una frase o segue uno schema che la rende facile da indovinare. Riprovare con una password diversa.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'elenco personalizzato di password escluse](howto-password-ban-bad.md)
- [Abilitare gli agenti di protezione password di Azure AD in locale](howto-password-ban-bad-on-premises-deploy.md)
