---
title: Crittografia del servizio vocale dei dati inattivi
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372359"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Crittografia del servizio vocale dei dati inattivi

Servizio di riconoscimento vocale crittografa automaticamente i dati quando vengono resi persistenti nel cloud. La crittografia del servizio di riconoscimento vocale protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

## <a name="about-cognitive-services-encryption"></a>Informazioni sulla crittografia di Servizi cognitivi

I dati vengono crittografati e decrittografati utilizzando la crittografia [AES conforme](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Crittografia e decrittografia sono trasparenti, il che significa che la crittografia e l'accesso sono gestiti per voi. I dati sono al sicuro per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi della crittografia.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Quando si utilizza la voce personalizzata e la voce personalizzata, il servizio di riconoscimento vocale può archiviare i seguenti dati nel cloud:  

* Dati di traccia vocale: solo se si attiva la traccia per l'endpoint personalizzatoSpeech trace data - only if your turn the trace on for your custom endpoint
* Dati di training e test caricati

Per impostazione predefinita, i dati vengono archiviati nell'archivio di Microsoft e la sottoscrizione utilizza chiavi di crittografia gestite da Microsoft.By default, your data are stored in Microsoft's storage and your subscription uses Microsoft-managed encryption keys. È inoltre disponibile un'opzione per preparare il proprio account di archiviazione. L'accesso all'archivio è gestito dall'identità gestita e il servizio di riconoscimento vocale non può accedere direttamente ai propri dati, ad esempio dati di traccia vocale, dati di training di personalizzazione e modelli personalizzati.

Per ulteriori informazioni sull'identità gestita, vedere [Che cosa sono le identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Porta il tuo spazio di archiviazione (BYOS) per la personalizzazione e la registrazione

Per richiedere l'accesso per portare il proprio spazio di archiviazione, compilare e inviare il [servizio di riconoscimento vocale - portare il proprio modulo di richiesta di archiviazione (BYOS).](https://aka.ms/cogsvc-cmk) Dopo l'approvazione, è necessario creare il proprio account di archiviazione per archiviare i dati necessari per la personalizzazione e la registrazione. Quando si aggiunge un account di archiviazione, la risorsa del servizio di riconoscimento vocale consentirà un'identità gestita assegnata al sistema. Dopo aver abilitato l'identità gestita assegnata al sistema, questa risorsa verrà registrata con Azure Active Directory (AAD). Dopo la registrazione, all'identità gestita verrà concesso l'accesso all'account di archiviazione. Ulteriori informazioni sulle identità gestite sono disponibili qui. Per ulteriori informazioni sull'identità gestita, vedere [Che cosa sono le identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se si disabilitano le identità gestite assegnate al sistema, l'accesso all'account di archiviazione verrà rimosso. In questo modo le parti del servizio di riconoscimento vocale che richiedono l'accesso all'account di archiviazione smetteranno di funzionare.  

## <a name="regional-availability"></a>Disponibilità a livello di area

BYOS è attualmente disponibile in queste aree:

* Stati Uniti centro-meridionali
* Stati Uniti occidentali 2
* Stati Uniti orientali

## <a name="next-steps"></a>Passaggi successivi

* [Servizio vocale - porta il tuo modulo di richiesta di archiviazione (BYOS)](https://aka.ms/cogsvc-cmk)
* [Che cosa sono le identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
