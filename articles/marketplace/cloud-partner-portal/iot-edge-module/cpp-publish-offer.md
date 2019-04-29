---
title: Pubblicare l'offerta di modulo Azure IoT Edge | Microsoft Docs
description: Informazioni su come pubblicare un'offerta di modulo IoT Edge.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: dfa512a26334567301812ecefd8c5673b0ea4094
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60916163"
---
# <a name="publish-iot-edge-module-offer"></a>Pubblicare l'offerta di modulo IoT Edge

 Dopo avere creato una nuova offerta specificando le informazioni nella pagina **Nuova offerta**, è possibile pubblicarla. Selezionare **Pubblica** per avviare il processo di pubblicazione.

Il diagramma seguente illustra i passaggi principali del processo di pubblicazione che rende un'offerta disponibile in Azure Marketplace.

![Passaggi per la pubblicazione dell'offerta di modulo IoT Edge](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Descrizione dettagliata dei passaggi per la pubblicazione

La tabella seguente illustra ogni passaggio della pubblicazione e indica una stima dei tempi (massima) per completare ogni passaggio.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Passaggio per la pubblicazione**           | **Ora**    | **Descrizione**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Convalida dei prerequisiti         | 15 min   | Vengono convalidate le informazioni e le impostazioni dell'offerta.                        |
| Certificazione                  | 2 settimane | L'offerta viene analizzata dal team delle certificazioni di Azure. Questo passaggio esegue le analisi di virus, malware, conformità alle normative sulla sicurezza e problemi di sicurezza. Verifica anche che l'offerta di modulo IoT Edge soddisfi tutti i criteri di idoneità (vedere i [prerequisiti](./cpp-prerequisites.md) e [preparazione degli asset tecnici](./cpp-create-technical-assets.md)). Se viene rilevato un problema, viene fornito un feedback. |
| Packaging | 1 ora  | Le risorse tecniche dell'offerta vengono incluse in un pacchetto per uso dei clienti e i sistemi di registrazione di clienti potenziali vengono configurati e impostati. |
|  Consenso dell'editore             |  -        | Revisione finale dell'editore e conferma prima che l'offerta diventi disponibile in Azure Marketplace. È possibile distribuire l'offerta nelle sottoscrizioni selezionate (nei passaggi relativi alle informazioni sull'offerta) per verificare che soddisfi tutti i requisiti.  Selezionare **Go Live** in modo che l'offerta passi alla fase successiva. |
| Packaging                 | 1 ora | L'offerta finalizzata viene replicata nelle aree e nei sistemi di produzione del marketplace. | 
| Live                           | 4 giorni |L'offerta viene rilasciata, replicata nelle aree necessarie e resa disponibile al pubblico. |

Occorrono fino a 10 giorni lavorativi perché il processo di pubblicazione venga completato e l'offerta venga rilasciata. Dopo il completamento del processo di pubblicazione, l'offerta di modulo IoT Edge sarà disponibile nell'elenco di [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta di modulo IoT Edge esistente in Azure Marketplace](./cpp-update-existing-offer.md)
