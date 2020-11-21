---
title: Crittografia dei dati inattivi per il servizio vocale
titleSuffix: Azure Cognitive Services
description: Microsoft offre chiavi di crittografia gestite da Microsoft e consente inoltre di gestire le sottoscrizioni di servizi cognitivi con chiavi personalizzate, denominate chiavi gestite dal cliente (CMK). Questo articolo illustra la crittografia dei dati inattivi per il servizio di riconoscimento vocale.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: aa0fe33dff0161767b74546aad49003d8fc70c16
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015257"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Crittografia dei dati inattivi per il servizio vocale

Il servizio di riconoscimento vocale crittografa automaticamente i dati quando viene reso permanente nel cloud. La crittografia del servizio vocale protegge i dati e ti aiuta a soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

## <a name="about-cognitive-services-encryption"></a>Informazioni sulla crittografia di servizi cognitivi

I dati vengono crittografati e decrittografati usando la crittografia [AES a 256 bit](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) conforme allo standard [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . La crittografia e la decrittografia sono trasparenti, ovvero la crittografia e l'accesso vengono gestite per l'utente. I dati sono protetti per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare la crittografia.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Quando si usa Riconoscimento vocale personalizzato e una voce personalizzata, il servizio di riconoscimento vocale può archiviare i dati seguenti nel cloud:  

* Dati di traccia vocale: solo se l'utente attiva la traccia per l'endpoint personalizzato
* Dati di training e di test caricati

Per impostazione predefinita, i dati vengono archiviati nella risorsa di archiviazione di Microsoft e la sottoscrizione USA chiavi di crittografia gestite da Microsoft. È anche possibile preparare il proprio account di archiviazione. L'accesso all'archivio è gestito dall'identità gestita e il servizio di riconoscimento vocale non può accedere direttamente ai propri dati, ad esempio i dati di traccia vocale, i dati di training di personalizzazione e i modelli personalizzati.

Per altre informazioni sull'identità gestita, vedere [che cosa sono le identità gestite](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Bring your own Storage (BYOS) per la personalizzazione e la registrazione

Per richiedere l'accesso all'archiviazione, compilare e inviare il [modulo di richiesta BYOS (Bring your own Storage)](https://aka.ms/cogsvc-cmk). Una volta approvata, è necessario creare un proprio account di archiviazione per archiviare i dati necessari per la personalizzazione e la registrazione. Quando si aggiunge un account di archiviazione, la risorsa del servizio vocale Abilita un'identità gestita assegnata dal sistema. Quando l'identità gestita assegnata dal sistema è abilitata, questa risorsa verrà registrata con Azure Active Directory (AAD). Dopo la registrazione, all'identità gestita verrà concesso l'accesso all'account di archiviazione. Altre informazioni sulle identità gestite sono disponibili qui. Per altre informazioni sull'identità gestita, vedere [che cosa sono le identità gestite](../../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Se si disabilitano le identità gestite assegnate dal sistema, l'accesso all'account di archiviazione verrà rimosso. In questo modo, le parti del servizio di riconoscimento vocale che richiedono l'accesso all'account di archiviazione smetteranno di funzionare.  

Il servizio di riconoscimento vocale attualmente non supporta Customer Lockbox. Tuttavia, i dati dei clienti possono essere archiviati usando BYOS, consentendo di ottenere controlli dati simili per [Customer Lockbox](../../security/fundamentals/customer-lockbox-overview.md). Tenere presente che i dati del servizio vocale rimangono e vengono elaborati nell'area in cui è stata creata la risorsa di riconoscimento vocale. Questo vale per tutti i dati inattivi e i dati in transito. Quando si usano le funzionalità di personalizzazione, come Riconoscimento vocale personalizzato e la voce personalizzata, tutti i dati dei clienti vengono trasferiti, archiviati ed elaborati nella stessa area in cui risiedono le risorse di BYOS (se usate) e del servizio di riconoscimento vocale.

> [!IMPORTANT]
> Microsoft **non** usa i dati dei clienti per migliorare i propri modelli di riconoscimento vocale. Inoltre, se la registrazione dell'endpoint è disabilitata e non vengono utilizzate personalizzazioni, non vengono archiviati dati del cliente. 

## <a name="next-steps"></a>Passaggi successivi

* [Servizio di riconoscimento vocale-modulo di richiesta Bring your own Storage (BYOS)](https://aka.ms/cogsvc-cmk)
* [Che cosa sono le identità gestite](../../active-directory/managed-identities-azure-resources/overview.md).