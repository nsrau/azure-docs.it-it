---
title: Le password escluse dinamicamente - Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263998"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminare le password non appropriate nell'organizzazione

I leader del settore ti dicono di non usare la stessa password in più luoghi, di renderla complessa e di non renderla semplice come "Password123". In che modo le organizzazioni possono garantire che gli utenti seguano le indicazioni sulle procedure consigliate? Come possono assicurarsi che gli utenti non utilizzino password deboli o anche variazioni sulle password deboli?

Il passaggio iniziale per avere password più complesse consiste nel fornire indicazioni agli utenti. Le indicazioni correnti di Microsoft su questo argomento sono disponibili nella pagina Web a cui si accede tramite il collegamento seguente:

[Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance) (Indicazioni di Microsoft sulle password)

Avere una buona guida è importante, ma anche con questo sappiamo che molti utenti finiranno comunque per scegliere password deboli. Azure AD Password Protection protegge l'organizzazione rilevando e bloccando le password deboli note e le relative varianti, nonché bloccando facoltativamente termini deboli aggiuntivi specifici per l'organizzazione.

Per altre informazioni sulle attività volte a garantire la sicurezza, vedere il [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Elenco globale di password escluse

Il team di Azure AD Identity Protection analizza costantemente i dati di telemetria della sicurezza di Azure AD alla ricerca di password deboli o compromesse comunemente usate o, più specificamente, dei termini di base deboli che spesso vengono usati come base per password deboli. Quando vengono trovati questi termini deboli, vengono aggiunti all'elenco globale delle password escluse. Il contenuto dell'elenco globale delle password non vietate non è basato su alcuna origine dati esterna. L'elenco globale delle password non vietate si basa interamente sui risultati in corso della telemetria e dell'analisi di sicurezza di Azure AD.

Ogni volta che una nuova password viene modificata o reimpostata per qualsiasi utente in qualsiasi tenant in Azure AD, la versione corrente dell'elenco globale delle password non vietate viene usata come input chiave per la convalida della complessità della password. Questa convalida comporta password molto più complesse per tutti i clienti di Azure AD.

> [!NOTE]
> I cyber-criminali utilizzano anche strategie simili nei loro attacchi. Pertanto Microsoft non pubblica pubblica pubblica pubblicamente il contenuto di questo elenco.

## <a name="custom-banned-password-list"></a>Elenco password personalizzate escluse

Alcune organizzazioni potrebbero voler migliorare ulteriormente la sicurezza aggiungendo le proprie personalizzazioni in cima all'elenco globale delle password vietate in quello che Microsoft chiama l'elenco personalizzato delle password vietate. Microsoft consiglia che i termini aggiunti a questo elenco si concentrino principalmente su termini specifici dell'organizzazione, ad esempio:

- Marchi
- Nomi dei prodotti
- Sedi (ad esempio, ad esempio la sede centrale dell'azienda)
- Termini interni specifici dell'azienda
- Abbreviazioni che hanno un significato specifico della società.

Una volta che i termini vengono aggiunti all'elenco personalizzato delle password escluse, verranno combinati con i termini nell'elenco globale delle password escluse durante la convalida delle password.

> [!NOTE]
> L'elenco personalizzato delle password escluse è limitato a un massimo di 1000 termini. Non è progettato per bloccare elenchi estremamente grandi di password. Per sfruttare appieno i vantaggi dell'elenco personalizzato delle password escluse, Microsoft consiglia di esaminare e comprendere l'algoritmo di valutazione delle password (vedere [Come vengono valutate le password)](concept-password-ban-bad.md#how-are-passwords-evaluated)prima di aggiungere nuovi termini all'elenco degli argomenti non crittografati personalizzati. Comprendere il funzionamento dell'algoritmo consentirà all'azienda di rilevare e bloccare in modo efficiente un numero elevato di password deboli e le relative varianti.

Si consideri, ad esempio: un cliente denominato "Contoso", con sede a Londra e che crea un prodotto denominato "Widget". Per un tale cliente, sarebbe dispendioso e meno sicuro cercare di bloccare variazioni specifiche di questi termini come:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso"
- "LondraHQ"
- ... Eccetera

Invece, è molto più efficiente e sicuro per bloccare solo i termini di base chiave:

- "Contoso"
- "Londra"
- "Widget"

L'algoritmo di convalida della password bloccherà automaticamente le varianti deboli e le combinazioni di quanto sopra.

L'elenco personalizzato di password escluse e la possibilità di abilitare l'integrazione di Active Directory locale vengono gestiti tramite il portale di Azure.

![Modificare l'elenco personalizzato delle password non vietate in Metodi di autenticazione](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Attacchi di spray password ed elenchi di password compromesse di terze parti

Uno dei principali vantaggi della protezione con password di Azure AD consiste nell'aiutarti a difenderti dagli attacchi di spray password. La maggior parte degli attacchi di spray per le password non tenta di attaccare un determinato account individuale più di un paio di volte poiché tale comportamento aumenta notevolmente la probabilità di rilevamento, tramite il blocco dell'account o altri mezzi. La maggior parte degli attacchi di spray password si basa quindi sull'invio solo di un piccolo numero di password più deboli conosciute contro ciascuno degli account in un'azienda. Questa tecnica consente all'utente malintenzionato di cercare rapidamente un account facilmente compromesso evitando allo stesso tempo potenziali soglie di rilevamento.

La protezione con password di Azure AD è progettata per bloccare in modo efficiente tutte le password deboli note che potrebbero essere usate negli attacchi di spruzzo di password, in base ai dati di telemetria di sicurezza reali visualizzati da Azure AD.  Microsoft è a conoscenza di siti Web di terze parti che enumerano milioni di password che sono state compromesse in precedenti violazioni della sicurezza pubblicamente note. È comune che i prodotti di convalida delle password di terze parti siano basati sul confronto della forza bruta con quei milioni di password. Microsoft ritiene che tali tecniche non sono il modo migliore per migliorare la complessità complessiva della password date le strategie tipiche utilizzate dagli aggressori spray password.

> [!NOTE]
> L'elenco globale delle password non aggiornate di Microsoft non si basa su alcuna origine dati di terze parti, inclusi gli elenchi di password compromesse.

Anche se l'elenco globale di sicurezza di Microsoft è piccolo rispetto ad alcuni elenchi di massa di terze parti, i suoi effetti di sicurezza sono amplificati dal fatto che è originato da dati di telemetria di sicurezza del mondo reale su attacchi di spruzzo di password effettivi, oltre al fatto che il Microsoft algoritmo di convalida della password utilizza tecniche di corrispondenza fuzzy intelligente. Il risultato finale è che rileverà e bloccherà in modo efficiente milioni di password deboli più comuni da utilizzare nella tua azienda. Anche i clienti che scelgono di aggiungere termini specifici dell'organizzazione all'elenco di password escluse personalizzato traggono vantaggio dallo stesso algoritmo.

Ulteriori informazioni sui problemi di sicurezza basati su password possono essere esaminate presso [il tuo nuovo $word non ha importanza.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

## <a name="on-premises-hybrid-scenarios"></a>Scenari ibridi locali

La protezione degli account solo cloud è utile, ma molte organizzazioni gestiscono scenari ibridi, incluso Windows Server Active Directory locale. I vantaggi in termini di sicurezza della protezione con password di Azure AD possono essere estesi anche nell'ambiente Active Directory di Windows Server tramite l'installazione di agenti locali. Ora gli utenti e gli amministratori che modificano o reimpostano le password in Active Directory devono rispettare gli stessi criteri password degli utenti solo cloud.

## <a name="how-are-passwords-evaluated"></a>Come vengono valutate le password

Ogni volta che un utente modifica o reimposta la password, viene verificata la complessità della nuova password convalidandola in base all'elenco combinato di termini degli elenchi globali e personalizzati delle password escluse (se quest'ultima è configurata).

Anche se la password di un utente contiene una password esclusa, è possibile che venga comunque accettata se la password complessiva è sufficientemente complessa. Per una password appena configurata sono previsti i passaggi seguenti per valutarne il livello di complessità generale e determinare se può essere accettata o meno.

### <a name="step-1-normalization"></a>Passaggio 1: NormalizzazioneStep 1: Normalization

Per prima cosa, una nuova password viene sottoposta a un processo di normalizzazione, Questa tecnica consente di mappare un piccolo set di password vietate a un insieme molto più ampio di password potenzialmente deboli.

La normalizzazione si articola in due parti.  Nella prima fase, tutte le lettere maiuscole vengono convertite in lettere minuscole,  mentre nella seconda viene eseguita la sostituzione dei caratteri, ad esempio:  

| Lettera originale  | Lettera sostituita |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

Esempio: si supponga che la password "blank" sia compresa tra le password escluse e che un utente tenti di modificarla in "Bl@nK". Anche se "Bl@nk" non è espressamente vietata, il processo di normalizzazione converte questa password in "blank", ovvero in una password esclusa.

### <a name="step-2-check-if-password-is-considered-banned"></a>Passaggio 2: controlla se la password è considerata bannata

#### <a name="fuzzy-matching-behavior"></a>Comportamento della corrispondenza fuzzy

Sulla password normalizzata viene applicata la corrispondenza fuzzy per verificare che non contenga una password presente nell'elenco globale o nell'elenco personalizzato di password escluse. Il processo di corrispondenza è basato su una distanza di edit di un (1) confronto.  

Esempio: si supponga che la password "abcdef" sia compresa tra le password escluse e che un utente tenti di modificarla in una delle password seguenti:

'abcdeg' *(ultimo carattere modificato da 'f' a 'g')* 'abcdefg' *'(g' aggiunto a fine)* 'abcde' *(la 'f' finale è stata eliminata dalla fine)*

Nessuna delle password precedenti corrisponde esattamente alla password esclusa "abcdef", Tuttavia, poiché ogni esempio rientra in una distanza di modifica di 1 del termine bandito "abcdef", sono tutti considerati come una corrispondenza con "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Corrispondenza delle sottostringhe (su termini specifici)

Sulla password normalizzata viene applicata la corrispondenza delle sottostringhe per verificare il nome e il cognome dell'utente e il nome del tenant (la corrispondenza del nome del tenant non viene applicata quando si convalida una password in un controller di dominio di Active Directory).

Esempio: supponiamo di avere un utente, Pol, che vuole reimpostare la password su "P0l123fb". Dopo la normalizzazione, questa password diventerà "pol123fb". La corrispondenza della sottostringa trova che la password contiene il nome dell'utente "Pol". Anche se "P0l123fb" non era specificamente presente nell'elenco delle password vietate, la corrispondenza delle sottostringhe ha trovato "Pol" nella password. che viene quindi rifiutata.

#### <a name="score-calculation"></a>Calcolo del punteggio

Il passaggio successivo consiste nell'identificare le eventuali istanze di password escluse presenti nella nuova password normalizzata dell'utente. Quindi:

1. A ogni password vietata presente nella password di un utente viene assegnato un punto,
2. così come a ogni carattere univoco rimanente.
3. Una password deve essere di almeno cinque (5) punti per essere accettata.

Nei due esempi successivi, si supponga che Contoso usi la protezione delle password di Azure AD e che "contoso" si trovi nell'elenco personalizzato. Si supponga inoltre che nell'elenco globale sia presente "blank".

Esempio: un utente modifica la password in "C0ntos0Blank12"

Dopo la normalizzazione, la password diventa "contosoblank12". Il processo di corrispondenza rileva che la password contiene due password escluse: contoso e blank. Alla password viene quindi assegnato un punteggio:

[contoso] - [vuoto] - [1] - [2] - 4 punti Poiché la password è inferiore a cinque (5) punti, verrà rifiutata.

Esempio: un utente modifica la password in "ContoS0Bl@nkf9!".

Dopo la normalizzazione, la password diventa "contosoblankf9!". Il processo di corrispondenza rileva che la password contiene due password escluse: contoso e blank. Alla password viene quindi assegnato un punteggio:

[contoso] - [vuoto] - [f] , [9] , [!] - 5 punti Poiché questa password è di almeno cinque (5) punti, è accettata.

   > [!IMPORTANT]
   > L'algoritmo delle password escluse e l'elenco globale possono cambiare in qualsiasi momento in Azure, in base ai processi di ricerca e di analisi di sicurezza in corso. Per il servizio agente controller di dominio locale, gli algoritmi aggiornati avranno effetto solo dopo il reinstallazione del software dell'agente controller di dominio.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

|   | Protezione delle password di Azure AD con elenco globale di password escluse | Protezione delle password di Azure AD con elenco password personalizzate escluse|
| --- | --- | --- |
| Solo utenti cloud | Azure AD Free | Azure AD Premium P1 o P2 |
| Utenti sincronizzati da Windows Server Active Directory locale | Azure AD Premium P1 o P2 | Azure AD Premium P1 o P2 |

> [!NOTE]
> Gli utenti locali di Windows Server Active Directory che non sono sincronizzati con Azure Active Directory beneficiano anche della protezione con password di Azure AD in base alle licenze esistenti per gli utenti sincronizzati.

Informazioni aggiuntive sulle licenze, inclusi i costi, sono disponibili nella pagina [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Quando un utente cerca di reimpostare una password usando una stringa che verrà esclusa, viene visualizzato il messaggio di errore seguente:

La password contiene una parola o una frase o segue uno schema che la rende facile da indovinare. Riprovare con una password diversa.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'elenco personalizzato password escluse](howto-password-ban-bad.md)
- [Abilitare gli agenti di protezione password di Azure AD in locale](howto-password-ban-bad-on-premises-deploy.md)
