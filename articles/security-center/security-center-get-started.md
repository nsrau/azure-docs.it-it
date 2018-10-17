---
title: Guida introduttiva di Centro sicurezza di Azure - Caricamento della sottoscrizione di Azure al livello Standard del Centro sicurezza di Azure | Microsoft Docs
description: Questa Guida introduttiva illustra come eseguire l'aggiornamento al piano tariffario Standard del Centro sicurezza per aumentare la sicurezza.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/2/2018
ms.author: rkarlin
ms.openlocfilehash: dc4c07b54085dfea6d97f2ca43a8cdddfcb9e57c
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025465"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Guida introduttiva: Caricamento della sottoscrizione di Azure al livello Standard del Centro di sicurezza
Il Centro sicurezza di Azure fornisce la gestione unificata della sicurezza e la protezione dalle minacce per carichi di lavoro cloud ibridi. Mentre il livello gratuito offre sicurezza limitata delle risorse di Azure, il livello Standard estende le funzionalità in locale e in altri ambienti cloud. Il livello Standard del Centro sicurezza consente di individuare e risolvere le vulnerabilità di sicurezza, di applicare i controlli su applicazioni e accessi per bloccare le attività dannose, di rilevare le minacce usando funzioni di analisi e di intelligenza e di rispondere rapidamente in caso di attacco. È possibile provare gratuitamente il livello Standard del Centro sicurezza per i primi 60 giorni.

Questo articolo descrive come eseguire l'aggiornamento al livello Standard per aumentare la sicurezza e come installare Microsoft Monitoring Agent nelle macchine virtuali per monitorare le vulnerabilità di sicurezza e le minacce.

## <a name="prerequisites"></a>Prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

Per aggiornare una sottoscrizione al livello Standard, è necessario disporre del ruolo di proprietario della sottoscrizione, collaboratore alla sottoscrizione o amministratore della sicurezza.

## <a name="enable-your-azure-subscription"></a>Abilitare la sottoscrizione di Azure

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).
2. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**.

 ![Panoramica del Centro sicurezza di Azure][2]

**Centro di sicurezza - Panoramica** offre una visualizzazione unificata del comportamento di sicurezza dei carichi di lavoro di cloud ibrido, consentendo all'utente di individuarne e valutarne la sicurezza e di identificare e ridurre i rischi. Il Centro sicurezza abilita automaticamente una sottoscrizione Azure non caricata in precedenza dall'utente o da un altro utente della sottoscrizione al livello gratuito.

Per visualizzare e filtrare l'elenco delle sottoscrizioni, fare clic sulla voce di menu **Sottoscrizioni**. Nel Centro sicurezza verrà iniziata la valutazione di sicurezza di tali sottoscrizioni per identificare le vulnerabilità. Per personalizzare i tipi delle valutazioni, è possibile modificare i criteri di sicurezza. Un criterio di sicurezza definisce la configurazione specifica dei carichi di lavoro e contribuisce ad assicurare la conformità ai requisiti aziendali o normativi per la sicurezza.

Dopo pochi minuti dal primo avvio del Centro di sicurezza, è possibile visualizzare gli elementi seguenti:

- **Raccomandazioni** per individuare i modi per migliorare la sicurezza delle sottoscrizioni di Azure. Fare clic sul riquadro **Raccomandazioni** per visualizzare un elenco di consigli con priorità.
- Una serie di risorse in **Risorse di calcolo e app**, **Rete**, **Sicurezza dei dati** e **Identità e accesso** in corso di valutazione presso il Centro sicurezza di Azure con le condizioni di sicurezza di ognuna.

Per sfruttare al meglio il Centro sicurezza, è necessario completare i passaggi seguenti per eseguire l'aggiornamento al livello Standard e installare Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Eseguire l'aggiornamento al livello Standard
Ai fini della Guida introduttiva e delle esercitazioni per il Centro di sicurezza è necessario eseguire l'aggiornamento al livello Standard. I primi 60 giorni sono gratuiti ed è possibile tornare al livello gratuito in qualsiasi momento.

1. Scegliere **Introduzione** dal menu principale del Centro sicurezza.
 
  ![Attività iniziali][4]

