---
title: Sottoscrizioni in Gestione API di Azure | Microsoft Docs
description: Informazioni sul concetto di sottoscrizione in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: afd43bbf6f52f498ad8f56d5a48b960d45d84137
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243251"
---
# <a name="subscriptions-in-azure-api-management"></a>Sottoscrizioni in Gestione API di Azure

Le sottoscrizioni sono un concetto importante in Gestione API di Azure. Rappresentano il modo più comune per i consumer delle API di ottenere l'accesso alle API pubblicate tramite un'istanza di Gestione API. Questo articolo offre una panoramica sul concetto.

## <a name="what-are-subscriptions"></a>Cosa sono le sottoscrizioni?

Quando si pubblicano le API tramite Gestione API, è semplice e comune proteggere l'accesso a tali API usando chiavi di sottoscrizione. Gli sviluppatori che devono utilizzare le API pubblicate devono includere una chiave di sottoscrizione valida nelle richieste HTTP quando effettuano chiamate a tali API. In caso contrario, le chiamate vengono immediatamente rifiutate dal gateway di Gestione API e non vengono inoltrate ai servizi di back-end.

Per ottenere una chiave di sottoscrizione per l'accesso alle API, è necessaria una sottoscrizione. Una sottoscrizione è essenzialmente un contenitore denominato per una coppia di chiavi di sottoscrizione. Gli sviluppatori con l'esigenza di utilizzare le API pubblicate possono ottenere le sottoscrizioni e non necessitano dell'approvazione da parte degli editori delle API. Gli editori delle API possono anche creare sottoscrizioni direttamente, per i consumer delle API.

> [!TIP]
> Gestione API supporta anche altri meccanismi per proteggere l'accesso alle API, inclusi gli esempi seguenti:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Certificati client](api-management-howto-mutual-certificates-for-clients.md)
> - [Inserimento nell'elenco di IP consentiti](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Ambito delle sottoscrizioni

Le sottoscrizioni possono essere associate a vari ambiti: prodotto, tutte le API o un'API singola.

### <a name="subscriptions-for-a-product"></a>Sottoscrizioni per un prodotto

In genere, le sottoscrizioni in Gestione API sono state sempre associate a un singolo ambito [prodotto API](api-management-terminology.md). L'elenco dei prodotti è disponibile nel portale per sviluppatori. Gli sviluppatori devono quindi inviare le richieste di sottoscrizione per i prodotti che vogliono usare. Dopo l'approvazione di una richiesta di sottoscrizione, automaticamente o dagli editori di API, lo sviluppatore può utilizzare le chiavi in esso per accedere a tutte le API nel prodotto. Al momento, portale per sviluppatori Mostra solo le sottoscrizioni di ambito del prodotto nella sezione profili utente. 

![Sottoscrizioni prodotto](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> In determinate circostanze, gli editori delle API potrebbero voler pubblicare un prodotto API al pubblico senza l'obbligo di sottoscrizioni. In questo caso, è possibile deselezionare l'opzione **Richiedi sottoscrizione** nella pagina **Impostazioni** del prodotto nel portale di Azure. Di conseguenza, è possibile accedere a tutte le API del prodotto senza una chiave API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Sottoscrizioni per tutte le API o un'API singola

Quando è stato introdotto il livello [Consumo](https://aka.ms/apimconsumptionblog) di Gestione API, sono state apportate alcune modifiche per semplificare la gestione delle chiavi:
- In primo luogo, sono stati aggiunti due altri ambiti per le sottoscrizioni: tutte le API e un'API singola. L'ambito delle sottoscrizioni non è più limitato a un prodotto API. È ora possibile creare chiavi che concedono l'accesso a un'API (o a tutte le API all'interno di un'istanza di Gestione API), senza la necessità di creare un prodotto e aggiungervi le API prima di tutto. Inoltre, ogni istanza di Gestione API include ora una sottoscrizione non modificabile per tutte le API. Con questa sottoscrizione è più semplice e immediato testare ed eseguire il debug delle API all'interno della console di test.

- In secondo luogo, Gestione API consente ora sottoscrizioni **autonome**. Le sottoscrizioni non devono più essere associate a un account per sviluppatore. Questa funzionalità è utile negli scenari in cui, ad esempio, più sviluppatori o team condividono una sottoscrizione.

- Infine, gli editori delle API possono ora [creare sottoscrizioni](api-management-howto-create-subscriptions.md) direttamente nel portale di Azure:

    ![Sottoscrizioni flessibili](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Gestione API:

+ Scoprire altri [concetti](api-management-terminology.md) in Gestione API.
+ Seguire le [esercitazioni](import-and-publish.md) per altre informazioni su Gestione API.
+ Leggere le domande comuni nella [pagina delle domande frequenti](api-management-faq.md).
