---
title: Protezione avanzata della rete adattiva nel Centro sicurezza di AzureAdaptive Network Hardening in Azure Security Center - Documenti Microsoft
description: Informazioni su come usare i modelli di traffico effettivi per rendere più rigide le regole dei gruppi di sicurezza di rete e migliorare ulteriormente il livello di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385079"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Protezione avanzata della rete adattiva nel Centro sicurezza di AzureAdaptive Network Hardening in Azure Security Center
Informazioni su come configurare la protezione avanzata della rete adattiva nel Centro sicurezza di Azure.Learn how to configure Adaptive Network Hardening in Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Che cos'è la protezione avanzata della rete adattiva?
L'applicazione di gruppi di sicurezza di [rete per](https://docs.microsoft.com/azure/virtual-network/security-overview) filtrare il traffico da e verso le risorse migliora la sicurezza della rete. Tuttavia, possono ancora esserci alcuni casi in cui il traffico effettivo che passa attraverso il gruppo di sicurezza di rete è un sottoinsieme delle regole del gruppo di sicurezza di rete definite. In questi casi, è possibile migliorare ulteriormente la posizione di sicurezza con la protezione avanzata delle regole del gruppo di sicurezza di rete, in base ai modelli di traffico effettivi.

Adaptive Network Hardening fornisce consigli per indurire ulteriormente le regole del gruppo di sicurezza di rete. Usa un algoritmo di apprendimento automatico che tiene conto del traffico effettivo, della configurazione attendibile nota, dell'intelligence delle minacce e di altri indicatori di compromissione e fornisce consigli per consentire il traffico solo da tuple IP/porta specifiche.

Si supponga, ad esempio, che la regola del gruppo di sicurezza di rete esistente consenta il traffico da 140.20.30.10/24 sulla porta 22.For example, let's say the existing NSG rule is to allow traffic from 140.20.30.10/24 on port 22. La raccomandazione di Adaptive Network Hardening, basata sull'analisi, sarebbe quella di restringere l'intervallo e consentire il traffico da 140.23.30.10/29, che è un intervallo IP più ristretto e negare tutto il traffico verso quella porta.

