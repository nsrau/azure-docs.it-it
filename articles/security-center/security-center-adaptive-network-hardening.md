---
title: Protezione avanzata di rete adattivo nel Centro sicurezza di Azure | Microsoft Docs
description: " Informazioni su come abilitare la protezione avanzata rete adattivo nel Centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: monhaber
ms.openlocfilehash: cede54f69fbeec5e01c17d84436bdc4c9ee91002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706610"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Protezione avanzata di rete adattivo nel Centro sicurezza di Azure
Informazioni su come configurare protezione avanzata di rete adattivo nel Centro sicurezza di Azure.

## <a name="what-is-adaptive-network-hardening"></a>What ' s Adaptive protezione avanzata di rete?
Applicando [(NSG) di gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview) per filtrare il traffico da e verso le risorse, migliora le condizioni di sicurezza di rete. Tuttavia, è comunque possibile sui casi in cui il traffico effettivo che passano attraverso il gruppo di sicurezza è un sottoinsieme di regole di sicurezza di rete definite. In questi casi, migliorando ulteriormente le condizioni di sicurezza può essere ottenuto hardening le regole NSG, basate sugli schemi effettivo del traffico.

Protezione avanzata di rete adattivo fornisce suggerimenti per rafforzare ulteriormente le regole NSG. Usa un algoritmo di machine learning che tenga conto effettivo del traffico, noto configurazione attendibile, intelligence per le minacce e altri indicatori di compromissione, e quindi fornisce consigli per consentire il traffico solo da specifici indirizzi IP/porte Tuple.

Supponiamo, ad esempio, che la regola NSG esistente sia per consentire il traffico da 140.20.30.10/24 sulla porta 22. Raccomandazione dell'adattivo rete protezione avanzata, in base all'analisi, sarebbe possibile restringere l'intervallo e consentire il traffico da 140.23.30.10/29, ovvero un intervallo IP più ristretto, e negare tutto il traffico a tale porta.

