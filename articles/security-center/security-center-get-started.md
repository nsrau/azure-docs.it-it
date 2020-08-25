---
title: Passare al piano Standard - Centro sicurezza di Azure
description: Questa Guida introduttiva illustra come eseguire l'aggiornamento al piano tariffario Standard del Centro sicurezza per aumentare la sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 550c9ff57b9c558f2f175165c7f06ead45991be9
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226014"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Guida di avvio rapido: Caricamento della sottoscrizione di Azure al livello Standard del Centro di sicurezza
Il Centro sicurezza di Azure fornisce la gestione unificata della sicurezza e la protezione dalle minacce per carichi di lavoro cloud ibridi. Mentre il livello gratuito offre sicurezza limitata delle risorse di Azure, il livello Standard estende le funzionalità in locale e in altri ambienti cloud. Il livello Standard del Centro sicurezza consente di individuare e risolvere le vulnerabilità di sicurezza, di applicare i controlli su applicazioni e accessi per bloccare le attività dannose, di rilevare le minacce usando funzioni di analisi e di intelligenza e di rispondere rapidamente in caso di attacco. È possibile provare il livello Standard del Centro sicurezza gratuitamente. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

Questo articolo descrive come eseguire l'aggiornamento al livello Standard per aumentare la sicurezza e come installare l'agente di Log Analytics nelle macchine virtuali per monitorare le vulnerabilità di sicurezza e le minacce.

## <a name="prerequisites"></a>Prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

Per aggiornare una sottoscrizione al livello Standard, è necessario avere il ruolo di proprietario della sottoscrizione, collaboratore alla sottoscrizione o amministratore della protezione.

## <a name="enable-your-azure-subscription"></a>Abilitare la sottoscrizione di Azure

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).

1. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**.

   ![Panoramica del Centro sicurezza di Azure][2]

**Centro di sicurezza - Panoramica** offre una visualizzazione unificata del comportamento di sicurezza dei carichi di lavoro cloud ibridi, consentendo all'utente di individuarne e valutarne la sicurezza e di identificare e ridurre i rischi. Il Centro sicurezza abilita automaticamente una sottoscrizione Azure non caricata in precedenza dall'utente o da un altro utente della sottoscrizione al livello gratuito.

Per visualizzare e filtrare l'elenco delle sottoscrizioni, fare clic sulla voce di menu **Sottoscrizioni**. Nel Centro sicurezza verrà iniziata la valutazione di sicurezza di tali sottoscrizioni per identificare le vulnerabilità. Per personalizzare i tipi delle valutazioni, è possibile modificare i criteri di sicurezza. Un criterio di sicurezza definisce la configurazione specifica dei carichi di lavoro e contribuisce ad assicurare la conformità ai requisiti aziendali o normativi per la sicurezza.

Dopo pochi minuti dal primo avvio del Centro di sicurezza, è possibile visualizzare gli elementi seguenti:

- **Raccomandazioni** per individuare i modi per migliorare la sicurezza delle sottoscrizioni di Azure. Fare clic sul riquadro **Raccomandazioni** per visualizzare un elenco di consigli con priorità.
- Una serie di risorse in **Risorse di calcolo e app**, **Rete**, **Sicurezza dei dati** e **Identità e accesso** in corso di valutazione presso il Centro sicurezza di Azure con le condizioni di sicurezza di ognuna.

Per sfruttare al meglio il Centro sicurezza, è necessario completare i passaggi seguenti per eseguire l'aggiornamento al livello Standard e installare l'agente di Log Analytics.


## <a name="upgrade-to-the-standard-tier"></a>Eseguire l'aggiornamento al livello Standard

Ai fini della Guida di avvio rapido e delle esercitazioni per il Centro di sicurezza è necessario eseguire l'aggiornamento al livello Standard. È disponibile una versione di valutazione gratuita del livello Standard del Centro sicurezza. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/). 

1. Nella barra laterale del Centro sicurezza selezionare **Attività iniziali**.
 
   ![Introduzione](./media/security-center-get-started/get-started-upgrade-tab.png)

    La scheda **Aggiorna** contiene un elenco di sottoscrizioni e aree di lavoro idonee per l'onboarding.

