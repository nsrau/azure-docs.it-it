---
title: Panoramica di Criteri di Azure | Microsoft Docs
description: "Criteri di Azure è un servizio disponibile in Azure che consente di creare, assegnare e gestire definizioni di criteri nell'ambiente Azure."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders; nini
ms.date: 01/17/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: bb6f08e8e6252233c51c47c63ca0aec4636247dd
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="what-is-azure-policy"></a>Informazioni su Criteri di Azure

L'IT governance stabilisce con chiarezza obiettivi di business e progetti IT. Una valida IT governance include la pianificazione delle iniziative e la definizione delle priorità a livello strategico. Se l'azienda affronta una serie di problemi IT importanti che sembrano non risolversi mai, l'implementazione di criteri aiuta a gestirli meglio e a prevenirli. È con l'implementazione dei criteri che entra in gioco il servizio Criteri di Azure.

Criteri di Azure è un servizio disponibile in Azure che consente di creare, assegnare e gestire definizioni di criteri. Le definizioni di criteri applicano diverse regole e azioni in relazione alle risorse, in modo che le risorse rimangano conformi ai contratti di servizio e agli standard dell'azienda. Criteri di Azure esegue una valutazione delle risorse, alla ricerca di quelle che non sono conformi alle definizioni di criteri specificate. Si possono avere, ad esempio, criteri che consentono solo un determinato tipo di macchine virtuali e altri che richiedono che tutte le risorse dispongano di un tag specifico. Questi criteri vengono quindi valutati durante la creazione e l'aggiornamento delle risorse.

## <a name="how-is-it-different-from-rbac"></a>Quali sono le differenze rispetto al controllo degli accessi in base al ruolo?

Esistono differenze importanti tra i criteri e il controllo degli accessi in base al ruoli (RBAC). Quest'ultimo è incentrato sulle azioni dell'utente in ambiti diversi. Si può, ad esempio, essere aggiunti al ruolo di collaboratore per un gruppo di risorse nell'ambito desiderato, in modo da poter apportare modifiche a tale gruppo di risorse. Il servizio Criteri è incentrato sulle proprietà delle risorse durante la distribuzione e per le risorse già esistenti. Tramite i criteri, ad esempio, si possono controllare i tipi di risorse di cui è possibile effettuare il provisioning. In alternativa, si possono limitare le posizioni in cui è possibile effettuare il provisioning delle risorse. A differenza del controllo degli accessi in base al ruolo, i criteri rappresentano un sistema con autorizzazioni predefinite e negazione esplicita.

Per usare i criteri, l'utente deve essere autenticato tramite il controllo degli accessi in base al ruolo. In particolare, per l'account sono necessari:

- Autorizzazione `Microsoft.Authorization/policydefinitions/write` per definire i criteri.
- Autorizzazione `Microsoft.Authorization/policyassignments/write` per assegnare i criteri.

Queste autorizzazioni non sono incluse nel ruolo di **collaboratore**.


## <a name="policy-definition"></a>Definizione di criteri

Ogni definizione di criteri include condizioni da cui ne dipende l'applicazione. Dispone inoltre di un'azione associata che viene eseguita se vengono soddisfatte le condizioni.

In Criteri di Azure sono disponibili alcuni criteri predefiniti, Ad esempio: 

- **Require SQL Server 12.0** (Richiedi SQL Server 12.0). Questa definizione di criteri contiene condizioni/regole per garantire che in tutte le istanze di SQL Server venga usata la versione 12.0. L'azione consiste nel rifiutare tutti i server che non soddisfano tali criteri.
- **Allowed Storage Account SKUs** (SKU di account di archiviazione consentiti). Questa definizione di criteri contiene un set di condizioni/regole per determinare se un account di archiviazione in fase di distribuzione rientra in un set di dimensioni di SKU. L'azione consiste nel rifiutare tutti i server che non rispettano il set delle dimensioni di SKU definite.
- **Allowed Resource Type** (Tipo di risorsa consentito). Questa definizione di criteri contiene un set di condizioni/regole per specificare i tipi di risorsa che possono essere distribuiti dall'organizzazione. L'azione consiste nel rifiutare tutte le risorse non incluse nell'elenco definito.
- **Allowed Locations** (Percorsi consentiti): questi criteri consentono di limitare i percorsi che l'organizzazione può specificare durante la distribuzione delle risorse. Questa azione viene usata per imporre i requisiti di conformità geografica.
- **Allowed Virtual Machine SKUs** (SKU delle macchine virtuali consentiti): questi criteri consentono di specificare un set di SKU di macchine virtuali che l'organizzazione può distribuire.
- **Apply tag and its default value** (Applica tag e relativo valore predefinito): questi criteri applicano un tag obbligatorio e il relativo valore predefinito, se non è specificato dall'utente.
- **Enforce tag and its value** (Imponi tag e relativo valore): questi criteri impongono l'uso di un tag obbligatorio e del relativo valore in una risorsa.
- **Not allowed resource types** (Tipi di risorsa non consentiti): questi criteri consentono di specificare i tipi di risorsa che l'organizzazione non può distribuire.

