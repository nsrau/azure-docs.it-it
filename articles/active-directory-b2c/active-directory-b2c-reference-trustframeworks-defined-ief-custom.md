---
title: "Azure Active Directory B2C: riferimenti - Framework attendibilità | Microsoft Docs"
description: "Informazioni sui criteri personalizzati e sul Framework dell'esperienza di gestione delle identità di Azure Active Directory B2C"
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
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: 4e2de9c4d1c0f92970911e132fffaacbd01d9ad0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definizione dei framework attendibilità basati sul Framework dell'esperienza di gestione delle identità di Azure AD B2C

I criteri personalizzati di Azure Active Directory B2C (Azure AD B2C) che usano il Framework dell'esperienza di gestione delle identità offrono all'organizzazione un servizio centralizzato. Questo servizio riduce la complessità della federazione delle identità in una community di interesse di grandi dimensioni a una singola relazione di trust e a un singolo scambio di metadati.

I criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità consentono di rispondere alle domande seguenti:

- Quali criteri legali, di sicurezza, privacy e protezione dei dati devono essere rispettati?
- Chi sono i contatti e quali sono le procedure per diventare membro accreditato?
- Chi sono i provider di informazioni di identità accreditati (anche noti come "provider di attestazioni") e cosa offrono?
- Chi sono le relying party accreditate e, facoltativamente, cosa richiedono?
- Quali sono i requisiti tecnici di interoperabilità "in transito" per i membri?
- Quali sono le regole operative di "runtime" che devono essere applicate per lo scambio di informazioni di identità digitali?

Per rispondere a tutte queste domande, i criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità usano il costrutto del framework attendibilità. Esaminiamo ora questo costrutto e il suo contributo in questo senso.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Nozioni fondamentali sul framework attendibilità e sulla gestione della federazione

Il framework attendibilità è una specifica scritta dei criteri di identità, sicurezza, privacy e protezione dei dati a cui i membri di una community di interesse devono conformarsi.

L'identità federata rappresenta la base per ottenere la verifica delle identità degli utenti finali a livello di Internet. Se si delega la gestione delle identità a terze parti, una singola identità digitale per un utente finale può essere usata con più relying party.  

La verifica delle identità richiede che i provider di identità (IdP) e i provider di attributi aderiscano a procedure e criteri operativi, di privacy e sicurezza specifici.  Se non possono eseguire ispezioni dirette, le relying party (RP) devono sviluppare relazioni di trust con i provider di identità e i provider di attributi con i quali scelgono di lavorare.  

Con l'aumento del numero di consumer e di provider di informazioni di identità digitali, diventa difficile proseguire la gestione pairwise di queste relazioni di trust o anche lo scambio pairwise dei metadati tecnici necessari per la connettività di rete.  Gli hub di federazione hanno risolto questi problemi solo in parte.

### <a name="what-a-trust-framework-specification-defines"></a>Contenuto definito in una specifica di framework di attendibilità
I framework attendibilità rappresentano il cardine del modello di Open Identity Exchange (OIX), in cui ogni community di interesse è regolata da una specifica di framework attendibilità. Questa specifica di framework attendibilità definisce:

- **Le metriche di sicurezza e privacy per la community di interesse con la definizione degli elementi seguenti:**
    - I livelli di verifica che sono offerti/richiesti dai membri, ad esempio un set ordinato di valutazioni di attendibilità per l'autenticità di informazioni su identità digitali.
    - I livelli di protezione offerti/richiesti dai membri, ad esempio un set ordinato di valutazioni di attendibilità per la protezione di informazioni su identità digitali gestite da membri nella community di interesse.

- **La descrizione delle informazioni su identità digitali offerte/richieste dai membri**.

- **I criteri tecnici per la produzione e l'uso delle informazioni su identità digitali e quindi per la misurazione dei livelli di verifica e dei livelli di protezione. Questi criteri scritti includono in genere le categorie seguenti:**
    - Criteri di verifica dell'identità, ad esempio *con quale rigore vengono controllate le informazioni sull'identità di un utente?*
    - Criteri di sicurezza, ad esempio *con quale rigore vengono protette l'integrità e la riservatezza delle informazioni?*
    - Criteri di privacy, ad esempio *quale controllo ha un utente sulle informazioni di identificazione personale*?
    - Criteri di capacità di sopravvivenza, ad esempio *come vengono gestite la continuità e la protezione delle informazioni di identificazione personale se un provider cessa l'attività?*

