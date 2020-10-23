---
title: Protezione avanzata della rete adattiva nel centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come usare i modelli di traffico effettivi per rafforzare le regole dei gruppi di sicurezza di rete (NSG) e migliorare ulteriormente il comportamento di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: e2b17e15c5548b4c9b93a62a7d4dfe62ff44404c
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341754"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Protezione avanzata della rete adattiva nel centro sicurezza di Azure
Informazioni su come configurare la protezione avanzata della rete adattiva nel centro sicurezza.

## <a name="availability"></a>Disponibilità
|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|È necessario [Azure Defender per server](defender-for-servers-introduction.md)|
|Autorizzazioni e ruoli obbligatori:|Autorizzazioni di scrittura per i gruppi di sicurezza di rete del computer|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||

## <a name="what-is-adaptive-network-hardening"></a>Che cos'è la protezione avanzata della rete adattiva?
L'applicazione dei [gruppi di sicurezza di rete (NSG)](../virtual-network/network-security-groups-overview.md) per filtrare il traffico da e verso le risorse migliora il comportamento di sicurezza della rete. In alcuni casi, tuttavia, è comunque possibile che il traffico effettivo che attraversa il gruppo di sicurezza di rete corrisponda a un subset delle regole del gruppo di sicurezza di rete definite. In questi casi è possibile migliorare ancora il comportamento di sicurezza applicando la protezione avanzata alle regole del gruppo di sicurezza di rete, in base ai criteri effettivi del traffico.

La protezione avanzata della rete adattiva fornisce consigli per rafforzare ulteriormente le regole NSG. Usa un algoritmo di Machine Learning che prende in considerazione il traffico effettivo, la configurazione attendibile nota, l'intelligence sulle minacce e altri indicatori di compromissione e quindi fornisce raccomandazioni per consentire il traffico solo da tuple di IP/porta specifiche.

Ad esempio, supponiamo che la regola NSG esistente consenta il traffico da 140.20.30.10/24 sulla porta 22. In base all'analisi del traffico, la protezione avanzata della rete adattiva potrebbe consigliare di limitare l'intervallo per consentire il traffico da 140.23.30.10/29 e di negare tutto il traffico a tale porta.

