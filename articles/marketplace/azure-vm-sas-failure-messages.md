---
title: Messaggi di errore SAS della macchina virtuale-Azure Marketplace
description: Domande frequenti quando si utilizzano firme di accesso condiviso (SAS).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 1c89887117c10ca77ec4c04b3adbe3e2d9923479
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126839"
---
# <a name="virtual-machine-sas-failure-messages"></a>Messaggi di errore SAS della macchina virtuale

Di seguito sono elencati alcuni problemi comuni riscontrati durante l'uso di firme di accesso condiviso, usate per identificare e condividere i dischi rigidi virtuali caricati per la soluzione, e le soluzioni suggerite.

| Problema | Messaggio di errore | Fix |
| --------- | ------------------- | ------- |
| *Errore durante la copia di immagini* |  |  |
| "?" non trovato nell'URI di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI di firma di accesso condiviso con gli strumenti consigliati. |
| Parametri "st" e "se" non presenti nell'URI di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI di firma di accesso condiviso con i valori **Data di inizio** e **Data di fine** corretti. |
| "sp = rl" non presente nell'URI di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI di firma di accesso condiviso con autorizzazioni `Read` e `List`. |
| L'URI di firma di accesso condiviso contiene spazi nel nome del disco rigido virtuale | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aggiornare l'URI di firma di accesso condiviso eliminando gli spazi vuoti. |
| Errore di autorizzazione dell'URI di firma di accesso condiviso | `Failure: Copying Images. Not able to download blob due to authorization error.` | Rivedere e correggere il formato dell'URI di firma di accesso condiviso. Se necessario, rigenerarlo. |
| L'indicazione di data e ora dei parametri "st" e "se" dell'URI di firma di accesso condiviso non è completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | I parametri **Data inizio** e **Data fine** dell'URI di firma di accesso condiviso (substring `st` e `se`) richiedono il formato datetime completo, ad esempio `11-02-2017T00:00:00Z`. Le versioni abbreviate non sono valide (alcuni comandi dell'interfaccia della riga di comando di Azure generano valori abbreviati per impostazione predefinita). |
|  |  |  |

Per informazioni dettagliate, vedere [Uso delle firme di accesso condiviso (SAS)](../storage/common/storage-sas-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- [Genera URI SAS](azure-vm-get-sas-uri.md)