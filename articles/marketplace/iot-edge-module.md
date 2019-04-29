---
title: Moduli di Azure IoT Edge
description: L'offerta Moduli di Azure IoT Edge in Azure Marketplace per gli editori di app e servizi.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/22/2018
ms.author: qianw211
ms.openlocfilehash: f13d3f780e24d71babb1bda48a9b85d1208e3c4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709298"
---
# <a name="iot-edge-modules"></a>Moduli di IoT Edge

La piattaforma [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) è supportata da Azure Cloud.  Questa piattaforma consente agli utenti di distribuire carichi di lavoro cloud da eseguire direttamente su dispositivi IoT.  Un modulo Azure IoT Edge può eseguire carichi di lavoro offline e analisi dei dati in locale. Questo tipo di offerta consente di risparmiare larghezza di banda, proteggere dati locali e sensibili e offre tempi di risposta a bassa latenza.  Sono ora disponibili opzioni per sfruttare i vantaggi di questi carichi di lavoro predefiniti. Fino ad ora erano disponibili solo un numero limitato di soluzioni prodotte direttamente da Microsoft.  Era necessario investire tempo e risorse nella creazione di soluzioni IoT personalizzate.

Con l'introduzione dei [Moduli di Azure IoT Edge in Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1) è disponibile ora un'unica destinazione in cui gli editori possono presentare e vendere le proprie soluzioni ai destinatari IoT. Gli sviluppatori IoT possono infine trovare e acquistare funzionalità per accelerare lo sviluppo di soluzioni.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Vantaggi principali dei moduli di Azure IoT Edge in Azure Marketplace:

| **Per gli editori**    | **Per i clienti (sviluppatori IoT)**  |
| :------------------- | :-------------------|
| Raggiungere milioni di sviluppatori che vogliono creare e distribuire soluzioni IoT Edge.  | Comporre una soluzione IoT Edge con la sicurezza di usare componenti sicuri e testati. |
| Pubblicare una sola volta ed eseguire su qualsiasi hardware IoT Edge che supporti i contenitori. | Ridurre il time-to-market individuando e distribuendo moduli IoT Edge prodotti direttamente e di terze parti per esigenze specifiche. |
| Monetizzazione con opzioni di fatturazione flessibili <ul> <li> Gratuito e BYOL (Bring Your Own License). </li> </ul> | Effettuare acquisti scegliendo il modello di fatturazione preferito. <ul> <li> Gratuito e BYOL (Bring Your Own License). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Che cosa è un modulo IoT Edge?

Azure IoT Edge consente di distribuire e gestire la logica di business sui dispositivi perimetrali sotto forma di moduli. I moduli di Azure IoT Edge sono le unità di calcolo più piccole gestite da IoT Edge e possono contenere i servizi di Microsoft, ad esempio analisi di flusso, servizi di terze parti o il codice specifico per la soluzione. Per altre informazioni sui moduli Azure IoT Edge, vedere [Informazioni sui moduli Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Qual è la differenza tra un tipo di offerta Contenitore e un tipo di offerta di modulo Azure IoT Edge?**

Il tipo di offerta di modulo Azure IoT Edge è un tipo specifico di contenitore in esecuzione in un dispositivo IoT Edge. Viene fornito con le impostazioni di configurazione predefinite per l'esecuzione nel contesto di IoT Edge e, facoltativamente, usa l'SDK del modulo Azure IoT Edge da integrare con il runtime di IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Pubblicare il modulo Azure IoT Edge

**Selezione della vetrina corretta**

I moduli Azure IoT Edge vengono pubblicati solo in Azure Marketplace, AppSource non è applicabile.  Per altre informazioni sulle differenze e sui destinatari delle vetrine, vedere [Determinare l'opzione di pubblicazione per la soluzione](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Opzioni di fatturazione**

Per i moduli Azure IoT Edge il marketplace supporta attualmente le opzioni di fatturazione **Gratuito** e **BYOL (Bring Your Own License)**.
 
**Opzioni di pubblicazione**

In tutti i casi, i moduli Azure IoT Edge devono selezionare l'opzione di pubblicazione **Transazione**.  Vedere [Determinare l'opzione di pubblicazione](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) per altre informazioni sulle opzioni di pubblicazione.  

## <a name="eligibility-criteria"></a>Criteri di idoneità

Tutti i criteri e le condizioni dei contratti di Microsoft Azure Marketplace si applicano alle offerte di moduli Azure IoT Edge.  Per i moduli Azure IoT Edge esistono inoltre prerequisiti e requisiti tecnici.  

**Prerequisiti**

Per pubblicare un modulo Azure IoT Edge in Azure Marketplace, è necessario soddisfare i seguenti prerequisiti:

- Ottenere l'accesso al portale Cloud Partner (CPP). Per altre informazioni, vedere la [Guida alla pubblicazione per Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Ospitare il modulo Azure IoT Edge in un'istanza di Registro Azure Container. 
- Avere a pronti i metadati del modulo Azure IoT Edge, come i seguenti (elenco non completo): 
    - Un titolo
    - Una descrizione (in formato HTML)
    - Un'immagine del logo (formato PNG e dimensioni delle immagini fisse, tra cui 40x40px, 90x90px, 115x115px, 255x115px)
    - Informativa sulla privacy e delle condizioni per l'utilizzo
    - Configurazione predefinita del modulo (route, le proprietà desiderate del dispositivo gemello, createOptions, variabili di ambiente)
    - Documentazione
    - Contatti del supporto tecnico

**Requisiti tecnici**

I requisiti tecnici principali di un modulo Azure IoT Edge, affinché possa essere certificato e pubblicato in Azure Marketplace, sono descritti nel dettaglio in [Preparare le risorse tecniche del modulo Azure IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets).  

## <a name="documentation-and-resources"></a>Documentazione e risorse

[Creare un'offerta di modulo Azure IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer): i passaggi per la pubblicazione di un nuovo modulo Azure IoT Edge con il portale di pubblicazione Cloud.

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto,

- Eseguire la registrazione a [Microsoft Partner Network](https://partner.microsoft.com/membership).
- Creare un [account Microsoft](https://account.microsoft.com/account/)(obbligatorio per le offerte di transazione di Azure Marketplace, consigliato per le altre).
- Inviare il [modulo di registrazione a Marketplace](https://azuremarketplace.microsoft.com/sell/signup).

Se la registrazione è già stata effettuata e si sta creando una nuova offerta o lavorando su una esistente,

- Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/) per creare o completare l'offerta.
- Vedere [Panoramica della pubblicazione dell'offerta di modulo IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) per informazioni su come pubblicare un'offerta di modulo IoT Edge.
