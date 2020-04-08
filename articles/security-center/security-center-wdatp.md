---
title: Microsoft Defender Advanced Threat Protection - Centro sicurezza di Azure
description: Questo documento introduce l'integrazione tra Centro sicurezza di Azure e Microsoft Defender Advanced Threat Protection.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: memildin
ms.openlocfilehash: a502541a5a01ec3304338054239fc3b50f4d3c61
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810300"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Microsoft Defender Advanced Threat Protection con Il Centro sicurezza di Azure

Il Centro sicurezza di Azure estende l'offerta di piattaforme di protezione da carico di lavoro cloud tramite l'integrazione con [Microsoft Defender Advanced Threat Protection](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP).
introducendo così funzionalità EDR (Endpoint Detection and Response) complete. Con l'integrazione di Microsoft Defender ATP, è possibile individuare le anomalie. nonché rilevare e rispondere agli attacchi avanzati negli endpoint server monitorati dal Centro sicurezza di Azure.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Funzionalità di Microsoft Defender ATP nel Centro sicurezza

Quando si utilizza Microsoft Defender ATP, si ottiene:

- Sensori avanzati di **rilevamento post-violazione**: I sensori ATP di Microsoft Defender per i server Windows raccolgono una vasta gamma di segnali comportamentali.

- **Rilevamento post-violazione basato su Analytics**e basato su cloud: Microsoft Defender ATP si adatta rapidamente alle minacce in evoluzione. sfruttando strumenti di analisi avanzata e Big Data. Microsoft Defender ATP è amplificato dalla potenza di Intelligent Security Graph con segnali in Windows, Azure e Office per rilevare minacce sconosciute. Fornisce avvisi interattivi e consente di reagire con tempestività.

- **Threat Intelligence**: Microsoft Defender ATP genera avvisi quando identifica strumenti, tecniche e procedure degli aggressori. Usa i dati generati dagli specialisti di minacce e dai team di sicurezza di Microsoft, integrati con intelligence fornita da partner.

Nel Centro sicurezza di Azure sono ora disponibili le funzionalità seguenti:The following capabilities are now available in Azure Security Center:

- **Onboarding automatico:** il sensore AtP di Microsoft Defender viene abilitato automaticamente per i server Windows che sono connessi al Centro sicurezza di Azure (ad eccezione di quelli che eseguono Windows Server 2019).

- **Singolo riquadro di vetro:** nella console di Azure Security Center vengono visualizzati gli avvisi di Microsoft Defender ATP.

- Analisi dettagliata del **computer:** i clienti del Centro sicurezza di Azure possono usare la console Microsoft Defender ATP per condurre un'indagine dettagliata per scoprire l'ambito di una violazione.

![Il Centro sicurezza di Azure, con un elenco di avvisi e informazioni generali relative a ogni avviso](media/security-center-wdatp/image1.png)

Per ulteriori informazioni, utilizzare Microsoft Defender ATP. Microsoft Defender ATP fornisce informazioni aggiuntive, ad esempio l'albero del processo di avviso e il grafico degli eventi imprevisti. È anche possibile visualizzare una sequenza temporale dettagliata che mostra ogni comportamento per un determinato periodo, fino a un massimo di sei mesi.

