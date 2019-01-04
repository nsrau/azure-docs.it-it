---
title: Windows Defender Advanced Threat Protection con il Centro sicurezza di Azure
description: Questo documento illustra l'integrazione tra il Centro sicurezza di Azure e Windows Defender Advanced Threat Protection.
services: security-center
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 1109e72d00ccddd6fc0f120ee71c88adaae72fed
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337598"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Windows Defender Advanced Threat Protection con il Centro sicurezza di Azure

Il Centro sicurezza di Azure estende l'offerta di piattaforme di protezione del carico di lavoro cloud grazie all'integrazione con [Windows Defender Advanced Threat Protection](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP),
introducendo così funzionalità EDR (Endpoint Detection and Response) complete. Con l'integrazione di Windows Defender ATP, è possibile individuare eventuali anomalie, nonché rilevare e rispondere agli attacchi avanzati negli endpoint server monitorati dal Centro sicurezza di Azure.

I clienti del Centro sicurezza di Azure possono ora usare le funzionalità di Windows Defender ATP:

- **Sensori di rilevamento post-violazione di ultima generazione**: i sensori di Windows Defender ATP per i server Windows raccolgono un'ampia gamma di segnali comportamentali.

- **Rilevamento post-violazione basato sul cloud e sull'analisi**: Windows Defender ATP si adatta rapidamente alle mutevoli minacce sfruttando strumenti di analisi avanzata e Big Data. La capacità di Windows Defender ATP è ampliata dalla potenza di Intelligent Security Graph con segnali attraverso Windows, Azure e Office per rilevare minacce sconosciute. Fornisce avvisi interattivi e consente di reagire con tempestività.

- **Intelligence per le minacce**: Windows Defender ATP identifica gli strumenti, le tecniche e le procedure degli utenti malintenzionati. Non appena questi vengono rilevati, genera avvisi. Usa i dati generati dagli specialisti di minacce e dai team di sicurezza di Microsoft, integrati con intelligence fornita da partner.

Nel Centro sicurezza di Azure sono ora disponibili queste funzionalità:

- **Onboarding automatico**: il sensore di Windows Defender ATP viene abilitato automaticamente per i server Windows di cui viene eseguito l'onboarding nel Centro sicurezza di Azure.

- **Interfaccia unificata**: la console del Centro sicurezza di Azure visualizza gli avvisi di Windows Defender ATP.

- **Analisi dettagliata dei sistemi**: i clienti del Centro sicurezza di Azure possono accedere alla console di Windows Defender ATP per eseguire un'analisi dettagliata allo scopo di individuare l'ambito di una violazione.

![Il Centro sicurezza di Azure, con un elenco di avvisi e informazioni generali relative a ogni avviso](media/security-center-wdatp/image1.png)

È possibile [analizzare l'avviso](security-center-investigation.md) nel Centro sicurezza di Azure:

![Dashboard per l'analisi degli avvisi nel Centro sicurezza di Azure](media/security-center-wdatp/image2.png)

È possibile eseguire un'analisi più approfondita dell'avviso passando a Windows Defender ATP, in cui sono visualizzate informazioni aggiuntive, ad esempio l'albero dei processi di avviso e il grafico degli eventi imprevisti. È anche possibile visualizzare una sequenza temporale dettagliata che mostra ogni comportamento per un determinato periodo, fino a un massimo di sei mesi.

![Pagina di Windows Defender ATP con informazioni dettagliate su un avviso](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Supporto delle piattaforme

Questa funzionalità supporta il rilevamento in Windows Server 2012 R2 e Windows Server 2016.

Sono supportati solo i server in sottoscrizioni per il livello di servizio Standard.

## <a name="onboarding-instructions"></a>Istruzioni per l'onboarding

Per verificare se l'integrazione di Windows Defender ATP è abilitata, selezionare **Centro sicurezza** > **Criteri di sicurezza** > **Sottoscrizione** > **Modifica impostazioni**.

  ![Gestione dei criteri del Centro sicurezza di Azure](media/security-center-wdatp/policy-management.png)

In questa schermata è possibile visualizzare le integrazioni attualmente abilitate.

  ![Pagina delle impostazioni di rilevamento delle minacce del Centro sicurezza di Azure con l'integrazione di Windows Defender ATP abilitata](media/security-center-wdatp/enable-integrations.png)

- Se si è già eseguito l'onboarding dei server nel livello Standard del Centro sicurezza di Azure, non è necessario eseguire altre operazioni. Il Centro sicurezza di Azure eseguirà automaticamente l'onboarding dei server in Windows Defender ATP. Questo processo può richiedere fino a 24 ore.

- Se non è mai stato eseguito l'onboarding dei server nel livello Standard del Centro sicurezza di Azure, eseguirne l'onboarding nel Centro sicurezza di Azure come di consueto.

- Se si è eseguito l'onboarding dei server tramite Windows Defender ATP:
  - Vedere la documentazione per istruzioni su [come eseguire l'offboarding dei computer server](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Eseguire l'onboarding di questi server nel Centro sicurezza di Azure.

## <a name="access-to-the-windows-defender-atp-portal"></a>Accedere al portale di Windows Defender ATP

Seguire le istruzioni riportate in [Assegnare l'accesso degli utenti al portale](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>Impostare la configurazione del firewall

Se si ha un proxy o un firewall che blocca il traffico anonimo, mentre un sensore di Windows Defender ATP si connette dal contesto del sistema, assicurarsi che sia consentito il traffico anonimo. Seguire le istruzioni in [Abilitare l'accesso agli URL del servizio Windows Defender ATP nel server proxy](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testare la funzionalità

Per generare un avviso di prova di Windows Defender ATP non dannoso:

1. Usare Desktop remoto per accedere a una macchina virtuale Windows Server 2012 R2 o Windows Server 2016.  Aprire una finestra del prompt dei comandi.

2. Al prompt copiare ed eseguire il comando seguente. La finestra del prompt dei comandi verrà chiusa automaticamente.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Finestra del prompt dei comandi con il comando precedente](media/security-center-wdatp/image4.jpeg)

3. Se il comando ha esito positivo, verrà visualizzato un nuovo avviso sul dashboard del Centro sicurezza di Azure e nel portale di Windows Defender ATP. Possono trascorrere alcuni minuti prima che l'avviso venga visualizzato.

4. Per esaminare l'avviso nel Centro sicurezza, passare ad **Avvisi di sicurezza** >  **Suspicious Powershell CommandLine** (Riga di comando PowerShell sospetta).

5. Nella finestra per l'analisi selezionare il collegamento che consente di passare al portale di Windows Defender ATP.

## <a name="next-steps"></a>Passaggi successivi

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): Informazioni su come monitorare l'integrità delle risorse di Azure.