- **I profili tecnici per la produzione e l'uso delle informazioni su identità digitali. Questi profili includono:**
    - Interfacce di ambito per le quali sono disponibili le informazioni su identità digitali al livello di verifica specificato.
    - Requisiti tecnici per l'interoperabilità in transito.

- **Le descrizioni dei diversi ruoli che i membri della community possono rivestire insieme alle qualifiche necessarie per adempiere a questi ruoli.**

Una specifica di framework attendibilità determina quindi la modalità di scambio delle informazioni di identità tra i membri della community di interesse: relying party, provider di identità e di attributi e verificatori di attributi.

Una specifica di framework attendibilità è organizzata in uno o più documenti che fungono da riferimento per la governance della community di interesse, regolando l'asserzione e l'uso di informazioni su identità digitali all'interno della community. Ciò implica una serie documentata di criteri e procedure, progettati per stabilire l'attendibilità nelle identità digitali usate per le transazioni online tra i membri di una community di interesse.  

Una specifica di framework attendibilità definisce quindi le regole per la creazione di un ecosistema di identità federate valido per una community.

Il vantaggio di questo approccio è oggi ampiamente riconosciuto. Senza dubbio le specifiche di framework attendibilità semplificano lo sviluppo di ecosistemi di identità digitali con caratteristiche verificabili di sicurezza, verifica e privacy che ne consentono il riutilizzo in più community di interesse.

Per questo motivo, i criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità usano la specifica come base della rappresentazione dei dati per un framework attendibilità per semplificare l'interoperabilità.  

I criteri personalizzati di Azure Active Directory B2C che si basano sul Framework dell'esperienza di gestione delle identità rappresentano una specifica di framework attendibilità come una combinazione di dati leggibili da computer e da risorse umane. Alcune sezioni di questo modello (in genere le sezioni più orientate alla governance) vengono rappresentate come riferimenti a documentazione pubblicata sui criteri di sicurezza e privacy insieme alle procedure correlate (se presenti). Altre sezioni descrivono in dettaglio le regole di runtime e i metadati di configurazione che facilitano l'automazione operativa.

## <a name="understand-trust-framework-policies"></a>Criteri di framework attendibilità

In termini di implementazione, la specifica di framework attendibilità è costituita da un set di criteri che offrono il controllo completo sulle esperienze e sui comportamenti delle identità.  I criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità consentono di creare framework attendibilità personali tramite tali criteri dichiarativi che possono definire e configurare:

- I documenti o i riferimenti che definiscono l'ecosistema di identità federative della community in relazione al framework attendibilità. Si tratta di collegamenti alla documentazione relativa al framework di attendibilità. Le regole operative di "runtime" predefinite oppure i percorsi utente che automatizzano e/o controllano lo scambio e l'uso delle attestazioni. Questi percorsi utente sono associati a un livello di verifica e a un livello di protezione. Un criterio può quindi avere percorsi utente con livelli di verifica e livelli di protezione differenti.

- I provider di identità e di attributi o i provider di attestazioni nella community di interesse e i profili tecnici supportati insieme all'accreditamento dei livelli di verifica e di protezione fuori banda correlati.

- L'integrazione con verificatori di attributi o provider di attestazioni.

- Le relying party nella community (per inferenza).

- I metadati per stabilire comunicazioni di rete tra i membri. Questi metadati vengono usati insieme ai profili tecnici durante una transazione per ottimizzare l'interoperabilità "in transito" tra la relying party e altri membri della community.

- L'eventuale conversione dei protocolli, ad esempio SAML, OAuth2, WS-Federation e OpenID Connect.

- I requisiti di autenticazione.

- L'eventuale orchestrazione a più fattori.

- Uno schema condiviso per tutte le attestazioni disponibili e i mapping ai membri di una community di interesse.

