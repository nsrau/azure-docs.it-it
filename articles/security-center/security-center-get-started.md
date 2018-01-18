---
title: Guida introduttiva di Centro sicurezza di Azure - Caricamento della sottoscrizione di Azure al livello Standard del Centro sicurezza di Azure | Microsoft Docs
description: Questa Guida introduttiva illustra come eseguire l'aggiornamento al piano tariffario Standard del Centro sicurezza per aumentare la sicurezza.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2018
ms.author: terrylan
ms.openlocfilehash: ac4e3b36b08223f7e3b54850ed53a8185e85f0d2
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
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
- Una serie di risorse nei riquadri **Calcolo**, **Rete**, **Archiviazione e dati** e **Applicazioni** valutate dal Centro sicurezza con le condizioni di sicurezza di ognuna.

Per sfruttare al meglio il Centro sicurezza, è necessario completare i passaggi seguenti per eseguire l'aggiornamento al livello Standard e installare Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Eseguire l'aggiornamento al livello Standard
Ai fini della Guida introduttiva e delle esercitazioni per il Centro di sicurezza è necessario eseguire l'aggiornamento al livello Standard. I primi 60 giorni sono gratuiti ed è possibile tornare al livello gratuito in qualsiasi momento.

1. Selezionare **Onboarding nella sicurezza avanzata** nel menu principale del Centro sicurezza.

2. In **Onboarding nella sicurezza avanzata** il Centro sicurezza di Azure elenca tutte le sottoscrizioni e i carichi di lavoro idonei per il caricamento. Selezionare una sottoscrizione nell'elenco.

  ![Selezionare una sottoscrizione][4]

3. L'opzione **Criteri di sicurezza** offre informazioni sui gruppi di risorse contenuti nella sottoscrizione. Viene inoltre visualizzata la finestra **Prezzi**.
4. In **Prezzi** selezionare **Standard** per eseguire l'aggiornamento da gratuito a standard e quindi fare clic su **Salva**.

  ![Selezionare Standard][5]

Ora che è stato eseguito l'aggiornamento al livello Standard, si dispone di accesso alle funzionalità aggiuntive del Centro sicurezza, ad esempio **controlli adattivi sulle applicazioni**, **accesso Just-In-Time alle macchine virtuali**, **avvisi di sicurezza**, **funzioni intelligenti contro le minacce**, **playbook di automazione** e altro ancora. Si noti che gli avvisi di sicurezza verranno visualizzata solo quando il Centro sicurezza rileva attività dannose.

  ![Avvisi di sicurezza][7]

## <a name="automate-data-collection"></a>Automatizzare la raccolta dei dati
Il Centro sicurezza raccoglie i dati delle macchine virtuali di Azure e dei computer senza Azure per monitorare le minacce e le vulnerabilità della sicurezza. I dati vengono raccolti tramite Microsoft Monitoring Agent, che legge diverse configurazioni correlate alla sicurezza oltre ai registri eventi del computer e copia i dati nell'area di lavoro per eseguire l'analisi. Per impostazione predefinita, il Centro sicurezza crea una nuova area di lavoro per l'utente.

Dopo aver abilitato il provisioning automatico, il Centro sicurezza installa Microsoft Monitoring Agent in tutte le macchine virtuali di Azure supportate e in quelle nuove che vengono create. È consigliabile abilitare il provisioning automatico.

Per abilitare il provisioning automatico di Microsoft Monitoring Agent:

1. selezionare **Criteri di sicurezza** nel menu principale Centro sicurezza.
2. Selezionare la sottoscrizione.
3. In **Criteri di sicurezza** selezionare **Raccolta dati**.
4. In **Raccolta dati** selezionare l'opzione di attivazione**per** abilitare il provisioning automatico.
5. Selezionare **Salva**.

  ![Abilitare il provisioning automatico][6]

Grazie a queste nuove informazioni sulle macchine virtuali di Azure, il Centro sicurezza può offrire indicazioni aggiuntive correlate allo stato di aggiornamento del sistema, alle configurazioni di sicurezza del sistema operativo e alla protezione di endpoint, nonché generare avvisi di sicurezza aggiuntivi.

  ![Raccomandazioni][8]

## <a name="clean-up-resources"></a>Pulire le risorse
Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare le guide introduttive e le esercitazioni successive, continuare a eseguire il livello Standard e tenere abilitato il provisioning automatico. Se non si intende proseguire oppure si vuole tornare al livello gratuito:

1. Tornare al menu principale del Centro sicurezza e selezionare **Criteri di sicurezza**.
2. Selezionare la sottoscrizione o i criteri per i quali si vuole tornare al livello gratuito. Viene visualizzata la finestra **Criteri di sicurezza**.
3. In **COMPONENTI DEI CRITERI** selezionare **Piano tariffario**.
4. Selezionare **Gratuito** per modificare il livello della sottoscrizione da Standard a Gratuito.
5. Selezionare **Salva**.

Se si vuole disabilitare il provisioning automatico:

1. Tornare al menu principale del Centro sicurezza e selezionare **Criteri di sicurezza**.
2. Selezionare la sottoscrizione per cui si desidera disabilitare il provisioning automatico.
3. In **Criteri di sicurezza - Raccolta dati** selezionare **No** in **Onboarding** per disabilitare il provisioning automatico.
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
[4]: ./media/security-center-get-started/onboarding.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