![Pagina Microsoft Defender ATP con informazioni dettagliate su un avviso](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Piattaforme supportate

Microsoft Defender ATP nel Centro sicurezza supporta il rilevamento in Windows Server 2016, 2012 R2 e 2008 R2 SP1, per le macchine virtuali di Azure è necessaria una sottoscrizione di livello Standard e per le macchine virtuali non Azure è necessario il livello Standard solo nel livello dell'area di lavoro.

> [!NOTE]
> Quando si usa il Centro sicurezza di Azure per monitorare i server, viene creato automaticamente un tenant microsoft Defender ATP e i dati di Microsoft Defender ATP vengono archiviati in Europa per impostazione predefinita. Se è necessario spostare i dati in un'altra posizione, è necessario contattare il supporto tecnico Microsoft per reimpostare il tenant. Il monitoraggio degli endpoint server che utilizza questa integrazione è stato disabilitato per i clienti di Office 365 GCC.

## <a name="onboarding-servers-to-security-center"></a>Onboarding dei server al Centro sicurezza 

Per eseguire l'onboarding dei server con il Centro sicurezza, fare clic su Vai al Centro sicurezza di Azure per eseguire **l'onboarding** del server Microsoft Defender ATP.

1. Nell'area **Onboarding** selezionare o creare un'area di lavoro in cui archiviare i dati. <br>
2. Se non è possibile visualizzare tutte le aree di lavoro, è possibile che si tratti di una mancanza di autorizzazioni, verificare che l'area di lavoro sia impostata sul livello Standard di sicurezza di Azure.If you can't see all your workspaces, it may be to a lack of permissions, make sure that your workspace is set to Azure Security Standard tier. Per ulteriori informazioni, vedere [Eseguire l'aggiornamento al livello Standard del Centro sicurezza per una maggiore sicurezza.](security-center-pricing.md)
    
3. Selezionare Aggiungi server per visualizzare le istruzioni su come installare l'agente di Log Analytics.Select **Add servers** to view instructions on how to install the Log Analytics agent. 

4. Dopo l'onboarding, è possibile monitorare i computer in **Calcolo e app**.

   ![Onboarding di computer](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Abilitare l'integrazione di Microsoft Defender ATP

Per visualizzare se l'integrazione di Microsoft Defender ATP è abilitata, selezionare Impostazioni di & del **Centro** > **sicurezza &** > fare clic sull'abbonamento.
In questa schermata è possibile visualizzare le integrazioni attualmente abilitate.

  ![Pagina delle impostazioni del rilevamento delle minacce del Centro sicurezza di Azure con l'integrazione di Microsoft Defender ATP abilitataAzure Security Center threat detection settings page with Microsoft Defender ATP integration enabled](media/security-center-wdatp/enable-integrations.png)

- Se si è già eseguito l'onboarding dei server nel livello Standard del Centro sicurezza di Azure, non è necessario eseguire altre operazioni. Il Centro sicurezza di Azure verrà automaticamente riattivato dai server a Microsoft Defender ATP. L'onboarding potrebbe richiedere fino a 24 ore.

- Se non è mai stato eseguito l'onboarding dei server nel livello Standard del Centro sicurezza di Azure, eseguirne l'onboarding nel Centro sicurezza di Azure come di consueto.

- Se hai eseguito l'onboarding dei server tramite Microsoft Defender ATP:
  - Vedere la documentazione per istruzioni su [come eseguire l'offboarding dei computer server](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Eseguire l'onboarding di questi server nel Centro sicurezza di Azure.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Accesso al portale Microsoft Defender ATP

Seguire le istruzioni riportate in [Assegnare l'accesso degli utenti al portale](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>Impostare la configurazione del firewall

Se si dispone di un proxy o di un firewall che blocca il traffico anonimo, poiché un sensore Microsoft Defender ATP si connette dal contesto di sistema, assicurarsi che sia consentito il traffico anonimo. Seguire le istruzioni in [Abilitare l'accesso agli URL del servizio Microsoft Defender ATP nel server proxy.](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)

## <a name="test-the-feature"></a>Testare la funzionalità

Per generare un avviso di test di Microsoft Defender ATP benigno:

1. Creare una cartella 'C:-test-MDATP-test'.

1. Usare Desktop remoto per accedere a una macchina virtuale Windows Server 2012 R2 o Windows Server 2016. Aprire una finestra della riga di comando.

1. Al prompt copiare ed eseguire il comando seguente. La finestra del prompt dei comandi verrà chiusa automaticamente.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Finestra del prompt dei comandi con il comando precedente](media/security-center-wdatp/image4.jpeg)

1. Se il comando ha esito positivo, verrà visualizzato un nuovo avviso nel dashboard del Centro sicurezza di Azure e nel portale di Microsoft Defender ATP. Possono trascorrere alcuni minuti prima che l'avviso venga visualizzato.

1. Per esaminare l'avviso nel Centro sicurezza, passare a **Avvisi** > di sicurezza**Riga di comando powerShell sospetti**.

1. Nella finestra di indagine, selezionare il collegamento per accedere al portale Microsoft Defender ATP.

## <a name="next-steps"></a>Passaggi successivi

- [Funzionalità e piattaforme supportate dal Centro sicurezza di Azure](security-center-os-coverage.md)
- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.