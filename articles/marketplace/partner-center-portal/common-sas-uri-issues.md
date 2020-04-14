---
title: Problemi e correzioni comuni relativi all'URI di sicurezza di accesso condiviso - Azure MarketplaceCommon SAS URI issues and fixes - Azure Marketplace
description: Problemi comuni riscontrati e risoluzioni consigliate quando si lavora con le firme di accesso condiviso.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266240"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Problemi e correzioni comuni relativi all'URI di sicurezza di accesso condivisoCommon SAS URI issues and fixes

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte di Macchine virtuali di Azure dal portale Cloud Partner al Centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, seguire le istruzioni in Problemi comuni con [URI sAS e correzioni](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) per il portale Cloud Partner per gestire le offerte.

Di seguito sono riportati i problemi comuni riscontrati quando si lavora con le firme di accesso condiviso (utilizzate per identificare e condividere i dischi rigidi virtuali caricati per la soluzione), insieme alle risoluzioni suggerite.

| **Problema** | **Messaggio di errore** | **correzione** |
| --------- | ------------------- | ------- |
| *Errore durante la copia di immagini* |  |  |
| "?" non viene trovato nell'URI di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI di accesso condiviso usando gli strumenti consigliati. |
| Parametri "st" e "se" non nell'URI di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI della sessione di accesso condiviso con i valori di **Data di inizio** e Data di **fine** appropriati. |
| "sp-rl" non nell'URI di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI di `Read` accesso `List`condiviso con le autorizzazioni impostate come e . |
| L'URI di sAS contiene spazi vuoti nel nome del disco rigido virtualeSAS URI has white spaces in VHD name | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI di accesso condiviso per rimuovere gli spazi vuoti. |
| Errore di autorizzazione URI di accesso condiviso | `Failure: Copying Images. Not able to download blob due to authorization error.` | Rivedere e correggere il formato dell'URI di firma di accesso condiviso. Se necessario, rigenerarlo. |
| I parametri "st" e "se" dell'URI di sAS non hanno una specifica di data e ora completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | I parametri **Data di inizio** e Data di **fine** URI di accesso condiviso (e`st` `se` sottostringhe) devono avere un formato data/ora completo, ad `11-02-2017T00:00:00Z`esempio . Le versioni abbreviate non sono valide (alcuni comandi nell'interfaccia della riga di comando di Azure possono generare valori abbreviati per impostazione predefinita). |
|  |  |  |

Per informazioni [dettagliate,](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)vedere Utilizzo delle firme di accesso condiviso.