1. Nell'elenco **Selezionare le aree di lavoro in cui abilitare il livello Standard** selezionare le aree di lavoro da aggiornare.


    > [!TIP]
    > Se si seleziona un'area di lavoro idonea per una versione di valutazione gratuita, il passaggio successivo inizierà una versione di valutazione. Se le aree di lavoro non sono idonee per la versione di valutazione, verranno aggiornate e verranno applicati addebiti.

1. Selezionare **Aggiorna** per aggiornare le aree di lavoro selezionate al livello Standard.


## <a name="automate-data-collection"></a>Automatizzare la raccolta dei dati
Il Centro sicurezza raccoglie i dati delle macchine virtuali di Azure e dei computer senza Azure per monitorare le minacce e le vulnerabilità della sicurezza. I dati vengono raccolti tramite l'agente di Log Analytics, che legge diverse configurazioni correlate alla sicurezza oltre ai log eventi del computer e copia i dati nell'area di lavoro per eseguire l'analisi. Per impostazione predefinita, il Centro sicurezza crea una nuova area di lavoro per l'utente.

Dopo aver abilitato il provisioning automatico, il Centro sicurezza installa l'agente di Log Analytics in tutte le macchine virtuali di Azure supportate e nelle nuove macchine create. È consigliabile abilitare il provisioning automatico.

Per abilitare il provisioning automatico dell'agente di Log Analytics:

1. Scegliere **Prezzi e impostazioni** dal menu principale del Centro sicurezza.
1. Nella riga della sottoscrizione fare clic sulla sottoscrizione di cui si vogliono modificare le impostazioni.
1. Nella scheda **Raccolta dati**, impostare **Provisioning automatico** su **Sì**.
1. Selezionare **Salva**.
---
  ![Abilitare il provisioning automatico][6]

Grazie a queste nuove informazioni sulle macchine virtuali di Azure, il Centro sicurezza può offrire indicazioni aggiuntive correlate allo stato di aggiornamento del sistema, alle configurazioni di sicurezza del sistema operativo e alla protezione di endpoint, nonché generare avvisi di sicurezza aggiuntivi.

  ![Consigli][8]

## <a name="clean-up-resources"></a>Pulire le risorse
Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare le guide introduttive e le esercitazioni successive, continuare a eseguire il livello Standard e tenere abilitato il provisioning automatico. Se non si intende proseguire oppure si vuole tornare al livello gratuito:

1. Tornare al menu principale del Centro sicurezza e selezionare **Prezzi e impostazioni**.
2. Fare clic sulla sottoscrizione per cui si vuole tornare al livello gratuito.
3. Selezionare **Piano tariffario** e quindi **Gratuito** per modificare il livello della sottoscrizione da Standard a Gratuito.
5. Selezionare **Salva**.

Se si vuole disabilitare il provisioning automatico:

1. Tornare al menu principale del Centro sicurezza e selezionare **Prezzi e impostazioni**.
2. Selezionare la sottoscrizione per cui si vuole disabilitare il provisioning automatico.
3. Nella scheda **Raccolta dati**, impostare **Provisioning automatico** su **No**.
4. Selezionare **Salva**.

>[!NOTE]
> La disabilitazione del provisioning automatico non implica la rimozione dell'agente di Log Analytics dalle macchine virtuali di Azure in cui è stato eseguito il provisioning dell'agente. La disabilitazione automatica del provisioning limita il monitoraggio delle risorse.
>

## <a name="next-steps"></a>Passaggi successivi
In questa guida di avvio rapido è stato eseguito l'aggiornamento al livello Standard ed è stato effettuato il provisioning dell'agente di Log Analytics per la gestione unificata della sicurezza e della protezione dalle minacce nei carichi di lavoro cloud ibridi. Per altre informazioni su come usare il Centro sicurezza, continuare con le indicazioni presenti nella Guida di avvio rapido per l'onboarding di computer Windows in locale o in altri ambienti cloud.

> [!div class="nextstepaction"]
> [Avvio rapido: Onboarding di computer Windows in Centro sicurezza di Azure](quick-onboard-windows-computer.md)

Si vuole ottimizzare e risparmiare sulla spesa per il cloud?

> [!div class="nextstepaction"]
> [Per iniziare, analizzare i costi con Gestione costi](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
