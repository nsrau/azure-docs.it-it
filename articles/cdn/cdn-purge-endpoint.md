---
title: Ripulire un endpoint della rete CDN di Azure | Microsoft Docs
description: Informazioni su come ripulire tutto il contenuto memorizzato nella cache da un endpoint della rete CDN di Azure.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b035c232bb58d653960190d4974cc3789d55a51d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="purge-an-azure-cdn-endpoint"></a>Ripulire un endpoint della rete CDN di Azure
## <a name="overview"></a>Panoramica
I nodi perimetrali della rete CDN di Azure memorizzeranno nella cache gli asset fino alla scadenza della durata TTL dell'asset.  Dopo la scadenza della durata TTL dell'asset, quando un client richiede l'asset dal nodo periodico, questo recupera una nuova copia aggiornata dell'asset per soddisfare la richiesta del client e aggiornare la cache.

La procedura consigliata per assicurarsi che gli utenti ottengano sempre la copia più recente degli asset consiste nel versioning di questi ultimi per ogni aggiornamento e nella relativa pubblicazione come nuovi URL.  La rete CDN recupera immediatamente i nuovi asset per le richieste client successive.  A volte si desidera ripulire il contenuto memorizzato nella cache da tutti i nodi periodici e forzare il recupero dei nuovi asset aggiornati.  Ciò potrebbe essere dovuto agli aggiornamenti all'applicazione Web o a un aggiornamento rapido di asset che contengono informazioni non corrette.

> [!TIP]
> Solo la cancellazione svuota il contenuto della cache sui server perimetrali della rete CDN.  Tutte le cache downstream, ad esempio le cache dei server proxy e del browser locale, possono comunque mantenere una copia del file nella cache.  È importante tenerlo presente quando si imposta la durata di un file.  È possibile forzare un client downstream per richiedere la versione più recente del file assegnandogli un nome univoco ogni volta che viene aggiornato o sfruttando la [memorizzazione nella cache delle stringhe di query](cdn-query-string.md).  
> 
> 

Questa esercitazione illustra l'eliminazione dagli asset di tutti i nodi periodici di un endpoint.

## <a name="walkthrough"></a>Procedura dettagliata
1. Nel [portale di Azure](https://portal.azure.com)passare al profilo di rete CDN contenente l'endpoint che si desidera ripulire.
2. Nel pannello relativo al profilo di rete CDN fare clic sul pulsante di eliminazione.
   
    ![Pannello del profilo di rete CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Viene visualizzato il pannello di eliminazione.
   
    ![Pannello di eliminazione della rete CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Nel pannello di eliminazione selezionare l'indirizzo del servizio che si desidera ripulire dall'elenco a discesa degli URL.
   
    ![Maschera di eliminazione](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > È possibile visualizzare il pannello di eliminazione anche facendo clic sul pulsante **Elimina** nel pannello dell'endpoint della rete CDN.  In tal caso, il campo **URL** sarà prepopolato con l'indirizzo del servizio dell'endpoint specifico.
   > 
   > 
4. Selezionare gli asset che si desidera ripulire dai nodi periferici.  Se si desidera ripulire tutti gli asset, fare clic sulla casella di controllo **Elimina tutto** .  In alternativa digitare il percorso di ogni asset che si vuole eliminare nella casella di testo **Percorso**. I formati seguenti sono supportati nel percorso.
    1. **Single URL purge**: (Eliminazione di un URL singolo) eliminazione di un singolo asset specificando l'URL completo, con o senza l'estensione di file, ad esempio `/pictures/strasbourg.png`; `/pictures/strasbourg`
    2. **Wildcard purge**: (Eliminazione dei caratteri jolly) l'asterisco (\*) può essere usato come carattere jolly. Consente di eliminare tutte le cartelle, le sottocartelle e i file in un endpoint inserendo `/*` nel percorso o di eliminare tutte le sottocartelle e i file in una determinata cartella specificando la cartella seguita da `/*`, ad esempio `/pictures/*`.  Si noti che l'eliminazione dei caratteri jolly non è attualmente supportata dalla rete CDN di Azure fornita da Akamai. 
    3. **Root domain purge**: (Eliminazione del dominio radice) consente di eliminare la radice dell'endpoint inserendo "/" nel percorso.
   
   > [!TIP]
   > Per l'eliminazione, è necessario che i percorsi vengano specificati e che siano un URL relativo che soddisfi l'[espressione regolare](https://msdn.microsoft.com/library/az24scfc.aspx) seguente. Le funzioni **Elimina tutti** e **Wildcard purge** (Eliminazione dei caratteri jolly) non sono attualmente supportate con la **rete CDN di Azure fornita da Akamai**.
   > > Single URL purge (Eliminazione di un URL singolo) `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Stringa di query `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Wildcard purge (Eliminazione dei caratteri jolly) `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > Dopo l'immissione di testo verranno visualizzate altre caselle di testo **Percorso** che consentono di compilare un elenco di più asset.  È possibile eliminare gli asset dall'elenco facendo clic sul pulsante con i puntini di sospensione (...).
   > 
5. Fare clic sul pulsante **Elimina** .
   
    ![Pulsante di eliminazione](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> L'elaborazione delle richieste di eliminazione dura circa 2-3 minuti per l'elaborazione con la **rete CDN di Azure fornita da Verizon** (Standard e Premium) e circa 7 minuti con la **rete CDN di Azure fornita da Akamai**.  La rete CDN di Azure ha un limite di 50 richieste di eliminazione simultanee in qualsiasi momento. 
> 
> 

## <a name="see-also"></a>Vedere anche
* [Precaricamento di risorse in un endpoint della rete CDN di Azure](cdn-preload-endpoint.md)
* [Riferimento API REST della rete CDN di Azure - Ripulire o precaricare un endpoint](https://msdn.microsoft.com/library/mt634451.aspx)