![vista avanzata della rete](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


> [!NOTE]
> Adaptive indicazioni di protezione avanzata di rete sono supportati sulle porte seguenti: 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Visualizzare gli avvisi di protezione avanzata di rete adattivo e regole

1. Nel Centro sicurezza, selezionare **Networking** -> **adattivo rete Hardening**. Le macchine virtuali di rete sono elencate in tre schede separate:
   * **Risorse non integre**: Macchine virtuali che attualmente dispongono dei raccomandazioni e avvisi attivati eseguendo l'algoritmo adattivo protezione avanzata di rete. 
   * **Risorse integre**: Macchine virtuali senza avvisi e raccomandazioni.
   * **Risorse analizzati**: Macchine virtuali che non è possibile eseguire l'algoritmo adattivo protezione avanzata di rete a causa di uno dei motivi seguenti:
      * **Le macchine virtuali sono le macchine virtuali classiche**:-sono supportate solo VM di Azure Resource Manager.
      * **Non sono sufficienti i dati sono disponibili**: Per generare un traffico accurato consigli sul rafforzamento, Centro sicurezza richiede almeno 30 giorni di dati di traffico.
      * **Macchina virtuale non è protetta da standard di Centro sicurezza di AZURE**: Solo le macchine virtuali che sono impostate su piano tariffario Standard del Centro sicurezza sono idonee per questa funzionalità.

     ![risorse non integre](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Dal **risorse non integre** scheda, selezionare una macchina virtuale per visualizzare di che avvisi e le regole di protezione avanzata consigliato da applicare.

    ![avvisi di protezione avanzata](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Esaminare e applicare protezione avanzata rete adattivo regole consigliate

1. Dal **risorse non integre** scheda, selezionare una macchina virtuale. Sono elencate gli avvisi e regole di protezione consigliata avanzata.
   ![avvisi di protezione avanzata](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Il **regole** scheda sono elencate le regole di protezione avanzata di rete adattivo consiglia si aggiunge. Il **avvisi** scheda vengono elencati gli avvisi generati a causa di traffico, che passano alla risorsa, che non è compreso nell'intervallo IP consentito nelle regole consigliate.

   ![regole di protezione avanzata](./media/security-center-adaptive-network-hardening/hardening-rules.png)

2. Se si desidera modificare alcuni dei parametri di una regola, è possibile modificare, come illustrato in [modifica di una regola](#modify-rule).
   > [!NOTE]
   > È anche possibile [eliminare](#delete-rule) oppure [aggiungere](#add-rule) una regola.

3. Selezionare le regole che si desidera applicare il gruppo di sicurezza e fare clic su **Imponi**. 

### Modificare una regola  <a name ="modify-rule"> </a>

È possibile modificare i parametri di una regola che è stato consigliato. Ad esempio, è possibile modificare gli intervalli IP consigliati.

Alcune importanti linee guida per la modifica di una regola di protezione avanzata di rete adattivo:

* È possibile modificare i parametri di solo regole "Consenti". 
* È possibile modificare le regole diventare "Nega" regole "Consenti". 

  > [!NOTE]
  > Creazione e modifica di regole "Nega" avviene direttamente la sicurezza di rete per altri dettagli, vedere [crea, modifica o elimina un gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* Oggetto **rifiutare tutto il traffico** regola è l'unico tipo di regola di "accesso negato" che verrà elencata qui e non può essere modificato. È possibile, tuttavia, può eliminarlo (vedere [eliminare una regola](#delete-rule)).
  > [!NOTE]
  > Oggetto **rifiutare tutto il traffico** regola è consigliata quando, come risultato dell'esecuzione dell'algoritmo, il Centro sicurezza non identifica il traffico che deve essere consentito, in base alla configurazione di sicurezza di rete esistente. Pertanto, la regola consigliata consiste nel rifiutare tutto il traffico alla porta specificata. Il nome di questo tipo di regola viene visualizzato come "generata dal sistema". Dopo l'applicazione di questa regola, il nome effettivo nella sicurezza di rete sarà una stringa costituita da un numero casuale, direzione del traffico, "Nega" e il protocollo.

*Per modificare una regola di protezione avanzata di rete adattivo:*

1. Per modificare alcuni dei parametri di una regola, nelle **regole** scheda, fare clic sui tre puntini di sospensione alla fine della riga della regola e fare clic su **regola modifica**.

   ![Modifica regola](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Nel **Modifica regola** finestra, aggiornare i dettagli che si desidera modificare e scegliere **salvare**.

   > [!NOTE]
   > Dopo aver fatto clic **salvare**, hai modificato la regola. *Tuttavia, non è stato applicato lo al NSG.* Per applicarlo, è necessario selezionare la regola nell'elenco e fare clic su **Imponi** (come illustrato nel passaggio successivo).

3. Per applicare la regola aggiornata, dall'elenco, selezionare la regola di aggiornamento e fare clic su **Imponi**.

### Aggiungere una nuova regola <a name ="add-rule"> </a>


È possibile aggiungere una regola "Consenti" che non è stata consigliata dal Centro sicurezza.

> [!NOTE]
> È possibile aggiungere le regole solo "Consenti" di seguito. Se si desidera aggiungere le regole "Nega", non è pertanto direttamente sulla sicurezza di rete. Per altre informazioni, vedere [crea, modifica o elimina un gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Per aggiungere una regola di protezione avanzata di rete adattivo:*

1. Fare clic su **Aggiungi regola** (che si trova nell'angolo superiore sinistro).

   ![Aggiungi regola](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Nel **Modifica regola** , immettere i dettagli e fare clic su **salvare**.

   > [!NOTE]
   > Dopo aver fatto clic **salvare**, è stata aggiunta la regola e viene elencato con altre regole consigliate. Tuttavia, non è stato applicato, nella sicurezza di rete. Per attivarlo, è necessario selezionare la regola nell'elenco e fare clic su **Imponi** (come illustrato nel passaggio successivo).

3. Per applicare la nuova regola, dall'elenco, selezionare la nuova regola e fare clic su **Imponi**.



### Eliminare una regola <a name ="delete-rule"> </a>

Se necessario, è possibile eliminare una regola consigliata. Ad esempio, si potrebbe determinare che l'applicazione di una regola suggerita potrebbe bloccare il traffico legittimo.

*Per eliminare una regola di protezione avanzata di rete adattivo:*

1. Nel **regole** scheda, fare clic sui tre puntini di sospensione alla fine della riga della regola e fare clic su **regola di eliminazione**.

   ![Elimina regola](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

