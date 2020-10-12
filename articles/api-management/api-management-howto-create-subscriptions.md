---
title: Creare sottoscrizioni in Gestione API di Azure | Microsoft Docs
description: Informazioni su come creare sottoscrizioni in Gestione API di Azure. Una sottoscrizione è necessaria per ottenere le chiavi di sottoscrizione che consentono l'accesso alle API.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 191323a4c150c00c93245be35c9c8af381e26b42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87904861"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Creare sottoscrizioni in Gestione API di Azure

Quando si pubblicano le API tramite Gestione API di Azure, è semplice e comune proteggere l'accesso a tali API usando chiavi di sottoscrizione. Le applicazioni client che devono utilizzare le API pubblicate devono includere una chiave di sottoscrizione valida nelle richieste HTTP quando effettuano chiamate a tali API. Per ottenere una chiave di sottoscrizione per l'accesso alle API, è necessaria una sottoscrizione. Per altre informazioni sulle sottoscrizioni, vedere [sottoscrizioni in gestione API di Azure](api-management-subscriptions.md).

Questo articolo descrive i passaggi per la creazione di sottoscrizioni nel portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, i prerequisiti sono i seguenti:

+ [Creare un'istanza di gestione API](get-started-create-service-instance.md).
+ Comprendere le [sottoscrizioni in Gestione API](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Creare una nuova sottoscrizione

1. Selezionare **Sottoscrizioni** nel menu a sinistra.
2. Selezionare **Aggiungi sottoscrizione**.
3. Specificare il nome della sottoscrizione e selezionare l'ambito.
4. Facoltativamente, scegliere se la sottoscrizione deve essere associata a un utente.
5. Selezionare **Salva**.

![Sottoscrizioni flessibili](./media/api-management-subscriptions/flexible-subscription.png)

Dopo aver creato la sottoscrizione, vengono fornite due chiavi API per accedere alle API. Una è la chiave primaria e l'altra è la chiave secondaria. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Gestione API:

+ Scoprire altri [concetti](api-management-terminology.md) in Gestione API.
+ Seguire le [esercitazioni](import-and-publish.md) per altre informazioni su Gestione API.
+ Leggere le domande comuni nella [pagina delle domande frequenti](api-management-faq.md).