---
title: "Azure Active Directory B2C: Informazioni di riferimento - Framework attendibilità | Microsoft Docs"
description: Argomento sui criteri personalizzati di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 019dd66d5abe4242ffb6cdc3276929dcd7f04466
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---

# <a name="defining-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definizione di framework attendibilità con il Framework dell'esperienza di gestione delle identità di Azure AD B2C

I criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità offrono all'organizzazione un servizio centralizzato che riduce a un'unica relazione di trust e a un unico scambio di metadati la complessità della federazione delle identità di una vasta community di interesse.

A tale scopo, i criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità devono consentire di rispondere alle domande seguenti.

- *Quali criteri legali, di sicurezza, privacy e protezione dati devono essere rispettati?*
- *Chi sono i contatti e quali sono le procedure per diventare membro accreditato?*
- *Chi sono i provider di informazioni di identità accreditati (anche noti come provider di attestazioni) e cosa offrono?*
- *Chi sono le relying party accreditate e, facoltativamente, cosa richiedono?*
- *Quali sono i requisiti tecnici di interoperabilità "in transito" per i membri?*
- *Quali sono le regole operative di "runtime" che devono essere applicate per lo scambio di informazioni di identità digitali?*

Per rispondere a tutte queste domande, i criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità sfruttano il costrutto del framework attendibilità. Si esaminerà ora il costrutto e il suo contributo in questo senso.

## <a name="understanding-the-trust-framework-and-federation-management-foundation"></a>Framework attendibilità e gestione della federazione

Tale costrutto deve essere interpretato come una specifica scritta dei criteri di identità, sicurezza, privacy e protezione dei dati che devono rispettati dai membri di una community di interesse.

L'identità federata costituisce una base per ottenere la verifica dell'identità degli utenti finali su scala Internet.  Delegando la gestione delle identità a terze parti, una singola identità digitale per un utente finale può essere riutilizzata con più relying party.  

La verifica dell'identità richiede effettivamente che i provider di attributi e i provider di identità siano conformi a procedure e criteri operativi, di privacy e sicurezza specifici.  Non potendo eseguire ispezioni dirette, le relying party devono sviluppare relazioni di trust con i provider di identità e i provider di attributi con i quali scelgono di lavorare.  Con il rapido incremento del numero di consumer e provider di informazioni di identità digitale, diventa insostenibile proseguire la gestione pairwise di queste relazioni di trust o anche lo scambio pairwise dei metadati tecnici necessari per la connettività di rete.  Gli hub di federazione hanno risolto questi problemi solo in parte.

I framework attendibilità rappresentano il cardine del modello di Open Identity Exchange (OIX), in cui ogni community di interesse è regolata da una specifica di framework attendibilità. Questa specifica di framework attendibilità definisce:

- Le metriche di sicurezza e privacy per la community di interesse con la definizione degli elementi seguenti:
    - Livelli di verifica offerti/richiesti dai membri, ovvero un set ordinato di classificazioni di attendibilità per l'autenticità delle informazioni di identità digitale.
    - Livelli di protezione offerti/richiesti dai membri, ovvero un set ordinato di classificazioni di attendibilità per la protezione delle informazioni di identità digitale gestite dai membri nella community di interesse.
- La descrizione delle informazioni di identità digitale offerte/richieste dai membri.
- I criteri tecnici per la produzione e l'utilizzo delle informazioni di identità digitale e quindi per la misurazione dei livelli di verifica e dei livelli di protezione. Questi criteri scritti includono in genere le categorie seguenti:
    - Criteri di verifica dell'identità: *con quale rigore vengono esaminate le informazioni di identità di un utente?*
    - Criteri di sicurezza: *con quale rigore vengono protette l'integrità delle informazioni e la riservatezza?*
    - Criteri di privacy: *quale controllo ha un utente sulle informazioni di identificazione personale*?
    - Criteri di capacità di sopravvivenza: continuità e protezione delle informazioni di identificazione personale se un provider cessa l'attività.
