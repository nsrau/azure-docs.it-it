---
title: Crittografia dei dati inattivi per il servizio vocale
titleSuffix: Azure Cognitive Services
description: Crittografia del servizio vocale dei dati inattivi.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372359"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Crittografia dei dati inattivi per il servizio vocale

Il servizio di riconoscimento vocale crittografa automaticamente i dati quando viene reso permanente nel cloud. La crittografia del servizio vocale protegge i dati e ti aiuta a soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

## <a name="about-cognitive-services-encryption"></a>Informazioni sulla crittografia di servizi cognitivi

I dati vengono crittografati e decrittografati usando la crittografia [AES a 256 bit](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) conforme allo standard [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . La crittografia e la decrittografia sono trasparenti, ovvero la crittografia e l'accesso vengono gestite per l'utente. I dati sono protetti per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi della crittografia.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Quando si usa Riconoscimento vocale personalizzato e una voce personalizzata, il servizio di riconoscimento vocale può archiviare i dati seguenti nel cloud:  

* Dati di traccia vocale: solo se l'utente attiva la traccia per l'endpoint personalizzato
* Dati di training e di test caricati

Per impostazione predefinita, i dati vengono archiviati nella risorsa di archiviazione di Microsoft e la sottoscrizione USA chiavi di crittografia gestite da Microsoft. È anche possibile preparare il proprio account di archiviazione. L'accesso all'archivio è gestito dall'identità gestita e il servizio di riconoscimento vocale non può accedere direttamente ai propri dati, ad esempio i dati di traccia vocale, i dati di training di personalizzazione e i modelli personalizzati.

Per altre informazioni sull'identità gestita, vedere [che cosa sono le identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Bring your own Storage (BYOS) per la personalizzazione e la registrazione

Per richiedere l'accesso all'archiviazione, compilare e inviare il [modulo di richiesta BYOS (Bring your own Storage)](https://aka.ms/cogsvc-cmk). Una volta approvata, è necessario creare un proprio account di archiviazione per archiviare i dati necessari per la personalizzazione e la registrazione. Quando si aggiunge un account di archiviazione, la risorsa del servizio vocale Abilita un'identità gestita assegnata dal sistema. Quando l'identità gestita assegnata dal sistema è abilitata, questa risorsa verrà registrata con Azure Active Directory (AAD). Dopo la registrazione, all'identità gestita verrà concesso l'accesso all'account di archiviazione. Altre informazioni sulle identità gestite sono disponibili qui. Per altre informazioni sull'identità gestita, vedere [che cosa sono le identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se si disabilitano le identità gestite assegnate dal sistema, l'accesso all'account di archiviazione verrà rimosso. In questo modo, le parti del servizio di riconoscimento vocale che richiedono l'accesso all'account di archiviazione smetteranno di funzionare.  

## <a name="regional-availability"></a>Disponibilità a livello di area

BYOS è attualmente disponibile nelle aree geografiche seguenti:

* Stati Uniti centro-meridionali
* Stati Uniti occidentali 2
* Stati Uniti orientali

## <a name="next-steps"></a>Passaggi successivi

* [Servizio di riconoscimento vocale-modulo di richiesta Bring your own Storage (BYOS)](https://aka.ms/cogsvc-cmk)
* [Che cosa sono le identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