Tutti questi criteri possono essere assegnati tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

Per altre informazioni sulle strutture delle definizioni di criteri, vedere [Struttura delle definizioni di criteri](policy-definition.md).

## <a name="policy-assignment"></a>Assegnazione di criteri

Un'assegnazione di criteri è una definizione di criteri che è stata assegnata per l'implementazione in un ambito specifico. L'ambito può spaziare da un gruppo di gestione a un gruppo di risorse. Il termine *ambito* fa riferimento a tutti i gruppi di risorse, le sottoscrizioni o i gruppi di gestione a cui è assegnata la definizione di criteri. Le assegnazioni dei criteri vengono ereditate da tutte le risorse figlio. I criteri applicati a un gruppo di risorse vengono pertanto applicati a tutte le risorse presenti nel gruppo. È tuttavia possibile escludere un sottoambito dall'assegnazione dei criteri. Nell'ambito della sottoscrizione è ad esempio possibile assegnare criteri che non permettono la creazione di risorse di rete. È tuttavia possibile escludere un gruppo di risorse nella sottoscrizione che è pensato per l'infrastruttura di rete e consentire l'accesso a questo gruppo specifico a utenti fidati in grado di creare risorse di rete.

Per altre informazioni sull'impostazione delle definizioni e delle assegnazioni di criteri, vedere [Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure](assign-policy-definition.md).

## <a name="policy-parameters"></a>Parametri dei criteri

I parametri dei criteri consentono di semplificarne la gestione riducendo il numero di definizioni di criteri che è necessario creare. È possibile definire parametri mentre si crea una definizione di criteri per renderla più generica. La definizione di criteri può successivamente essere usata anche per altri scenari. È sufficiente, a tal fine, passare valori diversi durante l'assegnazione della definizione di criteri. Si può ad esempio specificare un set di località per una sottoscrizione.

I parametri vengono definiti/creati quando si crea una definizione di criteri. Quando viene definito un parametro, vengono assegnati un nome e facoltativamente un valore. È ad esempio possibile definire un parametro per i criteri denominato *location* (posizione) e assegnargli valori diversi durante l'assegnazione dei criteri, ad esempio *EastUS* o *WestUS*.

