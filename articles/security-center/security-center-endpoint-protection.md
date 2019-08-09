---
title: Individuazione e valutazione dell'integrità delle soluzioni di Endpoint Protection nel centro sicurezza di Azure | Microsoft Docs
description: Il modo in cui le soluzioni di Endpoint Protection vengono individuate e identificate come integre.
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
ms.date: 08/08/2019
ms.author: v-mohabe
ms.openlocfilehash: 4d3fc90a722b9f4043e891a14b542e6b90c94c55
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881042"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Valutazione e raccomandazioni di Endpoint Protection nel centro sicurezza di Azure

Valutazione e raccomandazioni di Endpoint Protection nel centro sicurezza di Azure rileva e fornisce la valutazione dell'integrità delle versioni [supportate](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) delle soluzioni di Endpoint Protection. Questo argomento illustra gli scenari che generano i due consigli seguenti per le soluzioni di Endpoint Protection dal centro sicurezza di Azure.

* **Installare soluzioni Endpoint Protection nella macchina virtuale**
* **Risolvere i problemi di integrità di Endpoint Protection nei computer**

## <a name="windows-defender"></a>Windows Defender

* La raccomandazione **"installa soluzioni Endpoint Protection nella macchina virtuale"** viene generata quando viene eseguito [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) e il risultato **è AMServiceEnabled: False**

* La raccomandazione **"Risolvi i problemi di integrità di Endpoint Protection nei computer"** viene generata quando viene eseguito [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) e si verificano entrambe le condizioni seguenti:

  * Almeno una delle proprietà seguenti è false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Se una o entrambe le proprietà seguenti sono maggiori o uguali a 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* La raccomandazione **"installa soluzioni Endpoint Protection nella macchina virtuale"** viene generata durante l'importazione di **SCEPMpModule ("$env:P rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1")** e l'esecuzione **di Risultati di Get-MProtComputerStatus** con **AMServiceEnabled = false**

* La raccomandazione **"Risolvi i problemi di integrità di Endpoint Protection nei computer"** viene generata quando viene eseguito **Get-MprotComputerStatus** e si verificano entrambe le condizioni seguenti:

    * Almeno una delle proprietà seguenti è false:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Se uno o entrambi gli aggiornamenti della firma seguenti sono maggiori o uguali a 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* La raccomandazione **"installa soluzioni Endpoint Protection nella macchina virtuale"** viene generata se uno o più dei controlli seguenti non sono soddisfatti:
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent** esiste
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** exists
    * Il file **dsq_query. cmd** si trova nella cartella di installazione
    * Esecuzione di **dsa_query. cmd** risultati con **Component. am. Mode: rilevato un agente di sicurezza Deep micro-trend**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
La raccomandazione **"installa soluzioni Endpoint Protection nella macchina virtuale"** viene generata se uno dei controlli seguenti non viene soddisfatto:

* **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Oppure

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Il suggerimento **"risolvere i problemi di integrità di Endpoint Protection nei computer"** viene generato se uno dei controlli seguenti non viene soddisfatto:  

* Controllare la versione di Symantec > = 12:  Percorso del registro di sistema: **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion "-value" PRODUCTVERSION "**

* Controllare lo stato di protezione in tempo reale: **HKLM: \ Software\Wow6432Node\Symantec\Symantec endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**

* Verificare lo stato di aggiornamento della firma: **< Protection\CurrentVersion\public-opstate\LatestVirusDefsDate endpoint HKLM\Software\Symantec\Symantec = 7 giorni**

* Controllare lo stato dell'analisi completa: **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime < = 7 giorni**

* Trova firma numero di versione percorso della firma per Symantec 12: **Percorsi del registro di sistema + "CurrentVersion\SharedDefs"-valore "SRTSP"** 

* Percorso della versione della firma per Symantec 14: **Percorsi del registro di sistema + "CurrentVersion\SharedDefs\SDSDefs"-valore "SRTSP"**

Percorsi del registro di sistema:

* **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection per Windows

Viene generata la raccomandazione **"installa soluzioni Endpoint Protection nella macchina virtuale"** se i controlli seguenti non sono soddisfatti:

* **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** esiste

* **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Viene generata la raccomandazione **"Risolvi i problemi di integrità di Endpoint Protection nei computer"** se i controlli seguenti non sono soddisfatti:

* Versione di McAfee: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* Trova versione firma: **HKLM: \ Software\McAfee\AVSolution\DS\DS-value "dwContentMajorVersion"**

* Trova data firma: **HKLM: \ Software\McAfee\AVSolution\DS\DS-value "szContentCreationDate" > = 7 giorni**

* Trova data analisi: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-value "LastFullScanOdsRunTime" > = 7 giorni**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security per Linux Threat Prevention 

Se uno o entrambi i controlli seguenti non sono soddisfatti, viene generata la raccomandazione **installa soluzioni Endpoint Protection sulle macchine virtuali** :  

- Uscite file **/opt/ISEC/ENS/threatprevention/bin/isecav** 

- l'output **di "/opt/ISEC/ENS/threatprevention/bin/isecav-Version"** è: **Nome McAfee = McAfee Endpoint Security per Linux Threat Prevention e McAfee version > = 10**

Se uno o più dei controlli seguenti non sono soddisfatti, viene generato il suggerimento **risolvere i problemi di integrità di Endpoint Protection sulle macchine virtuali** :

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** restituisce un'analisi **veloce, un'analisi completa** ed entrambe le analisi < = 7 giorni

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** restituisce la **data e l'ora di aggiornamento del motore** e di entrambi < = 7 giorni

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--getoasconfig--Summary"** restituisce lo stato **di analisi dell'accesso**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus per Linux 

Se uno o entrambi i controlli seguenti non sono soddisfatti, viene generata la raccomandazione **installa soluzioni Endpoint Protection sulle macchine virtuali** :

- Il file **/opt/Sophos-AV/bin/savdstatus** viene chiuso o cerca il percorso personalizzato **"readlink $ (savscan)"**

- **"/opt/Sophos-AV/bin/savdstatus--Version"** restituisce Sophos Name = **Sophos Anti-virus e Sophos version > = 9**

Se uno o più dei controlli seguenti non sono soddisfatti, viene generato il suggerimento **risolvere i problemi di integrità di Endpoint Protection sulle macchine virtuali** :

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep-i "analisi pianificata. completato\* "| Tail-1"** , restituisce un valore   

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep "analisi completata"** | Tail-1 ", restituisce un valore   

- **"/opt/Sophos-AV/bin/savdstatus--LastUpdate"** restituisce LastUpdate che deve essere < = 7 giorni 

- **"/opt/Sophos-AV/bin/savdstatus-v"** è uguale a **"l'analisi in corso è in esecuzione"** 

- **"/opt/Sophos-AV/bin/savconfig Get LiveProtection"** restituisce Enabled  

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I log dell'estensione Microsoft antimalware sono disponibili all'indirizzo:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (o PaaSAntimalware) \1.5.5.x (versione #) \CommandExecution.log**

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto del supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
