---
title: Spostare le risorse di Azure in un'altra areaMove Azure resources to another region
description: Viene fornita una panoramica dello spostamento delle risorse di Azure tra aree di Azure.Provides an overview of moving Azure resources across Azure regions.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485208"
---
# <a name="moving-azure-resources-across-regions"></a>Spostamento di risorse di Azure tra aree

Questo articolo fornisce informazioni sullo spostamento di risorse di Azure tra aree di Azure.This article provides information about moving Azure resources across Azure regions.

Aree geografiche, aree e zone di disponibilità di Azure costituiscono la base dell'infrastruttura globale di Azure.Azure geographies, regions, and Availability zones form the base of the Azure global infrastructure. Le [aree geografiche](https://azure.microsoft.com/global-infrastructure/geographies/) di Azure contengono in genere due o più [aree di Azure.Azure](https://azure.microsoft.com/global-infrastructure/regions/)geographies typically contain two or more Azure regions . Un'area è un'area all'interno di un'area geografica contenente zone di disponibilità e più data center. 

Dopo aver distribuito le risorse in un'area di Azure specifica, è possibile spostare le risorse in un'area diversa per diversi.

- **Allinea all'avvio di un'area:** spostare le risorse in un'area di Azure appena introdotta che non era disponibile in precedenza.
- **Align for services/features**: Spostare le risorse per sfruttare i vantaggi dei servizi o delle funzionalità disponibili in un'area specifica.
- **Rispondere agli sviluppi aziendali**: spostare le risorse in un'area in risposta a modifiche aziendali, ad esempio fusioni o acquisizioni.
- **Allinea per prossimità**: Spostare le risorse in una regione locale per l'azienda.
- **Soddisfare i requisiti**dei dati : Spostare le risorse per allinearle ai requisiti di residenza dei dati o alle esigenze di classificazione dei dati. [Scopri di più](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Rispondere ai requisiti**di distribuzione : Spostare le risorse distribuite per errore o spostarsi in risposta alle esigenze di capacità. 
- **Rispondi allo smantellamento:** spostare le risorse a causa dello smantellamento delle aree.

## <a name="move-process"></a>Processo di spostamento

Il processo di spostamento effettivo dipende dalle risorse che si stanno spostando. Tuttavia, ci sono alcuni passaggi chiave comuni:

- **Verifica prerequisiti:** i prerequisiti includono la verifica che le risorse necessarie siano disponibili nell'area di destinazione, che si disponga di una quota sufficiente e che la sottoscrizione possa accedere all'area di destinazione.
- **Analizza dipendenze:** le risorse potrebbero avere dipendenze da altre risorse. Prima dello spostamento, individuare le dipendenze in modo che le risorse in movimento continuino a funzionare come previsto dopo lo spostamento.
- **Preparazione per**lo spostamento : Questi sono i passaggi da eseguire nella regione primaria prima dello spostamento. Ad esempio, potrebbe essere necessario esportare un modello di Azure Resource Manager o iniziare a replicare le risorse dall'origine alla destinazione.
- **Spostare le risorse**: La modalità di spostamento delle risorse dipende da quali sono. Potrebbe essere necessario distribuire un modello nell'area di destinazione o eseguire il failover delle risorse nella destinazione.
- **Elimina le risorse**di destinazione: dopo aver spostato le risorse, è possibile esaminare le risorse ora nell'area di destinazione e decidere se non è necessario.
- **Eseguire il commit dello spostamento:** dopo aver verificato le risorse nell'area di destinazione, alcune risorse potrebbero richiedere un'azione di commit finale. Ad esempio, in un'area di destinazione che è ora l'area primaria, potrebbe essere necessario impostare il ripristino di emergenza in una nuova area secondaria. 
- **Pulizia dell'origine:** infine, dopo che tutti gli elementi sono stati operativi nella nuova area, è possibile pulire e rimuovere le risorse create per lo spostamento e le risorse nell'area primaria.



## <a name="next-steps"></a>Passaggi successivi

Per un elenco delle risorse che supportano lo spostamento tra aree, vedere [Spostare il supporto delle operazioni per le risorse](region-move-support.md).