>[!TIP]
> Le raccomandazioni di Protezione avanzata rete adattiva sono supportate solo sulle seguenti porte specifiche (sia per UDP che per TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 293 , 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![Visualizzazione protezione avanzata rete](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Visualizzare gli avvisi e le regole di Protezione avanzata della rete adattiva

1. Nel Centro sicurezza selezionare **Protezione** -> avanzata rete**adattiva rete**. Le macchine virtuali di rete sono elencate in tre schede separate:The network VMs are listed under three separate tabs:
   * **Risorse non integre:** macchine virtuali che attualmente dispongono di consigli e avvisi che sono stati attivati eseguendo l'algoritmo di protezione avanzata della rete adattiva. 
   * **Risorse integre:** macchine virtuali senza avvisi e consigli.
   * **Risorse non analizzate:** macchine virtuali su cui non è possibile eseguire l'algoritmo Adaptive Network Hardening a causa di uno dei motivi seguenti:
      * **Le macchine virtuali sono macchine virtuali classiche:** sono supportate solo le macchine virtuali di Azure Resource Manager.VMs are Classic VMs : Only Azure Resource Manager VMs are supported.
      * **Non sono disponibili dati sufficienti:** per generare raccomandazioni accurate sulla protezione avanzata del traffico, il Centro sicurezza richiede almeno 30 giorni di dati sul traffico.
      * **La macchina virtuale non è protetta dallo standard ASC:** solo le macchine virtuali impostate nel piano tariffario Standard del Centro sicurezza sono idonee per questa funzionalità.

     ![risorse non integre](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Nella scheda **Risorse non integre** selezionare una macchina virtuale per visualizzarne gli avvisi e le regole di protezione avanzata consigliate da applicare.

    ![Avvisi di protezione avanzata](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Esaminare e applicare le regole consigliate per la protezione avanzata della rete adattivaReview and apply Adaptive Network Hardening recommended rules

1. Nella scheda **Risorse non integre** selezionare una macchina virtuale. Vengono elencati gli avvisi e le regole di protezione avanzata consigliate.

     ![regole di indurimento](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Nella scheda **Regole** sono elencate le regole aggiunte dalla protezione avanzata rete adattiva. Nella scheda **Avvisi** sono elencati gli avvisi generati a causa del traffico, che scorrono alla risorsa, che non rientra nell'intervallo IP consentito nelle regole consigliate.

2. Se si desidera modificare alcuni parametri di una regola, è possibile modificarla, come illustrato in [Modificare una regola.](#modify-rule)
   > [!NOTE]
   > È inoltre possibile [eliminare](#delete-rule) o [aggiungere](#add-rule) una regola.

3. Selezionare le regole che si desidera applicare al gruppo di sicurezza di rete e fare clic su **Applica**.

      > [!NOTE]
      > Le regole applicate vengono aggiunte ai server di sicurezza di rete che proteggono la macchina virtuale. (Una macchina virtuale potrebbe essere protetta da un gruppo di sicurezza di rete associato alla relativa scheda di interfaccia di rete o dalla subnet in cui risiede la macchina virtuale o da entrambi)

    ![applicare le regole](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Modificare una <a name ="modify-rule"> </a> regola

È possibile modificare i parametri di una regola consigliata. Ad esempio, è possibile modificare gli intervalli IP consigliati.

Alcune importanti linee guida per la modifica di una regola di protezione avanzata della rete adattiva:Some important guidelines for modifying an Adaptive Network Hardening rule:

* È possibile modificare solo i parametri delle regole "Consenti". 
* Non è possibile modificare le regole "consenti" per diventare regole "nega". 

  > [!NOTE]
  > La creazione e la modifica di regole di negazione vengono eseguite direttamente nel gruppo di sicurezza di gruppo. Per ulteriori informazioni, vedere [Creare, modificare o eliminare un gruppo](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)di sicurezza di rete.

* Una regola **Nega tutto il traffico** è l'unico tipo di regola "Nega" che verrebbe elencata qui e non può essere modificata. È tuttavia possibile eliminarla (vedere [Eliminare una regola](#delete-rule)).
  > [!NOTE]
  > Una regola **Nega tutto il traffico** è consigliata quando, in seguito all'esecuzione dell'algoritmo, il Centro sicurezza non identifica il traffico che deve essere consentito, in base alla configurazione del gruppo di sicurezza di rete esistente. Pertanto, la regola consigliata consiste nel negare tutto il traffico alla porta specificata. Il nome di questo tipo di regola viene visualizzato come "*Sistema generato*". Dopo aver forzato questa regola, il nome effettivo nel gruppo di sicurezza di rete sarà una stringa costituita dal protocollo, dalla direzione del traffico, da "DENY" e da un numero casuale.

*Per modificare una regola di protezione avanzata rete adattiva:*

1. Per modificare alcuni parametri di una regola, nella scheda **Regole** fare clic sui tre puntini (...) alla fine della riga della regola e fare clic su **Modifica**.

   ![modifica regola](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Nella finestra **Modifica regola** aggiornare i dettagli che si desidera modificare e fare clic su **Salva**.

   > [!NOTE]
   > Dopo aver fatto clic su **Salva**, la regola è stata modificata. *Tuttavia, non è stato applicato al gruppo di sicurezza di rete.* Per applicarla, è necessario selezionare la regola nell'elenco e fare clic su **Applica** (come spiegato nel passaggio successivo).

   ![modifica regola](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Per applicare la regola aggiornata, selezionare la regola aggiornata dall'elenco e fare clic su **Applica**.

    ![applicare la regola](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Aggiungere una <a name ="add-rule"> </a> nuova regola

È possibile aggiungere una regola "consenti" che non è stata consigliata dal Centro sicurezza.

> [!NOTE]
> Qui è possibile aggiungere solo regole "consenti". Se si desidera aggiungere regole "nega", è possibile farlo direttamente nel gruppo di sicurezza di rete. Per ulteriori informazioni, vedere [Creare, modificare o eliminare un gruppo](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)di sicurezza di rete.

*Per aggiungere una regola di protezione avanzata rete adattiva:*

1. Fare clic su **Aggiungi regola** (nell'angolo in alto a sinistra).

   ![aggiungi regola](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Nella finestra **Nuova regola** immettere i dettagli e fare clic su **Aggiungi**.

   > [!NOTE]
   > Dopo aver fatto clic su **Aggiungi**, la regola è stata aggiunta correttamente ed è elencata con le altre regole consigliate. Tuttavia, non è stato applicato sul gruppo di sicurezza di rete. Per attivarla, è necessario selezionare la regola nell'elenco e fare clic su **Applica** (come spiegato nel passaggio successivo).

3. Per applicare la nuova regola, selezionare la nuova regola dall'elenco e fare clic su **Applica**.

    ![applicare la regola](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Eliminare una <a name ="delete-rule"> </a> regola

Se necessario, è possibile eliminare una regola consigliata per la sessione corrente. Ad esempio, è possibile determinare che l'applicazione di una regola suggerita potrebbe bloccare il traffico legittimo.

*Per eliminare una regola di protezione avanzata rete adattiva per la sessione corrente:*

1. Nella scheda **Regole** fare clic sui tre puntini (...) alla fine della riga della regola e quindi fare clic su **Elimina**.  

    ![regole di indurimento](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)