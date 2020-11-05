---
title: Protezione con password in Azure Active Directory
description: Informazioni su come vietare dinamicamente le password vulnerabili dall'ambiente con Azure Active Directory la protezione delle password
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4da1066166a3384ffb5f0f94599452829faed1b
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356587"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>Eliminare le password non valide usando la protezione Azure Active Directory password

Molte linee guida sulla sicurezza consigliano di non usare la stessa password in più punti, per renderla complessa ed evitare semplici password come *Password123*. È possibile fornire agli utenti [informazioni aggiuntive su come scegliere le password](https://www.microsoft.com/research/publication/password-guidance), ma le password vulnerabili o non sicure vengono spesso usate. Azure AD Password Protection rileva e blocca le password vulnerabili note e le relative varianti e può anche bloccare ulteriori termini vulnerabili specifici per l'organizzazione.

Con Azure AD la protezione con password, gli elenchi di password Global Banned globali vengono applicati automaticamente a tutti gli utenti in un tenant di Azure AD. Per supportare le esigenze aziendali e di sicurezza, è possibile definire le voci in un elenco di password vietate personalizzate. Quando gli utenti modificano o reimpostano le password, vengono controllati gli elenchi delle password vietate per imporre l'utilizzo di password complesse.

È consigliabile usare funzionalità aggiuntive come [Azure multi-factor authentication](concept-mfa-howitworks.md), non solo basarsi sulle password complesse applicate da Azure ad la protezione delle password. Per altre informazioni sull'uso di più livelli di sicurezza per gli eventi di accesso, vedere la pagina relativa a [PA $ $Word non è rilevante](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

> [!IMPORTANT]
> Questo articolo concettuale spiega a un amministratore come funziona Azure AD la protezione delle password. Se si è un utente finale già registrato per la reimpostazione della password self-service ed è necessario tornare al proprio account, passare a [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Se il team IT non ha abilitato la funzionalità per reimpostare la propria password, rivolgersi al supporto tecnico per assistenza aggiuntiva.

## <a name="global-banned-password-list"></a>Elenco globale di password escluse

Il team di Azure AD Identity Protection analizza costantemente i dati di telemetria della sicurezza di Azure AD alla ricerca di password vulnerabili o compromessa comunemente utilizzate. In particolare, l'analisi cerca i termini di base che spesso vengono usati come base per le password vulnerabili. Quando vengono rilevati termini vulnerabili, vengono aggiunti all' *elenco globale delle password escluse*. Il contenuto dell'elenco Global Banned password non è basato su un'origine dati esterna, ma sui risultati di Azure AD la telemetria e l'analisi della sicurezza.

Quando una password viene modificata o reimpostata per tutti gli utenti di un tenant di Azure AD, per convalidare l'attendibilità della password viene usata la versione corrente dell'elenco di password Global Banned. Questo controllo di convalida comporta password più sicure per tutti i clienti Azure AD.

L'elenco delle password Global Banned viene applicato automaticamente a tutti gli utenti di un tenant di Azure AD. Non c'è niente da abilitare o configurare e non può essere disabilitato. Questo elenco di password Global Banned viene applicato agli utenti quando modificano o reimpostano la propria password tramite Azure AD.

> [!NOTE]
> I criminali informatici usano anche strategie analoghe nei propri attacchi per identificare le password e le variazioni vulnerabili più comuni. Per migliorare la sicurezza, Microsoft non pubblica il contenuto dell'elenco globale delle password escluse.

## <a name="custom-banned-password-list"></a>Elenco password personalizzate escluse

Alcune organizzazioni vogliono migliorare la sicurezza e aggiungere le proprie personalizzazioni all'elenco globale delle password escluse. Per aggiungere voci personalizzate, è possibile usare l' *elenco di password escluse*. I termini aggiunti all'elenco di password vietate personalizzate devono essere incentrati su termini specifici dell'organizzazione, ad esempio gli esempi seguenti:

- Nomi di marchio
- Nomi di prodotto
- Località, ad esempio la sede centrale aziendale
- Termini interni specifici della società
- Abbreviazioni con un significato specifico per la società

Quando le condizioni vengono aggiunte all'elenco delle password vietate personalizzate, vengono combinate con i termini nell'elenco globale delle password escluse. Gli eventi di modifica o di reimpostazione della password vengono quindi convalidati in base al set combinato di questi elenchi di password escluse.

> [!NOTE]
> L'elenco di password personalizzate escluse è limitato a un massimo di 1000 termini. Non è progettato per bloccare elenchi di password estremamente estesi.
>
> Per sfruttare appieno i vantaggi dell'elenco delle password con Banned personalizzato, è necessario prima di tutto comprendere [come vengono valutate le password](#how-are-passwords-evaluated) prima di aggiungere termini all'elenco degli elenchi bloccati personalizzati. Questo approccio consente di rilevare e bloccare in modo efficiente un numero elevato di password vulnerabili e le relative varianti.

![Modificare l'elenco di password vietate personalizzate in metodi di autenticazione](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

Si prenda in considerazione un cliente denominato *Contoso*. La società è basata a Londra e crea un prodotto denominato *widget*. Per questo esempio di cliente, sarebbe inutile e meno sicuro provare a bloccare le varianti specifiche di questi termini, come le seguenti:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso
- "LondonHQ"

Al contrario, è molto più efficiente e sicuro bloccare solo i termini di base delle chiavi, ad esempio gli esempi seguenti:

- "Contoso"
- Londra
- Widget

L'algoritmo di convalida della password blocca automaticamente le varianti e le combinazioni deboli.

Per iniziare a usare un elenco di password bannate personalizzate, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: configurare password vietate personalizzate](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Attacchi spray per la password e elenchi di password compromesse di terze parti

Azure AD Password Protection consente di difendersi dagli attacchi di spray per le password. La maggior parte degli attacchi spray per le password non tenta di attaccare un determinato account singolo più volte. Questo comportamento aumenterebbe la probabilità di rilevamento, tramite il blocco degli account o altri mezzi.

Al contrario, la maggior parte degli attacchi spray per la password invia solo un numero ridotto di password più vulnerabili note a ogni account di un'azienda. Questa tecnica consente all'autore dell'attacco di cercare rapidamente un account facilmente compromesso ed evitare potenziali soglie di rilevamento.

Azure AD la protezione delle password blocca in modo efficiente tutte le password vulnerabili note che potrebbero essere usate negli attacchi di spray per le password. Questa protezione si basa sui dati di telemetria di sicurezza reali di Azure AD per compilare l'elenco globale delle password escluse.

Sono disponibili alcuni siti Web di terze parti che enumerano milioni di password compromesse nelle precedenti violazioni della sicurezza note pubblicamente. È comune che i prodotti di convalida delle password di terze parti siano basati sul confronto di forza bruta contro questi milioni di password. Tuttavia, queste tecniche non rappresentano il modo migliore per migliorare la complessità complessiva delle password in base alle strategie tipiche usate dagli utenti malintenzionati di spray per le password.

> [!NOTE]
> L'elenco delle password Global Banned non è basato sulle origini dati di terze parti, inclusi gli elenchi di password compromesse.

Anche se l'elenco Global Banned è ridotto rispetto ad alcuni elenchi bulk di terze parti, viene originato da dati di telemetria di sicurezza reali per gli attacchi di spray con password effettivi. Questo approccio migliora la sicurezza e l'efficacia generali e anche l'algoritmo di convalida delle password utilizza tecniche di corrispondenza fuzzy. Di conseguenza, Azure AD la protezione delle password rileva in modo efficiente e blocca milioni di password vulnerabili più comuni dall'uso nell'azienda.

## <a name="on-premises-hybrid-scenarios"></a>Scenari ibridi locali

Molte organizzazioni dispongono di un modello di identità ibrido che include ambienti Active Directory Domain Services locali (AD DS). Per estendere i vantaggi di sicurezza di Azure AD la protezione delle password nell'ambiente Active Directory Domain Services, è possibile installare i componenti di nei server locali. Questi agenti richiedono che gli eventi di modifica della password nell'ambiente Active Directory Domain Services locale siano conformi agli stessi criteri password del Azure AD.

Per ulteriori informazioni, vedere [Enforce Azure ad Password Protection for Active Directory](concept-password-ban-bad-on-premises.md)Domain Services.

## <a name="how-are-passwords-evaluated"></a>Come vengono valutate le password

Quando un utente modifica o Reimposta la password, la nuova password viene verificata per la complessità e la complessità convalidando l'elenco di termini in base agli elenchi delle password vietate globali e personalizzate.

Anche se la password di un utente contiene una password vietata, è possibile che la password venga accettata se la password complessiva è sufficientemente complessa. Una password appena configurata esegue i passaggi seguenti per valutare il livello di attendibilità generale per determinare se deve essere accettato o rifiutato:

### <a name="step-1-normalization"></a>Passaggio 1: normalizzazione

Per prima cosa, una nuova password viene sottoposta a un processo di normalizzazione, Questa tecnica consente di eseguire il mapping di un piccolo set di password escluse a un set molto più ampio di password potenzialmente vulnerabili.

La normalizzazione presenta le due parti seguenti:

* Tutte le lettere maiuscole vengono modificate in lettere minuscole.
* Vengono quindi eseguite le sostituzioni di caratteri comuni, come nell'esempio seguente:

   | Lettera originale | Lettera sostituita |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | a                  |

Prendere in considerazione gli esempi seguenti:

* La password "blank" è vietata.
* Un utente tenta di modificare la password in " Bl@nK ".
* Anche se " Bl@nk " non è vietato, il processo di normalizzazione converte la password in "blank".
* Questa password viene rifiutata.

### <a name="step-2-check-if-password-is-considered-banned"></a>Passaggio 2: verificare se la password è considerata vietata

Viene quindi esaminata una password per altri comportamenti di corrispondenza e viene generato un punteggio. Questo punteggio finale determina se la richiesta di modifica della password viene accettata o rifiutata.

#### <a name="fuzzy-matching-behavior"></a>Comportamento della corrispondenza fuzzy

Sulla password normalizzata viene applicata la corrispondenza fuzzy per verificare che non contenga una password presente nell'elenco globale o nell'elenco personalizzato di password escluse. Il processo di corrispondenza è basato su una distanza di edit di un (1) confronto.

Prendere in considerazione gli esempi seguenti:

* La password "abcdef" è vietata.
* Un utente tenta di modificare la password in uno dei seguenti elementi:

   * ' abcdeg '- *l'ultimo carattere è stato modificato da' f ' a' g '*
   * ' abcdefg '- *' g ' aggiunto alla fine*
   * ' abcde '-la *fine ' f ' è stata eliminata dalla fine*

* Ognuna delle password precedenti non corrisponde specificamente alla password vietata "abcdef".

    Tuttavia, poiché ogni esempio si trova all'interno di una distanza di modifica di 1 del termine ' abcdef ' vietato, tutti gli elementi sono considerati come una corrispondenza con "abcdef".
* Queste password verranno rifiutate.

#### <a name="substring-matching-on-specific-terms"></a>Corrispondenza delle sottostringhe (su termini specifici)

La corrispondenza della sottostringa viene usata nella password normalizzata per verificare il nome e il cognome dell'utente e il nome del tenant. La corrispondenza dei nomi di tenant non viene eseguita durante la convalida delle password in un controller di dominio di servizi di dominio Active Directory per scenari ibridi locali.

> [!IMPORTANT]
> La corrispondenza della sottostringa viene applicata solo ai nomi e ad altri termini, che hanno una lunghezza di almeno quattro caratteri.

Prendere in considerazione gli esempi seguenti:

* Un utente denominato poll che desidera reimpostare la password su "p0LL23fb".
* Dopo la normalizzazione, questa password diventerebbe "poll23fb".
* La corrispondenza della sottostringa trova che la password contiene il nome dell'utente "poll".
* Anche se "poll23fb" non è stato specificamente in un elenco di password escluse, la corrispondenza di sottostringa ha trovato il "polling" nella password.
* Questa password viene rifiutata.

#### <a name="score-calculation"></a>Calcolo del punteggio

Il passaggio successivo consiste nell'identificare le eventuali istanze di password escluse presenti nella nuova password normalizzata dell'utente. I punti vengono assegnati in base ai criteri seguenti:

1. A ogni password vietata trovata nella password di un utente viene assegnato un punto.
1. così come a ogni carattere univoco rimanente.
1. Una password deve essere di almeno cinque (5) punti da accettare.

Per i due scenari di esempio successivi, contoso USA Azure AD la protezione delle password e contiene "contoso" nell'elenco delle password vietate personalizzate. Supponiamo anche che "blank" sia presente nell'elenco globale.

Nello scenario di esempio seguente un utente modifica la propria password in "C0ntos0Blank12":

* Dopo la normalizzazione, la password diventa "contosoblank12".
* Il processo di corrispondenza rileva che la password contiene due password escluse: "contoso" e "blank".
* A questa password viene quindi assegnato il punteggio seguente:

    *[Contoso] + [blank] + [1] + [2] = 4 punti*

* Poiché questa password è inferiore a cinque (5) punti, viene rifiutata.

Si osservi un esempio leggermente diverso per mostrare come la complessità aggiuntiva in una password può compilare il numero di punti necessario per l'accettazione. Nello scenario di esempio seguente un utente modifica la propria password in " ContoS0Bl@nkf9 !":

* Dopo la normalizzazione, la password diventa "contosoblankf9!".
* Il processo di corrispondenza rileva che la password contiene due password escluse: "contoso" e "blank".
* A questa password viene quindi assegnato il punteggio seguente:

    *[Contoso] + [blank] + [f] + [9] + [!] = 5 punti*

* Poiché questa password è almeno cinque (5) punti, viene accettata.

> [!IMPORTANT]
> L'algoritmo di password escluso, insieme all'elenco delle password Global Banned, può essere modificato in qualsiasi momento in Azure in base all'analisi e alla ricerca in corso sulla sicurezza.
>
> Per il servizio agente di controller di dominio locale in scenari ibridi, gli algoritmi aggiornati hanno effetto solo dopo l'aggiornamento del software dell'agente DC.

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Quando un utente tenta di reimpostare una password per un elemento vietato, viene visualizzato il messaggio di errore seguente:

*"Sfortunatamente, la password contiene una parola, una frase o un modello che rende la password facilmente intuibile. Riprovare con una password diversa ".*

## <a name="license-requirements"></a>Requisiti relativi alle licenze

| Utenti | Azure AD la protezione con password con l'elenco globale delle password escluse | Azure AD la protezione con password con elenco di password vietate personalizzate|
|-------------------------------------------|---------------------------|---------------------------|
| Solo utenti cloud                          | Azure AD Free             | Azure AD Premium P1 o P2 |
| Utenti sincronizzati da servizi di dominio Active Directory locale | Azure AD Premium P1 o P2 | Azure AD Premium P1 o P2 |

> [!NOTE]
> Gli utenti di servizi di dominio Active Directory locali che non sono sincronizzati con Azure AD traggono vantaggio anche dalla protezione Azure AD password basata sulle licenze esistenti per gli utenti sincronizzati.

Informazioni aggiuntive sulle licenze, inclusi i costi, sono disponibili nella pagina [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare un elenco di password bannate personalizzate, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: configurare password vietate personalizzate](tutorial-configure-custom-password-protection.md)

È anche possibile [abilitare la protezione Azure ad password locale](howto-password-ban-bad-on-premises-deploy.md).
