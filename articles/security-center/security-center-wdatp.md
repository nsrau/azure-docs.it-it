---
title: Microsoft Defender Advanced Threat Protection-Centro sicurezza di Azure
description: Questo documento introduce l'integrazione tra il Centro sicurezza di Azure e Microsoft Defender Advanced Threat Protection.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: memildin
ms.openlocfilehash: 92feb159fe4c893a55d37fa90c34acf4c4c93631
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826165"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Microsoft Defender Advanced Threat Protection con il Centro sicurezza di Azure

Il Centro sicurezza di Azure si integra con [Microsoft Defender Advanced Threat Protection (ATP)](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) per fornire funzionalità complete di rilevamento e risposta degli endpoint.


## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|È necessario [Azure Defender](security-center-pricing.md)|
|Computer supportati:|![Sì](./media/icons/yes-icon.png) Macchine virtuali di Azure che eseguono Windows<br>![Sì](./media/icons/yes-icon.png) Computer Azure Arc che eseguono Windows|
|Autorizzazioni e ruoli obbligatori:|Per abilitare o disabilitare l'integrazione: **amministratore della sicurezza** o **proprietario**<br>Per visualizzare gli avvisi MDATP nel centro sicurezza: **lettore sicurezza**, **lettore**, **collaboratore gruppo di risorse**, **proprietario del gruppo di risorse**, amministratore della **sicurezza**, **proprietario della sottoscrizione**o **collaboratore della sottoscrizione**|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali.<br>![No](./media/icons/no-icon.png) Clienti GCC che eseguono carichi di lavoro in cloud di Azure pubblici<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Governo cinese, altri governi|
|||


## <a name="microsoft-defender-atp-features-in-security-center"></a>Funzionalità di Microsoft Defender ATP nel centro sicurezza

Microsoft Defender ATP fornisce:

- **Sensori di rilevamento post-violazione avanzati**: i sensori di Microsoft Defender ATP per i server Windows raccolgono una vasta gamma di segnali comportamentali.

- **Rilevamento di violazioni post basate su analisi basate sul cloud**: Microsoft Defender ATP si adatta rapidamente alle mutevoli minacce. sfruttando strumenti di analisi avanzata e Big Data. Microsoft Defender ATP è stato amplificato dalla potenza del Intelligent Security Graph con segnali in Windows, Azure e Office per rilevare le minacce sconosciute. Fornisce avvisi interattivi e consente di reagire con tempestività.

- **Intelligence**per le minacce: Microsoft Defender ATP genera avvisi quando identifica gli strumenti, le tecniche e le procedure per gli utenti malintenzionati. Usa i dati generati dagli specialisti di minacce e dai team di sicurezza di Microsoft, integrati con intelligence fornita da partner.


Integrando Defender ATP con il Centro sicurezza di Azure, è anche possibile sfruttare le funzionalità aggiuntive seguenti:

- **Onboarding automatico**: l'integrazione abilita automaticamente il sensore Microsoft Defender ATP per i server Windows monitorati dal centro sicurezza di Azure, a meno che non eseguano Windows Server 2019.

