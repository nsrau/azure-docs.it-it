---
title: Panoramica di Criteri di Azure | Microsoft Docs
description: "Criteri di Azure è un servizio disponibile in Azure che consente di creare, assegnare e gestire definizioni di criteri nell'ambiente Azure."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 10/06/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: 01b0915cdf37ddc00a4e6a38c99ce7f6f8c4f9c9
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-policy"></a>Informazioni su Criteri di Azure

L'IT governance stabilisce con chiarezza obiettivi di business e progetti IT. Una valida IT governance include la pianificazione delle iniziative e la definizione delle priorità a livello strategico. Se l'azienda riscontra un elevato numero di problemi IT apparentemente irrisolvibili, l'implementazione di criteri consente di gestirli ed evitarli con maggiore efficacia. Criteri di Azure offre le funzionalità necessarie.

Criteri di Azure è un servizio disponibile in Azure che consente di creare, assegnare e gestire definizioni di criteri. Le definizioni di criteri applicano diverse regole e azioni in relazione alle risorse, in modo che le risorse rimangano conformi ai contratti di servizio e agli standard dell'azienda. A questo scopo viene eseguita una valutazione delle risorse per cercare quelle non conformi alle definizioni di criteri implementate.

## <a name="policy-definition"></a>Definizione di criteri

Ogni definizione di criteri include le condizioni in presenza delle quali viene applicata e un'azione corrispondente che viene eseguita se vengono soddisfatte le condizioni.

In Criteri di Azure sono disponibili alcuni criteri predefiniti, ad esempio:

- **Require SQL Server 12.0** (Richiedi SQL Server 12.0). Questa definizione di criteri contiene condizioni/regole per garantire che in tutte le istanze di SQL Server venga usata la versione 12.0. L'azione consiste nel rifiutare tutti i server che non soddisfano tali criteri.
- **Allowed Storage Account SKUs** (SKU di account di archiviazione consentiti). Questa definizione di criteri contiene un set di condizioni/regole per determinare se un account di archiviazione in fase di distribuzione rientra in un set di dimensioni di SKU. L'azione consiste nel rifiutare tutti i server che non rispettano il set delle dimensioni di SKU definite.
- **Allowed Resource Type** (Tipo di risorsa consentito). Questa definizione di criteri contiene un set di condizioni/regole per specificare i tipi di risorsa che possono essere distribuiti dall'organizzazione. L'azione consiste nel rifiutare tutte le risorse non incluse nell'elenco definito.

Per altre informazioni sulle strutture delle definizioni di criteri, vedere [Struttura delle definizioni di criteri](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure).

## <a name="policy-assignment"></a>Assegnazione di criteri
Un'assegnazione di criteri è una definizione di criteri che è stata assegnata per l'implementazione in un ambito specifico. L'ambito potrebbe comprendere da un gruppo di gestione a un gruppo di risorse.

Per altre informazioni sull'impostazione delle definizioni e delle assegnazioni di criteri, vedere [Panoramica dei criteri delle risorse](../azure-resource-manager/resource-manager-policy.md).

## <a name="policy-parameters"></a>Parametri dei criteri
I parametri dei criteri consentono di semplificarne la gestione riducendo il numero di definizioni di criteri che è necessario creare. È possibile definire parametri mentre si crea una definizione di criteri per renderla più generica. La definizione di criteri potrà quindi essere riusata per diversi scenari passando valori diversi (ad esempio specificando un set di località per una sottoscrizione) durante l'assegnazione dei criteri.

I parametri vengono definiti/creati quando si crea una definizione di criteri. Quando viene definito un parametro, vengono assegnati un nome e facoltativamente un valore. È ad esempio possibile definire un parametro come una località per un criterio e quindi assegnare al parametro diversi valori, ad esempio *EastUS* o *WestUS*, durante l'assegnazione di criteri.

