---
title: BuildingBlocks - Azure Active Directory B2C | Microsoft Docs
description: Specificare l'elemento BuildingBlocks di un criterio personalizzato di Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: afa064232c10d3e84e9c301d44b82faae4904253
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60383957"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L'elemento **BuildingBlocks** viene aggiunto all'interno dell'elemento [TrustFrameworkPolicy](trustframeworkpolicy.md).

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
 </BuildingBlocks>
```

L'elemento **BuildingBlocks** contiene gli elementi seguenti, che devono essere specificati nell'ordine definito:

- [ClaimsSchema](claimsschema.md): definisce i tipi di attestazione a cui è possibile fare riferimento nei criteri. Lo schema di attestazioni è il posto in cui si dichiarano i tipi di attestazioni. Un tipo di attestazione è simile a una variabile in molti linguaggi di programmazione. È possibile usare il tipo di attestazione per raccogliere i dati dell'utente dell'applicazione, ricevere le attestazioni dai provider di identità di social networking, inviare e ricevere dati da un'API REST personalizzata o per archiviare dati interni usati dai criteri personalizzati. 

- [Predicates and PredicateValidationsInput](predicates.md): consente di eseguire un processo di convalida per garantire l'inserimento dei soli dati formattati adeguatamente in un'attestazione.
 
- [ClaimsTransformations](claimstransformations.md): contiene un elenco di trasformazioni di attestazioni che possono essere usati nei criteri.  Una trasformazione di attestazioni converte una determinata attestazione in un'altra. Nella trasformazione delle attestazioni si specifica un metodo di trasformazione, ad esempio: 
    - Modifica delle maiuscole/minuscole di un'attestazione di stringa rispetto a quella specificata. Ad esempio è possibile cambiare le lettere di una stringa da minuscole a maiuscole.
    - Confronto tra due attestazioni e restituzione di un'attestazione con true che indica che le attestazioni corrispondono, in caso contrario si avrà false.
    - Creazione di un'attestazione di tipo stringa dal parametro fornito nei criteri.
    - Creazione di una stringa casuale tramite il generatore di numeri casuali.
    - Formattazione di un'attestazione in base alla stringa formato fornita. Questa trasformazione usa il metodo C# `String.Format`.

- [ContentDefinitions](contentdefinitions.md): contiene gli URL per i modelli HTML5 da usare nel percorso utente. Nei criteri personalizzati una definizione del contenuto indica l'URI della pagina HTML5 usato per un passaggio del percorso utente. Ad esempio, le pagine di accesso o iscrizione, reimpostazione della password o di errore. Nei criteri di estensione è possibile modificare l'aspetto eseguendo l'override del LoadUri per il file HTML5. Oppure è possibile creare nuove definizioni del contenuto in base alle esigenze. Questo elemento può contenere un riferimento alle risorse localizzate tramite un ID di localizzazione.

- [Localization](localization.md): consente di supportare più lingue. Il supporto di localizzazione nei criteri consente di impostare l'elenco delle lingue supportate in un criterio e di selezionare una lingua predefinita. Sono inoltre supportate stringhe e raccolte specifiche della lingua.


