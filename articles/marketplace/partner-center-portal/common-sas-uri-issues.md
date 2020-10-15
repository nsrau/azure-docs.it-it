---
title: Problemi comuni degli URI di firma di accesso condiviso e relative correzioni - Azure Marketplace
description: Problemi comuni riscontrati e soluzioni suggerite quando si usano firme di accesso condiviso.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: 4eb3c3e893a276aed10807a13a0f2d6d3bc4e71d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87316839"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Problemi comuni degli URI di firma di accesso condiviso e relative correzioni

Di seguito sono elencati alcuni problemi comuni riscontrati durante l'uso di firme di accesso condiviso, usate per identificare e condividere i dischi rigidi virtuali caricati per la soluzione, e le soluzioni suggerite.

| **Problema** | **Messaggio di errore** | **Correzione** |
| --------- | ------------------- | ------- |
| *Errore durante la copia di immagini* |  |  |
| "?" non trovato nell'URI di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI di firma di accesso condiviso con gli strumenti consigliati. |
| Parametri "st" e "se" non presenti nell'URI di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI di firma di accesso condiviso con i valori **Data di inizio** e **Data di fine** corretti. |
| "sp = rl" non presente nell'URI di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI di firma di accesso condiviso con autorizzazioni `Read` e `List`. |
| L'URI di firma di accesso condiviso contiene spazi nel nome del disco rigido virtuale | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI di firma di accesso condiviso eliminando gli spazi vuoti. |
| Errore di autorizzazione dell'URI di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob due to authorization error.` | Rivedere e correggere il formato dell'URI di firma di accesso condiviso. Se necessario, rigenerarlo. |
| L'indicazione di data e ora dei parametri "st" e "se" dell'URI di firma di accesso condiviso non è completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | I parametri **Data inizio** e **Data fine** dell'URI di firma di accesso condiviso (substring `st` e `se`) richiedono il formato datetime completo, ad esempio `11-02-2017T00:00:00Z`. Le versioni abbreviate non sono valide (alcuni comandi dell'interfaccia della riga di comando di Azure generano valori abbreviati per impostazione predefinita). |
|  |  |  |

Per informazioni dettagliate, vedere [Uso delle firme di accesso condiviso (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
