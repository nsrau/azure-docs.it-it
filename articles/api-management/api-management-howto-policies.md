---
title: Criteri in Gestione API di Azure | Microsoft Docs
description: Informazioni su come creare, modificare e configurare criteri in Gestione API. Vedere gli esempi di codice e visualizzare altre risorse disponibili.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 37ac6369790ed526fd923819558863ae84432aed
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358117"
---
# <a name="policies-in-azure-api-management"></a>Criteri in Gestione API di Azure

In Gestione API di Azure i criteri sono una potente funzionalità del sistema che consentono all'entità di pubblicazione di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale in caso di richiesta o risposta di un'API. Le istruzioni più comuni includono la conversione di formato da XML a JSON e la limitazione della frequenza delle chiamate per limitare la quantità di chiamate in ingresso da uno sviluppatore. Sono disponibili molti altri criteri predefiniti.

I criteri vengono applicati nel gateway che si trova tra il consumer di API e l'API gestita. Il gateway riceve tutte le richieste e in genere le inoltra invariate all'API sottostante. Tuttavia i criteri possono applicare modifiche sia alla richiesta in ingresso che alla risposta in uscita.

Le espressioni di criteri possono essere usate come valori di attributo o valori di testo in uno qualsiasi dei criteri di Gestione API, salvo diversamente specificato dai criteri. Alcuni criteri, come [choose][Control flow] e [set variable][Set variable], sono basati su espressioni di criteri. Per altre informazioni, vedere [criteri avanzati][Advanced policies] ed [espressioni di criteri][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Informazioni sulla configurazione dei criteri

La definizione criteri è un semplice documento XML che descrive una sequenza di istruzioni in ingresso e in uscita. Il codice XML può essere modificato direttamente nella finestra della definizione. Un elenco di istruzioni è disponibile a destra e le istruzioni applicabili all'ambito corrente sono abilitate ed evidenziate.

Facendo clic su un'istruzione abilitata, il codice XML appropriato verrà aggiunto in corrispondenza del cursore nella visualizzazione definizione. 

> [!NOTE]
> Se il criterio che si desidera aggiungere non è abilitato, verificare di essere nell'ambito corretto per il criterio. Ogni istruzione di criterio è progettata per essere usata in determinati ambiti e sezioni dei criteri. Per esaminare le sezioni dei criteri e gli ambiti di un criterio, controllare la sezione relativa all' **utilizzo** del criterio in [Riferimento ai criteri di Gestione API di Azure][Policy Reference].
> 
> 

La configurazione è divisa in `inbound`, `backend`, `outbound` e `on-error`. La serie di istruzioni dei criteri specificate viene eseguita in ordine per una richiesta e una risposta.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Se si verifica un errore durante l'elaborazione di una richiesta, tutti i rimanenti passaggi nelle sezioni `inbound`, `backend`, o `outbound` vengono ignorate e l'esecuzione prosegue con le istruzioni nella sezione`on-error`. Inserendo istruzioni di criteri nella sezione `on-error` è possibile rivedere l'errore utilizzando la proprietà `context.LastError`, esaminare e personalizzare la risposta di errore tramite il criterio `set-body` e configurare che cosa accade se si verifica un errore. Sono disponibili codici di errore per i passaggi incorporati e per gli errori che possono verificarsi durante l'elaborazione delle istruzioni dei criteri. Per altre informazioni, vedere [Gestione degli errori nei criteri di Gestione API](./api-management-error-handling-policies.md).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Come configurare i criteri

Per informazioni su come configurare i criteri, vedere [Impostare o modificare criteri](set-edit-policies.md).

## <a name="policy-reference"></a>Riferimento ai criteri

Per un elenco completo di istruzioni dei criteri e delle relative impostazioni, vedere [Informazioni di riferimento per i criteri](./api-management-policies.md).

## <a name="policy-samples"></a>Esempi di criteri

Per altri esempi di codice, vedere [Esempi di criteri](./policy-reference.md).

## <a name="examples"></a>Esempi

### <a name="apply-policies-specified-at-different-scopes"></a>Applicare i criteri specificati in ambiti diversi

Se ci sono un criterio a livello globale e un criterio configurato per un'API, quando questa particolare API viene usata, vengono applicati entrambi i criteri. Gestione API consente l'ordinamento deterministico delle istruzioni combinate per i criteri attraverso l'elemento di base. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

Nella definizione del criterio dell'esempio precedente, l'istruzione `cross-domain` verrà eseguita prima di un criterio di livello superiore che verrà a sua volta seguito dal criterio `find-and-replace`. 

### <a name="restrict-incoming-requests"></a>Limitare le richieste in ingresso

Per aggiungere una nuova istruzione per limitare le richieste in arrivo agli indirizzi IP specificati, posizionare il cursore nel contenuto dell'elemento XML `inbound` e fare clic sull'istruzione **Limita IP chiamanti**.

![Criteri di restrizione][policies-restrict]

Verrà aggiunto un frammento XML all'elemento `inbound` che fornisce informazioni aggiuntive sulla configurazione dell'istruzione.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Per limitare le richieste in ingresso e accettare solo quelle da un indirizzo IP 1.2.3.4, modificare il codice XML nel modo seguente:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di questi criteri, vedere:

+ [Trasformare le API](transform-api.md)
+ [Informazioni di riferimento sui criteri](./api-management-policies.md) per un elenco completo delle istruzioni dei criteri e delle relative impostazioni
+ [Esempi di criteri](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
