---
title: Pubblica offerta di applicazione SaaS di Azure | Azure Marketplace
description: Processo e procedura di pubblicazione per un'offerta di un'applicazione SaaS in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 5574a7ba216eed42896d6fcb1890585f76561834
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258079"
---
# <a name="publish-a-saas-application-offer"></a>Pubblicare un'offerta per un'applicazione SaaS

Dopo avere creato una nuova offerta specificando le informazioni nella pagina **Nuova offerta**, è possibile pubblicarla. Selezionare **Pubblica** per avviare il processo di pubblicazione.

> [!IMPORTANT] 
> SaaS offrono funzionalità viene eseguita la migrazione per la [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Tutti i nuovi server di pubblicazione deve utilizzare Centro per i Partner per la creazione di nuove offerte SaaS e la gestione delle offerte esistenti.  I server di pubblicazione corrente con offerte SaaS batchwise la migrazione dal portale per Cloud Partner per il centro per i Partner.  Il portale Cloud Partner visualizzerà i messaggi di stato per indicare quando specifiche offerte esistenti sono state migrate.
> Per altre informazioni, vedere [creare una nuova offerta SaaS](../../partner-center-portal/create-new-saas-offer.md).


Il diagramma seguente illustra i passaggi generali per la pubblicazione di una nuova offerta per un'applicazione SaaS.

![Procedura di pubblicazione dell'offerta](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Descrizione dettagliata dei passaggi per la pubblicazione

La tabella seguente illustra ogni passaggio della pubblicazione e indica una stima dei tempi (massima) per completare ogni passaggio.

|     **Step**       |     **Ora**      |  **Descrizione**  |
|  ---------------   |  ---------------  |  ---------------  |
|         Certificazione           |       2 settimane            |          L'offerta viene analizzata dal team delle certificazioni di Azure. Questo passaggio esegue le analisi di virus, malware, conformità alle normative sulla sicurezza e problemi di sicurezza. Verificherà anche che questa offerta soddisfi tutti i criteri di idoneità (vedere [Prerequisiti](./cpp-prerequisites.md)). Se viene rilevato un problema, viene fornito un feedback.         |
|           Packaging         |       1 ora            |       Le risorse tecniche dell'offerta vengono incluse in un pacchetto per uso dei clienti e i sistemi di registrazione di clienti potenziali vengono configurati e impostati.            |
|        Consenso dell'editore            |         -          |        Revisione finale dell'editore e conferma prima che l'offerta diventi disponibile in Azure Marketplace. È possibile distribuire l'offerta nelle sottoscrizioni selezionate (nei passaggi relativi alle informazioni sull'offerta) per verificare che soddisfi tutti i requisiti. Selezionare **Go Live** in modo che l'offerta passi alla fase successiva.           |
|        Packaging            |        1 ora           |        L'offerta finalizzata viene replicata nelle aree e nei sistemi di produzione del marketplace.           |
|        Live            |       4 giorni            |         L'offerta viene rilasciata, replicata nelle aree necessarie e resa disponibile al pubblico.          |

Occorrono fino a 10 giorni lavorativi perché il processo di pubblicazione venga completato e l'offerta venga rilasciata. Al termine del processo di pubblicazione, l'offerta SaaS sarà disponibile nell'elenco di [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare un'offerta esistente](./cpp-update-existing-offer.md)