- **Riquadro singolo**: nella console del Centro sicurezza di Azure vengono visualizzati gli avvisi di Microsoft Defender ATP. Per ulteriori approfondimenti, utilizzare Microsoft Defender ATP. Microsoft Defender ATP fornisce informazioni aggiuntive, ad esempio l'albero del processo di avviso e il grafico dell'evento imprevisto. È anche possibile visualizzare una sequenza temporale dettagliata che mostra ogni comportamento per un determinato periodo, fino a un massimo di sei mesi.

    ![Pagina di Microsoft Defender ATP con informazioni dettagliate su un avviso](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Piattaforme supportate

Microsoft Defender ATP nel centro sicurezza supporta il rilevamento in Windows Server 2016, 2012 R2 e 2008 R2 SP1. Per le macchine virtuali di Azure è necessario un per abilitare Azure Defender sulla sottoscrizione e per le macchine virtuali non di Azure è necessario abilitare Azure Defender solo a livello di area di lavoro.

Il monitoraggio degli endpoint server con questa integrazione è stato disabilitato per i clienti di Office 365 GCC.

## <a name="data-storage-location"></a>Percorso di archiviazione dei dati

Quando si usa il Centro sicurezza di Azure per monitorare i server, viene creato automaticamente un tenant di Microsoft Defender ATP. I dati raccolti da Microsoft Defender ATP vengono archiviati nella posizione geografica del tenant, come identificato durante il provisioning. I dati dei clienti in formato con pseudonimo possono anche essere archiviati nei sistemi di archiviazione e di elaborazione centrali del Stati Uniti. 

Dopo averla configurata, non è possibile modificare la posizione in cui vengono archiviati i dati. Se è necessario spostare i dati in un altro percorso, contattare supporto tecnico Microsoft per reimpostare il tenant.


## <a name="onboard-servers-to-security-center"></a>Eseguire l'onboarding di server nel centro sicurezza 

Per caricare i server nel centro sicurezza, fare clic su **Vai al centro sicurezza di Azure per** caricare i server dall'onboarding del server Microsoft Defender ATP.

1. Nell'area **onboarding** selezionare o creare un'area di lavoro in cui archiviare i dati.

2. Se non è possibile visualizzare tutte le aree di lavoro, il problema potrebbe essere dovuto a una mancanza di autorizzazioni, assicurarsi che l'area di lavoro sia protetta da Azure Defender.
    
3. Selezionare **Aggiungi server** per visualizzare le istruzioni su come installare l'agente di log Analytics. 

4. Dopo l'onboarding, è possibile monitorare i computer nell' [inventario asset](asset-inventory.md).

   ![Onboarding di computer](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Abilita l'integrazione di Microsoft Defender ATP

Per visualizzare se l'integrazione di Microsoft Defender ATP è abilitata, selezionare il **Centro sicurezza**  >  **prezzi & impostazioni** > selezionare la sottoscrizione.

In questa schermata è possibile visualizzare le integrazioni attualmente abilitate.

  ![Pagina delle impostazioni di rilevamento delle minacce del Centro sicurezza di Azure con l'integrazione di Microsoft Defender ATP abilitata](media/security-center-wdatp/enable-integrations.png)

- Se Azure Defender è già stato abilitato, non è necessaria alcuna azione aggiuntiva. Il Centro sicurezza di Azure effettuerà automaticamente l'onboarding dei server a Microsoft Defender ATP. Il caricamento potrebbe richiedere fino a 24 ore.

- Se i server non sono mai stati caricati nel centro sicurezza di Azure, caricarli nel centro sicurezza di Azure e abilitare Azure Defender come di consueto.

- Se i server sono stati caricati tramite Microsoft Defender ATP:
  - Vedere la documentazione per istruzioni su [come eseguire l'offboarding dei computer server](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Eseguire l'onboarding di questi server nel Centro sicurezza di Azure.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Accesso al portale di Microsoft Defender ATP

1. Seguire le istruzioni riportate in [Assegnare l'accesso degli utenti al portale](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Controllare se è presente un proxy o un firewall che blocca il traffico anonimo. Il sensore Defender ATP si connette dal contesto del sistema, quindi è necessario consentire il traffico anonimo. Per garantire l'accesso senza compromessi al portale di Microsoft Defender ATP, seguire le istruzioni in [abilitare l'accesso agli URL del servizio Microsoft Defender ATP nel server proxy](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testare la funzionalità

Per generare un avviso di test di Microsoft Defender ATP benigno:

1. Creare una cartella ' C:\test-MDATP-test '.

1. Usare Desktop remoto per accedere a una macchina virtuale Windows Server 2012 R2 o Windows Server 2016. Aprire una finestra della riga di comando.

1. Al prompt copiare ed eseguire il comando seguente. La finestra del prompt dei comandi verrà chiusa automaticamente.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Finestra del prompt dei comandi con il comando precedente](media/security-center-wdatp/image4.jpeg)

1. Se il comando ha esito positivo, verrà visualizzato un nuovo avviso nel dashboard del Centro sicurezza di Azure e nel portale di Microsoft Defender ATP. Possono trascorrere alcuni minuti prima che l'avviso venga visualizzato.

1. Per esaminare l'avviso nel centro sicurezza, passare a **avvisi di sicurezza**riga di comando di  >  **PowerShell sospetti**.

1. Nella finestra di analisi selezionare il collegamento per passare al portale di Microsoft Defender ATP.

## <a name="next-steps"></a>Passaggi successivi

- [Funzionalità e piattaforme supportate dal Centro sicurezza di Azure](security-center-os-coverage.md)
- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.