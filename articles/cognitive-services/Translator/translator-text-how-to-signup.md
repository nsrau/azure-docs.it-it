---
title: Introduzione - API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire la registrazione all'API Traduzione testuale e ottenere una chiave di sottoscrizione.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 011e1db141c36c44ef51edc62d0123fdf3c036c7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091562"
---
# <a name="how-to-sign-up-for-the-translator-text-api"></a>Come registrarsi all'API Traduzione testuale

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

- Non si dispone di un account? È possibile creare un [account gratuito](https://azure.microsoft.com/free/) per una prova senza addebiti.
- Si dispone già di un account? [Accesso](https://ms.portal.azure.com/)

## <a name="create-a-subscription-to-the-translator-text-api"></a>Creare una sottoscrizione all'API Traduzione testuale

Dopo aver effettuato l'accesso al portale, è possibile creare una sottoscrizione all'API Traduzione testuale come indicato di seguito:

1. Selezionare **+ Crea una risorsa**.
1. Nella casella di ricerca **Search the Marketplace** (Cerca nel marketplace) immettere **Translator Text** (Traduzione testuale) e selezionarla dai risultati.
1. Selezionare **Create** (Crea) per definire i dettagli della sottoscrizione.
1. Nell'elenco **Pricing tier** (Piano tariffario) selezionare il piano tariffario più adatto alle proprie esigenze.
    1. A ogni sottoscrizione è associato un piano gratuito. Il piano gratuito offre le stesse caratteristiche e funzionalità dei piani a pagamento e non ha scadenza.
    1. È possibile creare una sola sottoscrizione gratuita per account.
1. Selezionare **Create** (Crea) per completare la creazione della sottoscrizione.

## <a name="authentication-key"></a>Chiave di autenticazione

Quando si effettua la registrazione a Traduzione testuale, si ottiene una chiave di accesso personalizzata specifica della propria sottoscrizione. Questa chiave è necessaria per ogni chiamata all'API Traduzione testuale.

1. Recuperare la chiave di autenticazione selezionando la sottoscrizione appropriata.
1. Selezionare **Keys** (Chiavi) nella sezione **Resource Management** (Gestione risorse) dei dettagli della sottoscrizione.
1. Copiare una delle chiavi elencate per la sottoscrizione.

## <a name="learn-test-and-get-support"></a>Apprendere, testare e ottenere supporto

- [Esempi di codice in GitHub](https://github.com/MicrosoftTranslator)
- [Forum di supporto su Microsoft Translator](https://www.aka.ms/TranslatorForum)

Microsoft Translator elabora in genere le prime due richieste senza verificare lo stato dell'account di sottoscrizione. Se le prime richieste per l'API Microsoft Translator hanno esito positivo e quindi le chiamate hanno esito negativo, la risposta di errore indicherà il problema. Registrare la risposta dell'API per poter visualizzare il motivo.

## <a name="pricing-options"></a>Opzioni di prezzo

- [API Traduzione testuale](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>Personalizzazione

Usare Custom Translator (Traduttore personalizzato) per personalizzare le traduzioni e creare un sistema di traduzioni adattato al proprio stile e alla propria terminologia, partendo da sistemi di traduzione automatica neurali di Microsoft Translator generici. [Altre informazioni](customization.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Introduzione ad Azure (video di 3 minuti)](https://azure.microsoft.com/get-started/?b=16.24)
- [Modalità di pagamento con fattura](https://azure.microsoft.com/pricing/invoicing/)
