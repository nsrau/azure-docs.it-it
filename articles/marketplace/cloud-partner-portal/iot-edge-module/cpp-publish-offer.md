---
title: Pubblicare l'offerta del modulo Azure IoT Edge | Azure Marketplace
description: Informazioni su come pubblicare un'offerta di modulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 2310d7a7cad16009bbb58469190a77eedb0619f8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813842"
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
| Convalida dei prerequisiti         | 15 minuti   | Vengono convalidate le informazioni e le impostazioni dell'offerta.                        |
| Certificazione                  | 2 settimane | L'offerta viene analizzata dal team delle certificazioni di Azure. Questo passaggio esegue le analisi di virus, malware, conformità con le normative sulla sicurezza e problemi di sicurezza. Verifica anche che l'offerta di modulo IoT Edge soddisfi tutti i criteri di idoneità (vedere i [prerequisiti](./cpp-prerequisites.md) e [preparazione degli asset tecnici](./cpp-create-technical-assets.md)). Se viene rilevato un problema, viene fornito un feedback. |
| Packaging | 1 ora  | Gli asset tecnici dell'offerta sono inclusi in un pacchetto per l'uso da parte dei clienti e i sistemi lead sono configurati e impostati. |
|  Consenso dell'editore             |  -        | Revisione finale dell'editore e conferma prima che l'offerta diventi disponibile in Azure Marketplace. È possibile distribuire l'offerta nelle sottoscrizioni selezionate (nei passaggi relativi alle informazioni sull'offerta) per verificare che soddisfi tutti i requisiti.  Selezionare **Go Live** in modo che l'offerta passi alla fase successiva. |
| Packaging                 | 1 ora | L'offerta finalizzata viene replicata nelle aree e nei sistemi di produzione del marketplace. | 
| Live                           | 4 giorni |L'offerta viene rilasciata, replicata nelle aree necessarie e resa disponibile al pubblico. |

Occorrono fino a 10 giorni lavorativi perché il processo di pubblicazione venga completato e l'offerta venga rilasciata. Dopo il completamento del processo di pubblicazione, l'offerta di modulo IoT Edge sarà disponibile nell'elenco di [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta di modulo IoT Edge esistente in Azure Marketplace](./cpp-update-existing-offer.md)
