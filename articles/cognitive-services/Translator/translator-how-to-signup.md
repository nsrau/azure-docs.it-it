---
title: Introduzione-Translator
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come iscriversi al programma di traduzione dei servizi cognitivi di Azure e ottenere una chiave di sottoscrizione.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 1a26b00a95b1b71993385e816d06d6686f89a5a1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599546"
---
# <a name="how-to-sign-up-for-translator"></a>Come iscriversi a Translator

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

- Non si dispone di un account? È possibile creare un [account gratuito](https://azure.microsoft.com/free/) per una prova senza addebiti.
- Si dispone già di un account? [Accedi](https://ms.portal.azure.com/)

## <a name="create-a-subscription-for-translator"></a>Creare una sottoscrizione per Translator

Dopo aver eseguito l'accesso al portale, è possibile creare una sottoscrizione a Translator come indicato di seguito:

1. Selezionare **+ Crea una risorsa**.
1. Nella casella Cerca nel **Marketplace** immettere **Translator** , quindi selezionarlo dai risultati.
1. Selezionare **Create** (Crea) per definire i dettagli della sottoscrizione.
1. Nell'elenco **Pricing tier** (Piano tariffario) selezionare il piano tariffario più adatto alle proprie esigenze.
    1. A ogni sottoscrizione è associato un piano gratuito. Il piano gratuito offre le stesse caratteristiche e funzionalità dei piani a pagamento e non ha scadenza.
    1. È possibile creare una sola sottoscrizione gratuita per account.
1. Selezionare **Create** (Crea) per completare la creazione della sottoscrizione.

## <a name="authentication-key"></a>Chiave di autenticazione

Quando si esegue l'iscrizione a translator, si ottiene una chiave di accesso personalizzata univoca per la sottoscrizione. Questa chiave è obbligatoria per ogni chiamata al convertitore.

1. Recuperare la chiave di autenticazione selezionando la sottoscrizione appropriata.
1. Selezionare **Keys** (Chiavi) nella sezione **Resource Management** (Gestione risorse) dei dettagli della sottoscrizione.
1. Copiare una delle chiavi elencate per la sottoscrizione.

## <a name="learn-test-and-get-support"></a>Apprendere, testare e ottenere supporto

- [Esempi di codice in GitHub](https://github.com/MicrosoftTranslator)
- [Forum di supporto su Microsoft Translator](https://www.aka.ms/TranslatorForum)

Microsoft Translator elabora in genere le prime due richieste senza verificare lo stato dell'account di sottoscrizione. Se le prime richieste del traduttore hanno esito positivo, le chiamate hanno esito negativo, la risposta di errore indicherà il problema. Registrare la risposta dell'API per poter visualizzare il motivo.

## <a name="pricing-options"></a>Opzioni di prezzo

- [Traduttore](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>Personalizzazione

Usare Custom Translator (Traduttore personalizzato) per personalizzare le traduzioni e creare un sistema di traduzioni adattato al proprio stile e alla propria terminologia, partendo da sistemi di traduzione automatica neurali di Microsoft Translator generici. [Altre informazioni](customization.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Introduzione ad Azure (video di 3 minuti)](https://azure.microsoft.com/get-started/?b=16.24)
- [Pagare una sottoscrizione di Azure tramite fattura](https://azure.microsoft.com/pricing/invoicing/)