>[!Note]
> Le raccomandazioni per la protezione avanzata della rete adattiva sono supportate solo sulle seguenti porte specifiche (per UDP e TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


## <a name="view-and-manage-hardening-alerts-and-rules"></a>Visualizzare e gestire gli avvisi e le regole di protezione avanzata

1. Dal menu del Centro sicurezza aprire il dashboard di **Azure Defender** e selezionare il riquadro protezione avanzata della rete adattiva (1) o l'elemento pannello Insights correlato alla protezione avanzata della rete adattiva (2). 

    :::image type="content" source="./media/security-center-adaptive-network-hardening/traffic-hardening.png" alt-text="Accesso agli strumenti di protezione avanzata della rete adattiva" lightbox="./media/security-center-adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > Il pannello Insights Mostra la percentuale delle VM attualmente protette con la protezione avanzata della rete adattiva. 

1. La pagina dei dettagli per le raccomandazioni per la **protezione avanzata della rete adattiva dovrebbe essere applicata alle indicazioni relative alle macchine virtuali con connessione Internet** con le VM di rete raggruppate in tre schede:
   * **Risorse non integre**: VM che attualmente presentano raccomandazioni e avvisi attivati eseguendo l'algoritmo di protezione avanzata della rete adattiva. 
   * **Risorse integre**: VM senza avvisi e raccomandazioni.
   * **Risorse non analizzate**: le macchine virtuali in cui non è possibile eseguire l'algoritmo di protezione avanzata della rete adattiva a causa di uno dei motivi seguenti:
      * Le **macchine virtuali sono macchine virtuali classiche**: sono supportate solo macchine virtuali Azure Resource Manager.
      * **I dati disponibili non sono sufficienti**: per generare raccomandazioni accurate per la protezione avanzata del traffico, il Centro sicurezza richiede almeno 30 giorni di dati sul traffico.
      * La **macchina virtuale non è protetta da Azure Defender**: solo le macchine virtuali protette con [Azure Defender per i server](defender-for-servers-introduction.md) sono idonee per questa funzionalità.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/recommendation-details-page.png" alt-text="Accesso agli strumenti di protezione avanzata della rete adattiva":::

1. Dalla scheda **risorse non integre** selezionare una macchina virtuale per visualizzare gli avvisi e le regole di protezione avanzata consigliate da applicare.

    - Nella scheda **regole** sono elencate le regole che la protezione avanzata della rete adattiva consiglia di aggiungere
    - Nella scheda **avvisi** sono elencati gli avvisi generati a causa del traffico, che si propagano alla risorsa, che non rientra nell'intervallo IP consentito nelle regole consigliate.

1. Facoltativamente, modificare le regole:

    - [Modificare una regola](#modify-rule)
    - [Eliminare una regola](#delete-rule) 
    - [Aggiungere una regola](#add-rule)

3. Selezionare le regole che si desidera applicare al NSG, quindi fare clic su **applica**.

    > [!TIP]
    > Se gli intervalli di indirizzi IP di origine consentiti vengono visualizzati come ' none ', significa che la regola consigliata è una regola di *negazione* . in caso contrario, si tratta di una regola di *autorizzazione* .

    :::image type="content" source="./media/security-center-adaptive-network-hardening/hardening-alerts.png" alt-text="Accesso agli strumenti di protezione avanzata della rete adattiva":::

      > [!NOTE]
      > Le regole imposte vengono aggiunte a NSG (s) che proteggono la macchina virtuale. Una macchina virtuale può essere protetta da un NSG associato alla scheda di interfaccia di rete o alla subnet in cui risiede la macchina virtuale o a entrambe.

### <a name="modify-a-rule"></a>Modificare una regola <a name ="modify-rule"> </a>

Potrebbe essere necessario modificare i parametri di una regola consigliata. Ad esempio, potrebbe essere necessario modificare gli intervalli IP consigliati.

Alcune linee guida importanti per la modifica di una regola di protezione avanzata della rete adattiva:

* È possibile modificare i parametri solo per le regole "Allow". 
* Non è possibile modificare le regole "Consenti" per diventare regole "Nega". 

  > [!NOTE]
  > La creazione e la modifica delle regole di "negazione" vengono eseguite direttamente nel NSG. Per altre informazioni, vedere [creare, modificare o eliminare un gruppo di sicurezza di rete](../virtual-network/manage-network-security-group.md).

* Una regola **Deny all traffic** è l'unico tipo di regola "Deny" che verrebbe elencata e non può essere modificata. È tuttavia possibile eliminarla (vedere [eliminare una regola](#delete-rule)).
  > [!NOTE]
  > Quando si esegue l'algoritmo, il Centro sicurezza non identifica il traffico che deve essere consentito, in base alla configurazione NSG esistente, è consigliabile utilizzare una regola di **negazione di tutto il traffico** . Pertanto, la regola consigliata consiste nel negare tutto il traffico alla porta specificata. Il nome di questo tipo di regola viene visualizzato come "*generato dal sistema*". Dopo l'applicazione di questa regola, il nome effettivo in NSG sarà una stringa costituita dal protocollo, direzione del traffico, "Nega" e un numero casuale.

*Per modificare una regola di protezione avanzata della rete adattiva:*

1. Per modificare alcuni parametri di una regola, nella scheda **regole** fare clic sui tre puntini di sospensione (...) alla fine della riga della regola, quindi fare clic su **modifica**.

   ![Modifica della regola s](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Nella finestra **Modifica regola** aggiornare i dettagli che si desidera modificare e fare clic su **Salva**.

   > [!NOTE]
   > Dopo aver fatto clic su **Salva**, la regola è stata modificata. *Tuttavia, non è stato applicato a NSG.* Per applicarla, è necessario selezionare la regola nell'elenco e selezionare **applica** (come illustrato nel passaggio successivo).

   ![Selezione di Save](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Per applicare la regola aggiornata, dall'elenco selezionare la regola aggiornata e fare clic su **applica**.

    ![Applica regola](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Aggiungi una nuova regola <a name ="add-rule"> </a>

È possibile aggiungere una regola "Consenti" non consigliata dal centro sicurezza.

> [!NOTE]
> Qui è possibile aggiungere solo le regole "Allow". Se si desidera aggiungere regole di "negazione", è possibile eseguire questa operazione direttamente nel NSG. Per altre informazioni, vedere [creare, modificare o eliminare un gruppo di sicurezza di rete](../virtual-network/manage-network-security-group.md).

*Per aggiungere una regola di protezione avanzata della rete adattiva:*

1. Fare clic su **Aggiungi regola** (presente nell'angolo in alto a sinistra).

   ![aggiungi regola](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Nella finestra **nuova regola** immettere i dettagli e fare clic su **Aggiungi**.

   > [!NOTE]
   > Dopo aver fatto clic su **Aggiungi**, la regola è stata aggiunta correttamente ed è elencata con le altre regole consigliate. Tuttavia, non è stato applicato a NSG. Per attivarla, è necessario selezionare la regola nell'elenco e fare clic su **applica** (come illustrato nel passaggio successivo).

3. Per applicare la nuova regola, dall'elenco selezionare la nuova regola e fare clic su **applica**.

    ![Applica regola](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Eliminare una regola <a name ="delete-rule"> </a>

Quando necessario, è possibile eliminare una regola consigliata per la sessione corrente. Ad esempio, è possibile determinare che l'applicazione di una regola consigliata potrebbe bloccare il traffico legittimo.

*Per eliminare una regola di protezione avanzata della rete adattiva per la sessione corrente:*

1. Nella scheda **regole** fare clic sui tre puntini di sospensione (...) alla fine della riga della regola, quindi fare clic su **Elimina**.  

    ![Eliminazione di una regola](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)