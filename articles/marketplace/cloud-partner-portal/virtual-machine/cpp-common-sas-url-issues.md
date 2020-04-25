---
title: Problemi e correzioni comuni degli URL SAS per Azure Marketplace
description: Elenco dei problemi più comuni relativi all'uso di URI per la firma di accesso condiviso e possibili soluzioni.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 723762695d34380b7f237fa9082dc470dafcc8df
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147053"
---
# <a name="common-sas-url-issues-and-fixes"></a>Problemi comuni degli URL di firma di accesso condiviso e relative correzioni

> [!IMPORTANT]
> A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte della macchina virtuale di Azure al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [problemi comuni relativi all'URL SAS e correzioni](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) .

Nella tabella seguente sono elencati alcuni dei problemi più comuni riscontrati durante l'utilizzo di firme di accesso condiviso, usate per identificare e condividere i dischi rigidi virtuali caricati per la soluzione, e le soluzioni suggerite.

| **Problema** | **Messaggio di errore** | **Difficoltà** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Errore durante la copia di immagini* |  |  |
| "?" non trovato nell'URL di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URL di firma di accesso condiviso con gli strumenti consigliati. |
| parametri "St" e "se" non presenti nell'URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URL di firma di accesso condiviso con i valori **Data di inizio** e **Data di fine** corretti. | 
| "SP = RL" non presente nell'URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Aggiornare l'URL di firma di accesso condiviso con autorizzazioni `Read` e `List`. | 
| L'URL di firma di accesso condiviso contiene spazi nel nome del disco rigido virtuale | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URL di firma di accesso condiviso eliminando gli spazi vuoti. |
| Errore di autorizzazione dell'URL di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob due to authorization error` | Rivedere e correggere il formato dell'URI di firma di accesso condiviso. Se necessario, rigenerarlo. |
| L'indicazione di data e ora dei parametri "st" e "se" dell'URL di firma di accesso condiviso non è completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | I parametri **di data di inizio** e data di`st` `se` **fine** dell'URL SAS (e le sottostringhe) devono avere il formato `11-02-2017T00:00:00Z`DateTime completo, ad esempio. Le versioni abbreviate non sono valide. Alcuni comandi dell'interfaccia della riga di comando di Azure generano valori abbreviati per impostazione predefinita. | 
|  |  |  |

Per altre informazioni, vedere [Uso delle firme di accesso condiviso](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
