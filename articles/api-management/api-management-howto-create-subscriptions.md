---
title: Creare sottoscrizioni in Gestione API di Azure | Microsoft Docs
description: Informazioni su come creare sottoscrizioni in Gestione API di Azure.
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
ms.openlocfilehash: 1393e548c46c23f6b50c1b18a274febb74914ae8
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054503"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Creare sottoscrizioni in Gestione API di Azure

Quando si pubblicano le API tramite Gestione API di Azure, è semplice e comune proteggere l'accesso a tali API usando chiavi di sottoscrizione. Le applicazioni client che devono utilizzare le API pubblicate devono includere una chiave di sottoscrizione valida nelle richieste HTTP quando effettuano chiamate a tali API. Per ottenere una chiave di sottoscrizione per l'accesso alle API, è necessaria una sottoscrizione. Per altre informazioni sulle sottoscrizioni, vedere [Sottoscrizioni in Gestione API di Azure](api-management-subscriptions.md).

Questo articolo descrive i passaggi per la creazione di sottoscrizioni nel portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, i prerequisiti sono i seguenti:

+ [Creare un'istanza di Gestione API](get-started-create-service-instance.md).
+ Comprendere le [sottoscrizioni in Gestione API](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Creare una nuova sottoscrizione

1. Selezionare **Sottoscrizioni** nel menu a sinistra.
2. Selezionare **Aggiungi sottoscrizione**.
3. Specificare il nome della sottoscrizione e selezionare l'ambito.
4. Selezionare **Salva**.

![Sottoscrizioni flessibili](./media/api-management-subscriptions/flexible-subscription.png)

Dopo aver creato la sottoscrizione, vengono fornite due chiavi API per accedere alle API. Una è la chiave primaria e l'altra è la chiave secondaria. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Gestione API:

+ Scoprire altri [concetti](api-management-terminology.md) in Gestione API.
+ Seguire le [esercitazioni](import-and-publish.md) per altre informazioni su Gestione API.
+ Leggere le domande comuni nella [pagina delle domande frequenti](api-management-faq.md).