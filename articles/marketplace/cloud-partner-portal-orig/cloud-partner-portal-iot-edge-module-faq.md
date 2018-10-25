---
title: Domande frequenti sul Marketplace del modulo IoT Edge | Microsoft Docs
description: Domande frequenti sul Marketplace del modulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807467"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>Domande frequenti sul Marketplace del modulo IoT Edge


## <a name="what-is-the-edge-module-marketplace"></a>Cos'è il Marketplace del modulo IoT Edge?


Il marketplace del modulo IoT Edge è un elenco dei moduli Edge esistenti *certificati* che possono essere *individuati* tramite Azure Marketplace.

![Moduli IoT Edge](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Dove possono essere visualizzati i moduli Edge? 


Nel [marketplace del portale di Azure](https://ms.portal.azure.com/) (esperienza di autenticazione), nella categoria Internet delle cose, contrassegnati come "Modulo IoT Edge".

E nel [sito Web di Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1) (esperienza anonima), nella categoria Internet delle cose, contrassegnati come "Modulo IoT Edge".

## <a name="is-it-open-to-partners"></a>È aperto ai partner?


Per il momento no. Attualmente, solo i moduli creati da Microsoft vengono pubblicati nella sezione IoT Edge del marketplace. 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>Perché si consiglia ai partner di pubblicare i moduli IoT Edge?


-   Per aumentare la visibilità dei prodotti, aggiungendo questo canale per commercializzare e presentare soluzioni.

-   Per ottenere più lead. Facendo parte di Azure Marketplace, è possibile ottenere informazioni dettagliate su chi è interessato alla soluzione.

-   Per essere i primi a sfruttare altre funzionalità di monetizzazione.

## <a name="what-is-the-onboarding-process"></a>Che cos'è il processo di onboarding?


Pur non essendo ancora aperto, il processo di onboarding viene eseguito tramite Azure Marketplace. Le linee guida dettagliate si trovano in [Guida alla pubblicazione per Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide). Consultare il tipo per l'inclusione in elenco transact per Contenitori. 

Prima di tutto è necessario che i partner diventino editori di Azure Marketplace. Successivamente sarà possibile caricare i moduli Edge tramite il [portale Cloud Partner](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md).

## <a name="are-there-any-monetization-capabilities"></a>Sono disponibili funzionalità di monetizzazione?


Al momento non fuori dalla casella. Il primo obiettivo consiste nell'aprire un marketplace con moduli Edge *gratuiti* oppure *Bring Your Own License*. Verranno aggiunte altre funzionalità di monetizzazione, ad esempio un modello di fatturazione a consumo.

## <a name="what-is-bring-your-own-license-byol"></a>Cos'è BYOL (Bring Your Own License)?


La definizione ufficiale in [Criteri di partecipazione a Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) è:

- *Al di fuori di Marketplace, i clienti ottengono il diritto di accedere o usare l'offerta e non pagano alcun corrispettivo Azure Marketplace per l'uso dell'offerta di Azure Marketplace.*

Spetta ai partner concedere la licenza d'uso per il software e riscuoterne le entrate.

## <a name="can-partners-publish-a-freemium-module"></a>I partner possono pubblicare un modulo "freemium"?


Sì, moduli freemium, ovvero ai moduli disponibili gratuitamente, ma con alcune limitazioni, che verranno considerati come le altre pubblicazioni.

## <a name="is-intellectual-property-protected"></a>La proprietà intellettuale è protetta?


Un modulo Edge è un contenitore Docker compatibile. Spetta ai partner proteggere la proprietà intellettuale (IP) crittografata nei contenitori distribuiti.

## <a name="where-will-the-modules-be-hosted"></a>Dove vengono ospitati i moduli?


I moduli IoT Edge vengono ospitati in un registro contenitori di proprietà Microsoft, che può essere sottoposto a query in modo anonimo, ad esempio Docker Hub.

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>Quali sono i piani di integrazione tra gli strumenti di Marketplace e quelli di Azure IoT?

Verrà creata un'integrazione più precisa tra gli strumenti di Marketplace e quelli di Azure IoT. Abilitare l'esplorazione del marketplace del modulo IoT Edge direttamente dal portale hub IoT o direttamente dal Visual Studio Code rappresenta un esempio di ciò che si vuole realizzare.

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>Quale sistema operativo o architettura deve supportare il contenitore?

I moduli IoT Edge devono supportare lo stesso sistema operativo/matrice dell'architettura di IoT Edge nell'ambiente di produzione. Questo elenco viene mantenuto nel [Supporto di Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support). Ad esempio,attualmente un modulo deve supportare Linux x64 e Linux ARM32.

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>Esistono altri vincoli di certificazione?

Stiamo lavorando sul set esatto di vincoli di certificazione. Ecco alcuni dei principi guida:

-   Prediligere la qualità rispetto alla quantità.

-   Contenitori specifici per IoT Edge (non casuali).

-   Livello di produzione.

-   1-fare clic su distribuzione (almeno con un set di valori di configurazione predefiniti forniti).

## <a name="any-other-questions"></a>Altre domande?


Contattare [Azure IoT Edge on-boarding](mailto:azureiotedgeonboarding@service.microsoft.com).
