---
title: Sottoscrizioni in Gestione API di Azure | Microsoft Docs
description: Informazioni sul concetto di Sottoscrizione in Gestione API di Azure.
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
ms.openlocfilehash: 45a65c7a94f3e6917b2882f27c9ad7d764ef97d7
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621760"
---
# <a name="subscriptions-in-azure-api-management"></a>Sottoscrizioni in Gestione API di Azure

Le sottoscrizioni sono un concetto importante in Gestione API di Azure (APIM). È il modo più comune per i consumer delle API di ottenere l'accesso alle API pubblicate tramite un'istanza di gestione API di Azure. Questo articolo offre una panoramica sul concetto.

## <a name="what-is-subscriptions"></a>Cosa sono le sottoscrizioni

Quando si pubblicano le API tramite Gestione API di Azure, il modo più semplice e più comune per proteggere l'accesso a tali API consiste nell'usare chiavi di sottoscrizione. In altre parole, gli sviluppatori che devono usare le API pubblicate devono includere una chiave di sottoscrizione valida nelle richieste HTTP quando effettuano chiamate a tali API. In caso contrario, le chiamate vengono immediatamente rifiutate dal gateway di gestione API di Azure e non vengono inoltrate ai servizi back-end.

Per ottenere una chiave di sottoscrizione per l'accesso alle API, è necessaria una sottoscrizione. La sottoscrizione è essenzialmente un contenitore denominato per una coppia di chiavi di sottoscrizione. Gli sviluppatori che desiderano usare le API pubblicate possono ottenere le sottoscrizioni, con o senza l'approvazione da editori delle API. Gli editori delle API possono anche creare sottoscrizioni direttamente, per conto dei consumer delle API.

> [!TIP]
> Gestione API di Azure supporta anche altri meccanismi per proteggere l'accesso alle API, tra cui [OAuth 2.0](api-management-howto-protect-backend-with-aad.md), [i certificati dei client](api-management-howto-mutual-certificates-for-clients.md), e [l'inserimento nella elenco elementi IP consentiti](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Ambito delle sottoscrizioni

Le sottoscrizioni possono essere associate a vari ambiti: Prodotto, tutte le API o un'API singola.

### <a name="subscriptions-for-a-product"></a>Sottoscrizioni per un prodotto

In genere, le sottoscrizioni in Gestione API di Azure sono state sempre associate a un singolo ambito [prodotto API](api-management-terminology.md). Gli sviluppatori trovano l'elenco dei prodotti nel portale per sviluppatori e inviano le richieste di sottoscrizione per i prodotti che desiderano usare. Dopo che una richiesta di sottoscrizione è stata approvata (automaticamente o dagli editori delle API), lo sviluppatore può utilizzare le chiavi in essa per accedere a tutte le API nel prodotto.

![Sottoscrizioni prodotto](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> In determinate circostanze, gli editori delle API potrebbero voler pubblicare un prodotto API al pubblico senza l'obbligo di sottoscrizioni. È possibile deselezionare l'opzione **Richiedi sottoscrizione** nella pagina **Impostazioni** del prodotto nel portale di Azure. Di conseguenza, è possibile accedere a tutte le API del prodotto senza una chiave API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Sottoscrizioni per tutte le API o un'API singola

> [!NOTE]
> Attualmente questa funzione è disponibile solo nel livello di consumo di Gestione API.

Quando è stato introdotto il livello [Consumo](https://aka.ms/apimconsumptionblog) di Gestione API di Azure, sono state apportate alcune modifiche per semplificare la gestione delle chiavi. In primo luogo, sono stati aggiunti due altri ambiti delle sottoscrizioni: tutte le API e un'API singola. L'ambito delle sottoscrizioni non è più limitato a un prodotto API. È ora possibile creare chiavi concessione dell'accesso a un'API (o tutte le API all'interno di un'istanza di Gestione API di Azure), senza la necessità di creare un prodotto e aggiungervi le API prima di tutto. Inoltre, ogni istanza di Gestione API di Azure include ora una sottoscrizione non modificabile, Sottoscrizione tutte le API, che rende più semplice e più diretto testare ed eseguire il debug delle API all'interno della console di test.

In secondo luogo, Gestione API di Azure consente ora sottoscrizioni "autonome". Le sottoscrizioni non devono più essere associate a un account per sviluppatore. Ciò è utile negli scenari in cui ad esempio una sottoscrizione è condivisa da più sviluppatori o team.

Infine, gli editori delle API possono a questo punto [creare sottoscrizioni](api-management-howto-create-subscriptions.md) direttamente nel portale di Azure:

![Sottoscrizioni flessibili](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Gestione API:

+ Informazioni su altri [concetti](api-management-terminology.md) di Gestione API
+ Seguire le nostre [esercitazioni](import-and-publish.md) per altre informazioni su Gestione API
+ Leggere le domande comuni nella [pagina delle domande frequenti](api-management-faq.md)