---
title: Offerte del modulo IoT Edge di Azure Marketplace
description: Informazioni sulla pubblicazione di offerte di moduli IoT Edge in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/15/2020
ms.openlocfilehash: f7a4997117c8730b767f245050caa3f3157fb6fd
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750250"
---
# <a name="iot-edge-modules"></a>Moduli di IoT Edge

La piattaforma [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) è supportata da Microsoft Azure.  Questa piattaforma consente agli utenti di distribuire carichi di lavoro cloud da eseguire direttamente su dispositivi IoT.  Un modulo Azure IoT Edge può eseguire carichi di lavoro offline e analisi dei dati in locale. Questo tipo di offerta consente di risparmiare larghezza di banda, proteggere dati locali e sensibili e offre tempi di risposta a bassa latenza.  Sono ora disponibili opzioni per sfruttare i vantaggi di questi carichi di lavoro predefiniti. Fino ad ora erano disponibili solo un numero limitato di soluzioni prodotte direttamente da Microsoft.  Era necessario investire tempo e risorse nella creazione di soluzioni IoT personalizzate.

Con i [Moduli di Azure IoT Edge in Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1) è disponibile ora un'unica destinazione in cui gli editori possono presentare e vendere le proprie soluzioni ai destinatari IoT. Gli sviluppatori IoT possono infine trovare e acquistare funzionalità per accelerare lo sviluppo di soluzioni.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Vantaggi principali dei moduli di Azure IoT Edge in Azure Marketplace

| **Per gli editori**    | **Per i clienti (sviluppatori IoT)**  |
| :------------------- | :-------------------|
| Raggiungere milioni di sviluppatori che vogliono creare e distribuire soluzioni IoT Edge.  | Comporre una soluzione IoT Edge con la sicurezza di usare componenti sicuri e testati. |
| Pubblicare una sola volta ed eseguire su qualsiasi hardware IoT Edge che supporti i contenitori. | Ridurre il time-to-market individuando e distribuendo moduli IoT Edge prodotti direttamente e di terze parti per esigenze specifiche. |
| Monetizzazione con opzioni di fatturazione flessibili <ul> <li> Gratuito e BYOL (Bring Your Own License). </li> </ul> | Effettuare acquisti scegliendo il modello di fatturazione preferito. <ul> <li> Gratuito e BYOL (Bring Your Own License). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Che cosa è un modulo IoT Edge?

Azure IoT Edge consente di distribuire e gestire la logica di business sui dispositivi perimetrali sotto forma di moduli. I moduli di Azure IoT Edge sono le unità di calcolo più piccole gestite da IoT Edge e possono contenere i servizi di Microsoft, ad esempio analisi di flusso, servizi di terze parti o il codice specifico per la soluzione. Per altre informazioni sui moduli Azure IoT Edge, vedere [Informazioni sui moduli Azure IoT Edge](../iot-edge/iot-edge-modules.md).

**Qual è la differenza tra un tipo di offerta Contenitore e un tipo di offerta di modulo Azure IoT Edge?**

Il tipo di offerta di modulo Azure IoT Edge è un tipo specifico di contenitore in esecuzione in un dispositivo IoT Edge. Viene fornito con le impostazioni di configurazione predefinite per l'esecuzione nel contesto di IoT Edge e, facoltativamente, usa l'SDK del modulo Azure IoT Edge da integrare con il runtime di IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Pubblicare il modulo Azure IoT Edge

**Selezione dell'archivio online corretto**

I moduli Azure IoT Edge vengono pubblicati solo in Azure Marketplace, AppSource non è applicabile.  Per ulteriori informazioni sulle differenze e sui destinatari nei negozi online, vedere [determinare l'opzione di pubblicazione](determine-your-listing-type.md).
 
**Opzioni di fatturazione**

Per i moduli Azure IoT Edge il marketplace supporta attualmente le opzioni di fatturazione **Gratuito** e **BYOL (Bring Your Own License)** .
 
**Opzioni di pubblicazione**

In tutti i casi, i moduli Azure IoT Edge devono selezionare l'opzione di pubblicazione **Transazione**.  Vedere [Determinare l'opzione di pubblicazione](determine-your-listing-type.md) per altre informazioni sulle opzioni di pubblicazione.  

## <a name="eligibility-criteria"></a>Criteri di idoneità

Tutti i criteri e le condizioni dei contratti di Microsoft Azure Marketplace si applicano alle offerte di moduli Azure IoT Edge.  Per i moduli Azure IoT Edge esistono inoltre prerequisiti e requisiti tecnici.  

**Prerequisiti**

Per pubblicare un modulo Azure IoT Edge in Azure Marketplace, è necessario soddisfare i seguenti prerequisiti:

- Accesso al Centro per i partner. Per altre informazioni, vedere la [Guida alla pubblicazione per Azure Marketplace e AppSource](marketplace-publishers-guide.md).
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

I requisiti tecnici principali di un modulo Azure IoT Edge, affinché possa essere certificato e pubblicato in Azure Marketplace, sono descritti nel dettaglio in [Preparare le risorse tecniche del modulo Azure IoT Edge](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sul Marketplace](https://azuremarketplace.microsoft.com/sell) commerciale.
- Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare o completare l'offerta.
- [Creare un'offerta di modulo IOT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) nel centro per i partner.