2. In **Aggiorna**, il Centro sicurezza di Azure elenca tutte le sottoscrizioni e i carichi di lavoro idonei per il caricamento. 
   - È possibile espandere **Apply your trial** (Applicare la versione di valutazione) per visualizzare un elenco di tutte le sottoscrizioni e aree di lavoro con il relativo stato di idoneità alla versione di valutazione.
   -    È possibile aggiornare le sottoscrizioni e le aree di lavoro che non sono idonee per la versione di valutazione.
   -    È possibile selezionare le aree di lavoro e le sottoscrizioni idonee per avviare la versione di valutazione.
3.  Fare clic su **Avvia versione di valutazione** per avviare la versione di valutazione nelle sottoscrizioni selezionate.


  ![Avvisi di sicurezza][9]

## <a name="automate-data-collection"></a>Automatizzare la raccolta dei dati
Il Centro sicurezza raccoglie i dati delle macchine virtuali di Azure e dei computer senza Azure per monitorare le minacce e le vulnerabilità della sicurezza. I dati vengono raccolti tramite Microsoft Monitoring Agent, che legge diverse configurazioni correlate alla sicurezza oltre ai registri eventi del computer e copia i dati nell'area di lavoro per eseguire l'analisi. Per impostazione predefinita, il Centro sicurezza crea una nuova area di lavoro per l'utente.

Dopo aver abilitato il provisioning automatico, il Centro sicurezza installa Microsoft Monitoring Agent in tutte le macchine virtuali di Azure supportate e in quelle nuove che vengono create. È consigliabile abilitare il provisioning automatico.

Per abilitare il provisioning automatico di Microsoft Monitoring Agent:

1. selezionare **Criteri di sicurezza** nel menu principale Centro sicurezza.
2. Sulla riga della sottoscrizione, selezionare **Modifica impostazioni>**.
3. Nella scheda **Raccolta dati**, impostare **Provisioning automatico** su **Sì**.
4. Selezionare **Salva**.
****
  ![Abilitare il provisioning automatico][6]

Grazie a queste nuove informazioni sulle macchine virtuali di Azure, il Centro sicurezza può offrire indicazioni aggiuntive correlate allo stato di aggiornamento del sistema, alle configurazioni di sicurezza del sistema operativo e alla protezione di endpoint, nonché generare avvisi di sicurezza aggiuntivi.

  ![Consigli][8]

## <a name="clean-up-resources"></a>Pulire le risorse
Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare le guide introduttive e le esercitazioni successive, continuare a eseguire il livello Standard e tenere abilitato il provisioning automatico. Se non si intende proseguire oppure si vuole tornare al livello gratuito:

1. Tornare al menu principale del Centro sicurezza e selezionare **Criteri di sicurezza**.
2. Sulla riga della sottoscrizione che si vuole riportare al livello gratuito, selezionare **Modifica impostazioni>**.
3. Selezionare **Piano tariffario** e quindi **Gratuito** per modificare il livello della sottoscrizione da Standard a Gratuito.
5. Selezionare **Salva**.

Se si vuole disabilitare il provisioning automatico:

1. Tornare al menu principale del Centro sicurezza e selezionare **Criteri di sicurezza**.
2. Sulla riga della sottoscrizione per la quale si vuole disabilitare il provisioning automatico, selezionare **Modifica impostazioni>**.
3. Nella scheda **Raccolta dati**, impostare **Provisioning automatico** su **No**.
4. Selezionare **Salva**.

>[!NOTE]
> La disabilitazione del provisioning automatico non rimuove Microsoft Monitoring Agent dalle macchine virtuali di Azure in cui è stato eseguito il provisioning dell'agente. La disabilitazione automatica del provisioning limita il monitoraggio delle risorse.
>

## <a name="next-steps"></a>Passaggi successivi
In questa Guida rapida è stato eseguito l'aggiornamento al livello Standard ed è stato eseguito il provisioning di Microsoft Monitoring Agent per la gestione unificata della sicurezza e della protezione dalle minacce per i carichi di lavoro di cloud ibrido. Per altre informazioni su come usare il Centro sicurezza, continuare con le indicazioni presenti nella Guida introduttiva per l'onboarding di computer Windows in locale o in altri ambienti cloud.

> [!div class="nextstepaction"]
> [Guida introduttiva: Onboarding di computer Windows in Centro sicurezza](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