- I profili tecnici per la produzione e l'utilizzo delle informazioni di identità digitale. Questi profili:
    - Definiscono l'ambito delle interfacce per le quali sono disponibili le informazioni di identità digitale al livello di verifica specificato.
    - Descrivono i requisiti tecnici per l'interoperabilità "in transito".
- Le descrizioni dei diversi ruoli che i membri della community possono svolgere insieme alle qualifiche necessarie per adempiere a questi ruoli.

Una specifica di framework attendibilità determina quindi la modalità di scambio delle informazioni di identità tra i membri della community di interesse: relying party, provider di identità e di attributi e verificatori di attributi.

Nel gergo di questo modello di framework attendibilità OIX, una specifica di framework attendibilità è organizzata in uno o più documenti che fungono da riferimento per la governance della community di interesse, regolando l'asserzione e l'utilizzo di informazioni di identità digitale all'interno della community. Ciò implica una serie documentata di criteri e procedure, progettati per stabilire l'attendibilità nelle identità digitali usate per le transazioni online tra i membri di una community di interesse.  **Una specifica di framework attendibilità definisce le regole per la creazione di un ecosistema di identità federate valido per alcune community.**

Il vantaggio di questo approccio è oggi ampiamente riconosciuto e senza dubbio le specifiche del framework attendibilità semplificheranno lo sviluppo di ecosistemi di identità digitale con caratteristiche di sicurezza verificabile, controllo e privacy che ne consentiranno il riutilizzo in più community di interesse.

Per questo motivo, i criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità sfruttano la specifica come base della rappresentazione dei dati per un framework attendibilità per semplificare l'interoperabilità.  

I criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità rappresentano una specifica di framework attendibilità sotto forma di combinazione di dati leggibili da utenti e da computer: alcune sezioni di questo modello, in genere quelle più orientate alla governance, sono rappresentate come riferimenti alla documentazione pubblicata per criteri di protezione e privacy con le eventuali procedure correlate, mentre altre sezioni descrivono nei dettagli le regole di runtime e i metadati di configurazione per semplificare l'automazione operativa.

## <a name="understanding-trust-framework-policies"></a>Informazioni sui criteri di framework attendibilità

In termini di implementazione, la specifica di framework attendibilità indicata in precedenza è costituita da criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità in un set di criteri che consentono il controllo completo sul comportamento e le esperienze di identità.  I criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità consentono di creare framework attendibilità tramite tali criteri dichiarativi che possono definire e configurare:

- I riferimenti a documenti che definiscono l'ecosistema di identità federative della community in relazione al framework attendibilità. Si tratta di collegamenti alla documentazione relativa al framework di attendibilità. Le regole operative di "runtime" predefinite oppure i percorsi utente che automatizzano e/o controllano lo scambio e l'uso delle attestazioni. Questi percorsi utente sono associati a un livello di verifica e a un livello di protezione. Un criterio può quindi avere percorsi utente con livelli di verifica e livelli di protezione differenti.
- I provider di identità e di attributi o i provider di attestazioni nella community di interesse e i profili tecnici supportati insieme all'accreditamento dei livelli di verifica e di protezione fuori banda correlati.
- L'integrazione con verificatori di attributi o provider di attestazioni.
- Le relying party nella community (per inferenza).
- I metadati per stabilire comunicazioni di rete tra i membri. Questi metadati verranno usati insieme ai profili tecnici durante una transazione per ottimizzare l'interoperabilità "in transito" tra la relying party e altri membri della community.
- L'eventuale conversione dei protocolli, ovvero SAML, OAuth2, WS-Federation e OpenID Connect.
- I requisiti di autenticazione.
- L'eventuale orchestrazione a più fattori.
- Uno schema condiviso per tutte le attestazioni disponibili e i mapping ai membri di una community di interesse.
- Tutte le trasformazione delle attestazioni, insieme all'eventuale riduzione dei dati in questo contesto, per sostenere lo scambio e l'uso delle attestazioni.
- Il binding e la crittografia.
- L'archivio delle attestazioni.

