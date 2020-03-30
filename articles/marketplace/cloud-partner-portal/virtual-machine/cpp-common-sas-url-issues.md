---
title: Problemi e correzioni comuni relativi agli URL di archiviazione di rete per Azure MarketplaceCommon SAS URL issues and fixes for the Azure Marketplace
description: Elenco dei problemi più comuni relativi all'uso di URI per la firma di accesso condiviso e possibili soluzioni.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 47702959474a352a8e13710ec850f789dee4d517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278162"
---
# <a name="common-sas-url-issues-and-fixes"></a>Problemi comuni degli URL di firma di accesso condiviso e relative correzioni

Nella tabella seguente sono elencati alcuni dei problemi più comuni riscontrati durante l'utilizzo di firme di accesso condiviso, usate per identificare e condividere i dischi rigidi virtuali caricati per la soluzione, e le soluzioni suggerite.

| **Problema** | **Messaggio di errore** | **correzione** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Errore durante la copia di immagini* |  |  |
| "?" non trovato nell'URL di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URL di firma di accesso condiviso con gli strumenti consigliati. |
| Parametri "st" e "se" non nell'URL di chiamata in più condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URL di firma di accesso condiviso con i valori **Data di inizio** e **Data di fine** corretti. | 
| "sp-rl" non nell'URL di chiamata in se AS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Aggiornare l'URL di firma di accesso condiviso con autorizzazioni `Read` e `List`. | 
| L'URL di firma di accesso condiviso contiene spazi nel nome del disco rigido virtuale | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URL di firma di accesso condiviso eliminando gli spazi vuoti. |
| Errore di autorizzazione dell'URL di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob due to authorization error` | Rivedere e correggere il formato dell'URI di firma di accesso condiviso. Se necessario, rigenerarlo. |
| L'indicazione di data e ora dei parametri "st" e "se" dell'URL di firma di accesso condiviso non è completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | I parametri **Data di inizio** e Data di **fine** dell'URL sAS (e`st` `se` sottostringhe) devono avere un formato datetime completo, ad `11-02-2017T00:00:00Z`esempio . Le versioni abbreviate non sono valide. Alcuni comandi dell'interfaccia della riga di comando di Azure generano valori abbreviati per impostazione predefinita. | 
|  |  |  |

Per altre informazioni, vedere [Uso delle firme di accesso condiviso](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
