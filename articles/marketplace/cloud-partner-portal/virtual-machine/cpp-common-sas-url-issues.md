---
title: Problemi di URL di firma di accesso condiviso comuni e correzioni per Azure Marketplace
description: Elenco dei problemi più comuni relativi all'uso di URI per la firma di accesso condiviso e possibili soluzioni.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 4f2770312624e1ca4c939ade458a451eb03f9d20
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938385"
---
# <a name="common-sas-url-issues-and-fixes"></a>Problemi comuni degli URL di firma di accesso condiviso e relative correzioni

Nella tabella seguente sono elencati alcuni dei problemi più comuni riscontrati durante l'utilizzo di firme di accesso condiviso, usate per identificare e condividere i dischi rigidi virtuali caricati per la soluzione, e le soluzioni suggerite.

| **Problema** | **Messaggio di errore** | **Correzione** | 
| --------- | ------------------- | ------- | 
| &emsp; *Errore durante la copia di immagini* |  |  |
| "?" non trovato nell'URL di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URL di firma di accesso condiviso con gli strumenti consigliati. |
| Parametri "st" e "se" non presenti nell'URL di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URL di firma di accesso condiviso con i valori **Data di inizio** e **Data di fine** corretti. | 
| "sp = rl" non presente nell'URL di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Aggiornare l'URL di firma di accesso condiviso con autorizzazioni `Read` e `List`. | 
| L'URL di firma di accesso condiviso contiene spazi nel nome del disco rigido virtuale | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URL di firma di accesso condiviso eliminando gli spazi vuoti. |
| Errore di autorizzazione dell'URL di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob due to authorization error` | Rivedere e correggere il formato dell'URI di firma di accesso condiviso. Se necessario, rigenerarlo. |
| L'indicazione di data e ora dei parametri "st" e "se" dell'URL di firma di accesso condiviso non è completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | URL di firma di accesso condiviso **data di inizio** e **data di fine** parametri (`st` e `se` sottostringhe) devono avere un formato di data/ora completa, ad esempio `11-02-2017T00:00:00Z`. Le versioni abbreviate non sono valide. Alcuni comandi dell'interfaccia della riga di comando di Azure generano valori abbreviati per impostazione predefinita. | 
|  |  |  |

Per altre informazioni, vedere [Uso delle firme di accesso condiviso](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