> [!NOTE]
> Sono definite collettivamente "attestazioni" tutti i possibili tipi di informazioni di identità che possono essere scambiati: attestazioni sulle credenziali di autenticazione dell'utente finale, esame delle identità, dispositivo di comunicazione, posizione fisica, attributi di identificazione personale e così via.  
>
> Si usa il termine *attestazioni*, e non il termine "attributi" che rappresenta un subset, perché nel caso di transazioni online non si tratta di elementi che possono essere verificati direttamente dalla relying party; sono piuttosto asserzioni oppure attestazioni relative a elementi che la relying party deve ritenere sufficientemente attendibili per consentire la transazione dell'utente finale.  
>
> Si usa il termine "attestazioni" anche perché i criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità sono progettati per semplificare lo scambio di tutti i tipi di informazioni di identità digitale in modo coerente, indipendentemente dal fatto che il protocollo sottostante sia definito per l'autenticazione utente o il recupero di attributi.  Allo stesso modo si userà la definizione "provider di attestazioni" per fare riferimento ai provider di identità, ai provider di attributi e ai verificatori di attributi, quando non si intende distinguerli in base alle funzioni specifiche.   

Questi determinano la modalità di scambio delle informazioni di identità tra una relying party, i provider di identità e di attributi e i verificatori di attributi. Stabiliscono i provider di identità e di attributi necessari per l'autenticazione di una relying party. Devono essere considerati un linguaggio specifico di dominio, ovvero un linguaggio di programmazione specializzato per un determinato dominio dell'applicazione con ereditarietà, istruzioni *if* e polimorfismo.

Questi criteri costituiscono la parte leggibile da computer del costrutto del framework attendibilità nei criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità con tutti i dettagli operativi, inclusi i metadati dei provider di attestazioni e la definizione dello schema di attestazione dei profili tecnici, le funzioni di trasformazione delle attestazioni e i percorsi utente compilati per semplificare l'automazione e orchestrazione operativa.  

Sono considerati *documenti dinamici* nei criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità perché è molto probabile che il loro contenuto cambi nel tempo per quanto riguarda i membri attivi dichiarati nei criteri e anche, in alcune situazioni, per quanto riguarda i termini e le condizioni per la partecipazione.  
La configurazione e la manutenzione della federazione risultano notevolmente semplificate schermando le relying party da continue riconfigurazioni di attendibilità e connettività quando diversi provider di attestazioni/verificatori entrano o escono dalla community rappresentata dal set di criteri.

L'interoperabilità è un altro aspetto complesso quando è necessario integrare altri provider di attestazioni/verificatori, perché è improbabile che le relying party supportino tutti i protocolli necessari. I criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità risolvono questo problema supportando protocolli standard del settore e applicando percorsi utente specifici per trasporre le richieste quando le relying party e i provider di attributi non supportano lo stesso protocollo.  

I percorsi utente includono profili di protocolli e metadati che verranno usati per ottimizzare l'interoperabilità "in transito" tra la relying party e altri membri.  Sono anche disponibili regole operative di runtime che verranno applicate ai messaggi di richiesta/risposta per lo scambio di informazioni di identità ai fini della conformità con i criteri pubblicati nell'ambito della specifica del framework attendibilità. L'idea dei percorsi utente è fondamentale per la personalizzazione dell'esperienza del cliente e chiarisce il funzionamento del sistema a livello di protocollo.

Su questa base, le applicazioni e i portali delle relying party possono, a seconda del loro contesto, richiamare i criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità passando il nome di un criterio specifico e ottenere esattamente il comportamento e lo scambio di informazioni desiderati in modo semplice e senza rischi.

