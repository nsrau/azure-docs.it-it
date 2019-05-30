---
title: Endpoint protection soluzioni individuazione e valutazione dell'integrità in Centro sicurezza di Azure | Microsoft Docs
description: Come le soluzioni di protezione endpoint vengono individuate e identificate come integre.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247965"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Valutazione della protezione di endpoint e le raccomandazioni nel Centro sicurezza di Azure

Valutazione della protezione di endpoint e le raccomandazioni nel Centro sicurezza di Azure rileva e fornisce la valutazione dell'integrità del [supportato](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) versioni di soluzioni di protezione Endpoint. Questo argomento illustra gli scenari che generano le due indicazioni seguenti per le soluzioni di Endpoint protection dal Centro sicurezza di Azure.

* **Installare le soluzioni di protezione endpoint nella macchina virtuale**
* **Risolvere i problemi di integrità di endpoint protection nei computer**

## <a name="windows-defender"></a>Windows Defender

* Il **"Installare soluzioni di protezione endpoint nella macchina virtuale"** raccomandazione viene generata quando [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) esecuzioni e il risultato è **AMServiceEnabled: False**

* Il **"Per risolvere problemi di integrità di endpoint protection nei computer di"** raccomandazione viene generata quando [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) si verifica esecuzioni e una o entrambe le operazioni seguenti:

  * Almeno una delle seguenti proprietà è false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Se una o entrambe le proprietà seguenti è maggiore o uguale a 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Protezione di endpoint di Microsoft System Center

* Il **"Installare soluzioni di protezione endpoint nella macchina virtuale"** raccomandazione viene generata quando si importano **SCEPMpModule ("$env: Programmi\microsoft Security Client\MpProvider\MpProvider.psd1")** operatività **Get-MProtComputerStatus** risultante di **AMServiceEnabled = false**

* Il **"Per risolvere problemi di integrità di endpoint protection nei computer di"** raccomandazione viene generata quando **Get-MprotComputerStatus** si verifica esecuzioni e una o entrambe le operazioni seguenti:

    * Almeno una delle seguenti proprietà è false:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Se uno o entrambi i seguenti aggiornamenti delle firme è maggiore o uguale a 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Il **"Installare soluzioni di protezione endpoint nella macchina virtuale"** raccomandazione viene generata in presenza di una o più verifiche seguenti non sono soddisfatte:
    * **L'agente protezione HKLM:\SOFTWARE\TrendMicro\Deep** esiste
    * **HKLM:\SOFTWARE\TrendMicro\Deep sicurezza Agent\InstallationFolder** esiste
    * Il **dsq_query.cmd** file si trova nella cartella di installazione
    * In esecuzione **dsa_query.cmd** dei risultati con **Component.AM.mode: on - Trend Micro Deep Security Agent rilevato**

## <a name="symantec-endpoint-protection"></a>Protezione di endpoint di Symantec
Il **"Installare soluzioni di protezione endpoint nella macchina virtuale"** raccomandazione viene generata se non vengono soddisfatti tutti i controlli seguenti:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Oppure

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Il **"Per risolvere problemi di integrità di endpoint protection nei computer di"** raccomandazione viene generata se non vengono soddisfatti tutti i controlli seguenti:  

* Controllare la versione di Symantec > = 12:  Percorso del Registro di sistema: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Controllare lo stato di protezione in tempo reale: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Controllare lo stato di aggiornamento della firma: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 days**

* Controllare lo stato di analisi completa: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* Trovare il numero di versione firma percorso alla versione della firma per 12 Symantec: **I percorsi del Registro di sistema + "CurrentVersion\SharedDefs"-valore "SRTSP"** 

* Percorso della versione della firma per 14 Symantec: **I percorsi del Registro di sistema + "CurrentVersion\SharedDefs\SDSDefs"-valore "SRTSP"**

Percorsi del Registro di sistema:

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee endpoint protection per Windows

Il **"Installare soluzioni di protezione endpoint nella macchina virtuale"** raccomandazione viene generata se non vengono soddisfatti i seguenti controlli:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** esiste

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Il **"Per risolvere problemi di integrità di endpoint protection nei computer di"** raccomandazione viene generata se non vengono soddisfatti i seguenti controlli:

* McAfee versione: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Trovare versione della firma: **HKLM:\Software\McAfee\AVSolution\DS\DS-valore "dwContentMajorVersion"**

* Trovare la data di firma: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* Trovare Data analisi: **HKLM:\Software\McAfee\Endpoint\AV\ODS-valore "LastFullScanOdsRunTime" > = 7 giorni**

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

Log dell'estensione Microsoft Antimalware sono disponibili in:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile inviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