<!--
Next link should point to new Concept page for Parameters
-->
Per altre informazioni sui parametri dei criteri, vedere [Parametri](policy-definition.md#parameters) nella panoramica dei criteri delle risorse.

## <a name="initiative-definition"></a>Definizione di iniziativa
Una definizione di iniziativa è una raccolta di definizioni di criteri ottimizzate per il raggiungimento di un singolo obiettivo globale. Le definizioni di iniziativa semplificano la gestione e l'assegnazione delle definizioni di criteri, in quanto raggruppano un set di criteri in un unico elemento. Ad esempio, è possibile creare un'iniziativa denominata **Abilita monitoraggio nel Centro sicurezza di Azure** con l'obiettivo di monitorare tutte le raccomandazioni sulla sicurezza disponibili nel Centro sicurezza di Azure.

In questa iniziativa saranno incluse definizioni di criteri come le seguenti:

1. **Monitora database SQL non crittografato nel Centro sicurezza**, per il monitoraggio dei server e dei database SQL non crittografati.
2. **Monitora vulnerabilità del sistema operativo nel Centro sicurezza**, per il monitoraggio dei server che non soddisfano la baseline configurata.
3. **Monitora Endpoint Protection mancante nel Centro sicurezza**, per il monitoraggio dei server in cui non è installato un agente di Endpoint Protection.

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>Assegnazione di iniziativa
Analogamente a un'assegnazione di criteri, un'assegnazione di iniziativa è una definizione di iniziativa assegnata a un ambito specifico. Le assegnazioni di iniziativa riducono l'esigenza di creare diverse definizioni di iniziativa per ogni ambito. L'ambito potrebbe anche comprendere da un gruppo di gestione a un gruppo di risorse.

Nell'esempio precedente, l'iniziativa **Abilita monitoraggio nel Centro sicurezza di Azure** può essere assegnata a diversi ambiti. Si può, ad esempio, associare un'assegnazione a **subscriptionA** e un'altra a **subscriptionB**.

## <a name="initiative-parameters"></a>Parametri delle iniziative
Analogamente ai parametri dei criteri, i parametri delle iniziative consentono di semplificarne la gestione riducendo la ridondanza. I parametri delle iniziative sono essenzialmente l'elenco dei parametri usati dalle definizioni di criteri incluse nell'iniziativa.

Si consideri ad esempio uno scenario con una definizione di iniziativa, **initiativeC**, che include due definizioni di criteri, ognuna con un parametro definito:

| Criterio | Nome del parametro |Tipo di parametro  |Note |
|---|---|---|---|
| policyA | allowedLocations | array  |Questo parametro prevede come valore un elenco di stringhe, perché il parametro è stato definito come di tipo matrice |
| policyB | allowedSingleLocation |stringa |Questo parametro prevede come valore una parola, perché il parametro è stato definito come di tipo stringa |

In questo scenario, per la definizione dei parametri dell'iniziativa **initiativeC** sono disponibili tre opzioni:

1. Sfruttare i parametri delle definizioni di criteri incluse nell'iniziativa. In questo esempio *allowedLocations* e *allowedSingleLocation* diventano i parametri dell'iniziativa **initiativeC**.
2. Specificare valori per i parametri delle definizioni di criteri incluse nella definizione di iniziativa. In questo esempio, è possibile specificare un elenco di località per il **parametro allowedLocations di policyA** e il **parametro allowedSingleLocation di policyB**.
È anche possibile specificare i valori quando si assegna l'iniziativa.
3. Specificare un elenco di opzioni di *valore* utilizzabili durante l'assegnazione dell'iniziativa. Quando si assegna l'iniziativa, i parametri ereditati dalle definizioni di criteri incluse nell'iniziativa possono contenere solo valori presenti nell'elenco specificato.

È ad esempio possibile creare un elenco di opzioni di valori in una definizione di iniziativa che contenga i valori *EastUS*, *WestUS*, *CentralUS* e *WestEurope*. In questo caso, non è possibile immettere un valore diverso, ad esempio *Southeast Asia*, durante l'assegnazione dell'iniziativa perché non è incluso nell'elenco.

## <a name="recommendations-for-managing-policies"></a>Raccomandazioni per la gestione dei criteri

Durante la creazione e la gestione delle definizioni e delle assegnazioni di criteri, è consigliabile seguire alcune indicazioni:

- Se si creano definizioni di criteri nel proprio ambiente, è consigliabile iniziare con un effetto di controllo, anziché di rifiuto, per tenere traccia dell'impatto della definizione di criteri sulle risorse nell'ambiente. Se sono già presenti script per aumentare automaticamente le prestazioni delle applicazioni, l'impostazione di un effetto di rifiuto potrebbe impedire tali attività di automazione già implementate.
- Quando si creano le definizioni e le assegnazioni, è importante tenere presenti le gerarchie organizzative. È consigliabile creare le definizioni a un livello superiore, ad esempio a livello di gruppo di gestione o di sottoscrizione, e l'assegnazione al livello figlio successivo. Se, ad esempio, si crea una definizione di criteri a livello di gruppo di gestione, è possibile limitare l'ambito di un'assegnazione di criteri di tale definizione a un livello di sottoscrizione nel gruppo di gestione.
- È consigliabile usare il piano tariffario Standard, per comprendere meglio lo stato di conformità dell'ambiente. Per altre informazioni sui modelli di piano tariffario disponibili e sulle rispettive offerte, vedere [Prezzi di Criteri di Azure](https://azure.microsoft.com/pricing/details/azure-policy).
- È consigliabile usare sempre definizioni di iniziativa invece di definizioni di criteri, anche se si intende creare un solo criterio. Se ad esempio si dispone di una definizione di criteri, *policyDefA*, e la si crea nella definizione di iniziativa *initiativeDefC*, se si decide di creare un'altra definizione di criteri in un secondo momento per *policyDefB* con obiettivi simili a quelli di *policyDefA*, la si può aggiungere in *initiativeDefC* per tenerne meglio traccia.

   Si tenga presente che dopo la creazione di un'assegnazione di iniziativa da una definizione di iniziativa, tutte le nuove definizioni di criteri aggiunte alla definizione di iniziativa vengono automaticamente incluse nelle assegnazioni di iniziativa associate a tale definizione. Se tuttavia nella nuova definizione di criteri è stato introdotto un nuovo parametro, è necessario aggiornare la definizione di iniziativa e le relative assegnazioni modificando la definizione o l'assegnazione di conseguenza.

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati i passaggi successivi consigliati dopo questa panoramica di Criteri di Azure e di alcuni concetti chiave introdotti:

- [Assegnare una definizione di criteri](./assign-policy-definition.md)
- [Assegnare una definizione di criteri con l'interfaccia della riga di comando di Azure](./assign-policy-definition-cli.md)
- [Assegnare una definizione di criteri con PowerShell](./assign-policy-definition-ps.md)