- Tutte le trasformazioni di attestazioni, insieme all'eventuale riduzione dei dati in questo contesto, per sostenere lo scambio e l'uso delle attestazioni.

- Il binding e la crittografia.

- L'archivio delle attestazioni.

### <a name="understand-claims"></a>Attestazioni

> [!NOTE]
> Sono definiti "attestazioni" tutti i possibili tipi di informazioni di identità che possono essere scambiati come "attestazioni": attestazioni sulle credenziali di autenticazione di un utente finale, controllo delle identità, dispositivo di comunicazione, posizione fisica, attributi di identificazione personale e così via.  
>
> Si usa il termine "attestazioni" anziché "attributi" perché nelle transazioni online questi elementi dati non possono essere verificati direttamente dalla relying party. Si tratta piuttosto di asserzioni o attestazioni su fatti verso cui la relying party deve sviluppare una fiducia sufficiente a garantire la transazione richiesta dell'utente finale.  
>
> Si usa il termine "attestazioni" anche perché i criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità sono progettati per semplificare lo scambio di tutti i tipi di informazioni di identità digitale in modo coerente, indipendentemente dal fatto che il protocollo sottostante sia definito per l'autenticazione utente o il recupero di attributi.  Si usa analogamente il termine "provider di attestazioni" per fare riferimento ai provider di identità, i provider di attributi e i verificatori di attributi, quando non si intende distinguerli in base alle funzioni specifiche.   

Questi determinano la modalità di scambio delle informazioni di identità tra una relying party, i provider di identità e di attributi e i verificatori di attributi. Stabiliscono i provider di identità e di attributi necessari per l'autenticazione di una relying party. Devono essere considerati un linguaggio specifico di dominio, ovvero un linguaggio di programmazione specializzato per un determinato dominio dell'applicazione con ereditarietà, istruzioni *if* e polimorfismo.

Questi criteri costituiscono la parte leggibile dal computer del costrutto TF nei criteri personalizzati di Azure Active Directory B2C basati sul Framework dell'esperienza di gestione delle identità. Includono tutti i dettagli operativi, inclusi i metadati e i profili tecnici dei provider di attestazioni, le definizioni dello schema di attestazioni, le funzioni di trasformazione di attestazioni e i percorsi utente che vengono compilati per facilitare l'automazione e l'orchestrazione operative.  

Questi sono considerati *documenti in continua modifica* perché è probabile che il relativo contenuto verrà cambiato nel tempo relativamente ai membri attivi dichiarati nei criteri. È possibile che cambino anche le condizioni per diventare membri.  

La configurazione e la manutenzione della federazione risultano notevolmente semplificate schermando le relying party da continue riconfigurazioni di attendibilità e connettività quando diversi provider di attestazioni/verificatori entrano o escono dalla community rappresentata dal set di criteri.

L'interoperabilità è un'altra sfida importante. È necessario integrare altri provider di attestazioni/verificatori, perché è improbabile che le relying party supportino tutti i protocolli necessari. I criteri personalizzati di Azure AD B2C risolvono questo problema supportando protocolli standard del settore e applicando percorsi utente specifici per trasporre le richieste quando le relying party e i provider di attributi non supportano lo stesso protocollo.  

I percorsi utente includono profili di protocolli e metadati che vengono usati per ottimizzare l'interoperabilità "in transito" tra la relying party e altri membri. Sono anche disponibili regole di runtime operative che vengono applicate ai messaggi di richiesta/risposta per lo scambio di informazioni di identità ai fini della conformità con i criteri pubblicati nell'ambito della specifica di framework attendibilità. Il concetto di percorso utente è fondamentale per la personalizzazione dell'esperienza del cliente. Chiarisce anche il funzionamento del sistema a livello di protocollo.

Su questa base, le applicazioni e i portali delle relying party possono, a seconda del contesto, richiamare i criteri personalizzati di Azure AD B2C basati sul Framework dell'esperienza di gestione delle identità passando il nome di un criterio specifico e ottenere esattamente il comportamento e lo scambio di informazioni desiderati in modo semplice e senza rischi.
