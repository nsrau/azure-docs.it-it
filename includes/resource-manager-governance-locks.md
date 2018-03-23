---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0cb3de7d893ccfe638468110b1b6f5fb61b2bc7c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
I blocchi delle risorse impediscono agli utenti dell'organizzazione di modificare o eliminare accidentalmente risorse di importanza fondamentale. Diversamente dal controllo degli accessi in base al ruolo, i blocchi delle risorse consentono di applicare una restrizione a tutti gli utenti e i ruoli. 

È possibile impostare il livello di blocco **CanNotDelete** o **ReadOnly**. Nel portale i livelli dei blocchi sono visualizzati rispettivamente come **Elimina** e **Sola lettura**.

* **CanNotDelete** significa che gli utenti autorizzati possono leggere e modificare una risorsa, ma non eliminarla. 
* **ReadOnly** significa che gli utenti autorizzati possono leggere una risorsa, ma non eliminarla o aggiornarla. L'applicazione di questo blocco è simile alla concessione a tutti gli utenti autorizzati solo le autorizzazioni concesse dal ruolo **Lettore**. 

> [!TIP]
> Prestare attenzione quando si applica un blocco **ReadOnly**. Alcune operazioni che sembrano operazioni di sola lettura possono effettivamente richiedere azioni aggiuntive. Ad esempio, un blocco **ReadOnly** applicato a un account di archiviazione impedisce a tutti gli utenti di visualizzare l'elenco delle chiavi. L'operazione di elenco delle chiavi viene gestita tramite una richiesta POST, perché le chiavi restituite sono disponibili per operazioni di scrittura. Un blocco **ReadOnly** applicato a una risorsa Servizio app impedisce a Visual Studio Server Explorer di visualizzare i file della risorsa perché questa interazione richiede l'accesso in scrittura.

Quando si applica un blocco in un ambito padre, tutte le risorse in tale ambito ereditano lo stesso blocco. Anche le risorse aggiunte successivamente ereditano il blocco dal padre. Il blocco più restrittivo nell'ereditarietà ha la precedenza.

I blocchi di Resource Manager si applicano solo alle operazioni che si verificano nel piano di gestione, costituito da operazioni inviate a `https://management.azure.com`. I blocchi non limitano il modo in cui le risorse elaborano le proprie funzioni. Vengono limitate le modifiche alle risorse, ma non le operazioni delle risorse. Ad esempio, un blocco ReadOnly applicato a un database SQL impedisce l'eliminazione o la modifica del database. Non impedisce la creazione, l'aggiornamento o l'eliminazione di dati nel database. Le transazioni di dati sono consentite in quanto tali operazioni non vengono inviate a `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Chi può creare o eliminare i blocchi nell'organizzazione
Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni `Microsoft.Authorization/locks/*`. Dei ruoli predefiniti, solo **Proprietario** e **Amministratore Accesso utenti** garantiscono tali azioni.
