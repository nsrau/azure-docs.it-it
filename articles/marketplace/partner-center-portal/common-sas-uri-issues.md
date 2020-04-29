---
title: Problemi e correzioni comuni dell'URI SAS-Azure Marketplace
description: Problemi comuni riscontrati e soluzioni suggerite quando si utilizzano le firme di accesso condiviso.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266240"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Problemi e correzioni comuni dell'URI SAS

> [!IMPORTANT]
> Stiamo muovendo la gestione delle offerte di macchine virtuali di Azure dal portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, seguire le istruzioni riportate in problemi URI di firma di accesso [condiviso e correzioni](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) per portale cloud partner per gestire le offerte.

Di seguito sono riportati i problemi comuni riscontrati durante l'uso delle firme di accesso condiviso (che vengono usate per identificare e condividere i dischi rigidi virtuali caricati per la soluzione), insieme alle risoluzioni suggerite.

| **Problema** | **Messaggio di errore** | **Difficoltà** |
| --------- | ------------------- | ------- |
| *Errore durante la copia di immagini* |  |  |
| "?" non è stato trovato nell'URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI SAS usando gli strumenti consigliati. |
| parametri "St" e "se" non presenti nell'URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI SAS con i valori di **Data di inizio** e data di **fine** corretti. |
| "SP = RL" non presente nell'URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI SAS con le autorizzazioni impostate `Read` come `List`e. |
| URI SAS con spazi vuoti nel nome VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI SAS per rimuovere gli spazi vuoti. |
| Errore di autorizzazione URI SAS | `Failure: Copying Images. Not able to download blob due to authorization error.` | Rivedere e correggere il formato dell'URI di firma di accesso condiviso. Se necessario, rigenerarlo. |
| I parametri dell'URI SAS "St" e "se" non hanno una specifica di data e ora completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | I parametri **Start Date** di data e **ora di** inizio dell'`st` URI `se` SAS (e le sottostringhe) devono avere un formato di data `11-02-2017T00:00:00Z`e ora completo, ad esempio. Le versioni abbreviate non sono valide (alcuni comandi nell'interfaccia della riga di comando di Azure possono generare valori abbreviati per impostazione predefinita). |
|  |  |  |

Per informazioni dettagliate, vedere [uso delle firme di accesso condiviso (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