Per altre informazioni sui parametri dei criteri, vedere [Parametri](../azure-resource-manager/resource-manager-policy.md#parameters) nella panoramica dei criteri delle risorse.

## <a name="initiative-definition"></a>Definizione di iniziativa
Una definizione di iniziativa è una raccolta di definizioni di criteri ottimizzate per il raggiungimento di un singolo obiettivo globale. Ad esempio, è possibile creare un'iniziativa denominata **Abilita monitoraggio nel Centro sicurezza di Azure** con l'obiettivo di monitorare tutte le raccomandazioni sulla sicurezza disponibili nel Centro sicurezza di Azure.

In questa iniziativa saranno incluse definizioni di criteri come le seguenti:

1. **Monitora database SQL non crittografato nel Centro sicurezza**, per il monitoraggio dei server e dei database SQL non crittografati.
2. **Monitora vulnerabilità del sistema operativo nel Centro sicurezza**, per il monitoraggio dei server che non soddisfano la baseline configurata.
3. **Monitora Endpoint Protection mancante nel Centro sicurezza**, per il monitoraggio dei server in cui non è installato un agente di Endpoint Protection.

## <a name="initiative-assignment"></a>Assegnazione di iniziativa
Analogamente a un'assegnazione di criteri, un'assegnazione di iniziativa è una definizione di iniziativa assegnata a un ambito specifico. Le assegnazioni di iniziativa riducono l'esigenza di creare diverse definizioni di iniziativa per ogni ambito. L'ambito potrebbe anche comprendere da un gruppo di gestione a un gruppo di risorse.

Nell'esempio precedente, l'iniziativa **Abilita monitoraggio nel Centro sicurezza di Azure** può essere assegnata a diversi ambiti. Ad esempio, un'assegnazione può essere associata a **subscriptionA** e un'altra a **subscriptionB**.

## <a name="initiative-parameters"></a>Parametri delle iniziative
Analogamente ai parametri dei criteri, i parametri delle iniziative consentono di semplificarne la gestione riducendo la ridondanza. I parametri delle iniziative sono essenzialmente l'elenco dei parametri usati dalle definizioni di criteri incluse nell'iniziativa.

Si consideri ad esempio uno scenario con una definizione di iniziativa, **initiativeC**, che include due definizioni di criteri, ognuna con un parametro definito:

|Criteri  |Nome del parametro     |Tipo di parametro  |Nota                                                                                                |
|--------|----------------------|-------|----------------------------------------------------------------------------------------------------------------|
|policyA |allowedLocations      |array  |Questo parametro prevede come valore un elenco di stringhe, perché il parametro è stato definito come di tipo matrice |
|policyB |allowedSingleLocation |string |Questo parametro prevede come valore una parola, perché il parametro è stato definito come di tipo stringa          |

In questo scenario, per la definizione dei parametri dell'iniziativa **initiativeC** sono disponibili tre opzioni:

1. Sfruttare i parametri delle definizioni di criteri incluse nell'iniziativa. In questo esempio, *allowedLocations* e *allowedSingleLocation* diventeranno i parametri dell'iniziativa **initiativeC**.
2. Specificare valori per i parametri delle definizioni di criteri incluse nella definizione di iniziativa. In questo esempio, è possibile specificare un elenco di località per il **parametro allowedLocations di policyA** e il **parametro allowedSingleLocation di policyB**.
È anche possibile specificare i valori quando si assegna l'iniziativa.
3. Specificare un elenco di opzioni di *valore* utilizzabili durante l'assegnazione dell'iniziativa. In questo modo, quando si assegna l'iniziativa, i parametri ereditati dalle definizioni di criteri incluse nell'iniziativa possono avere solo valori dell'elenco specificato.

Se l'elenco di opzioni di valore specificato al momento della creazione della definizione di iniziativa include *EastUS*, *WestUS*, *CentralUS* e *WestEurope*, ad esempio, durante l'assegnazione dell'iniziativa non sarà possibile immettere un valore diverso come *SoutheastAsia* perché non fa parte dell'elenco.

## <a name="recommendations-for-managing-policies"></a>Raccomandazioni per la gestione dei criteri

Durante la creazione e la gestione delle definizioni e delle assegnazioni di criteri, è consigliabile seguire alcune indicazioni:

- Se si creano definizioni di criteri nel proprio ambiente, è consigliabile iniziare con un effetto di controllo, anziché di rifiuto, per tenere traccia dell'impatto della definizione di criteri nell'ambiente. Se sono già presenti script per aumentare automaticamente le prestazioni delle applicazioni, l'impostazione di un effetto di rifiuto potrebbe impedire tali attività di automazione già implementate.
- Quando si creano le definizioni e le assegnazioni, è importante tenere presenti le gerarchie organizzative. È consigliabile creare le definizioni a un livello superiore, ad esempio a livello di gruppo di gestione o di sottoscrizione, e l'assegnazione al livello figlio successivo. Se si crea una definizione di criteri a livello di gruppo di gestione, ad esempio, è possibile limitare l'ambito di un'assegnazione di criteri di tale definizione a livello di sottoscrizione.
- È consigliabile usare il piano tariffario Standard, per comprendere meglio lo stato di conformità dell'ambiente.
- È consigliabile usare sempre definizioni di iniziativa invece di definizioni di criteri, anche se si intende creare un solo criterio. Se si crea la definizione di criteri *policyDefA* nella definizione di iniziativa *initiativeDefC* e si decide quindi di creare un'altra definizione di criteri con gli stessi obiettivi di *policyDefA*, ad esempio, è sufficiente aggiungerla in *initiativeDefC* tenendo così traccia di entrambe le definizioni in modo più efficace.

   Tenere presente che dopo la creazione di un'assegnazione di iniziativa da una definizione di iniziativa, tutte le nuove definizioni di criteri aggiunte alla definizione di iniziativa verranno automaticamente incluse nelle assegnazioni di iniziativa associate a tale definizione. Se nella nuova definizione di criteri è stato introdotto un nuovo parametro, tuttavia, è necessario aggiornare la definizione e le assegnazioni dell'iniziativa modificandole in modo da riflettere la modifica.

## <a name="next-steps"></a>Passaggi successivi:
Di seguito sono riportati i passaggi successivi consigliati dopo questa panoramica di Criteri di Azure e di alcuni concetti chiave introdotti:

- [Assegnare una definizione di criteri](./assign-policy-definition.md)
- [Assegnare una definizione di criteri con l'interfaccia della riga di comando di Azure](./assign-policy-definition-cli.md)
- [Assegnare una definizione di criteri con PowerShell](./assign-policy-definition-ps.md)
