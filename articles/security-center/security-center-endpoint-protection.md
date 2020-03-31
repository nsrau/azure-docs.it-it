---
title: Endpoint protection recommendations in Azure Security Centers
description: Come le soluzioni di protezione degli endpoint vengono individuate e identificate come integre.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208543"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint protection assessment and recommendations in Azure Security Center

Il Centro sicurezza di Azure offre valutazioni dell'integrità delle versioni [supportate](security-center-services.md#endpoint-supported) delle soluzioni di protezione degli endpoint. In questo articolo vengono illustrati gli scenari che portano il Centro sicurezza per generare i due consigli seguenti:This article explains the scenarios that lead Security Center to generate the following two recommendations:

* **Installare soluzioni di protezione degli endpoint nella macchina virtualeInstall endpoint protection solutions on your virtual machine**
* **Risolvere i problemi di integrità della protezione degli endpoint nei computer**

## <a name="windows-defender"></a>Windows Defender

* Il Centro sicurezza consiglia di **"Installare soluzioni** di protezione endpoint nella macchina virtuale" quando viene eseguito [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) e il risultato è **AMServiceEnabled: False**

* Il Centro sicurezza consiglia di "Risolvere i problemi di integrità di **protezione degli endpoint nei computer"** quando viene eseguito [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) e si verifica una delle situazioni seguenti:

  * Una delle seguenti proprietà è false:

    **AMServiceEnabled**

    **AntispywareAbilitato**

    **RealTimeProtectionEnabled**

    **BehaviorMonitorEnabled**

    **IoavProtectionEnabled**

    **OnAccessProtectionEnabled**

  * Se una o entrambe le seguenti proprietà sono 7 o più.

    **AntispywareSignatureAge**

    **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Protezione degli endpoint di Microsoft System Center

* Il Centro sicurezza consiglia di **"Installare soluzioni** di protezione degli endpoint nella macchina virtuale" durante l'importazione di **SCEPMpModule ("$env:ProgramFiles" Microsoft Security Client, MpProvider, MpProvider, MpProvider e MpProvider.psd1)** e l'esecuzione dei risultati di **Get-MProtComputerStatus** con **AMServiceEnabled - false**

* Il Centro sicurezza consiglia di "Risolvere i problemi di integrità di **protezione dell'endpoint nei computer"** quando viene eseguito **Get-MprotComputerStatus** e si verifica una delle situazioni seguenti:

    * Almeno una delle seguenti proprietà è false:

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * Se uno o entrambi gli aggiornamenti della firma seguenti sono maggiori o uguali a 7. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Tendenza Micro

* Il Centro sicurezza consiglia di "Installare soluzioni di protezione endpoint nella macchina virtuale" quando non vengono soddisfatti i controlli seguenti:Security Center recommends **you "Install endpoint protection solutions on virtual machine"** when any of the following checks aren't met:
    * **HKLM:**
    * **HKLM:**
    * Il file **dsa_query.cmd** si trova nella cartella di installazione
    * Esecuzione dei risultati **di dsa_query.cmd** con **Component.AM.mode: on - Trend Micro Deep Security Agent rilevato**

## <a name="symantec-endpoint-protection"></a>Protezione endpoint Symantec
Il Centro sicurezza consiglia di "Installare soluzioni di protezione endpoint nella macchina virtuale" quando non vengono soddisfatti i controlli seguenti:Security Center recommends **you "Install endpoint protection solutions on virtual machine"** when any of the following checks aren't met:

* **HKLM: . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .**

* **HKLM: . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .**

Oppure

* **HKLM: . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .**

* **HKLM: . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .**

Il Centro sicurezza consiglia di "Risolvere i problemi di integrità della protezione degli endpoint nei **computer"** quando non vengono soddisfatti i controlli seguenti:

* Controllare la versione di Symantec >12: posizione del Registro di sistema: **HKLM:**

* Controllare lo stato della protezione in tempo reale: **HKLM:**

* Controllare lo stato dell'aggiornamento della firma: **H <KLM**

* Controllare lo stato dell'analisi completa: **HKLM <:**

* Trovare il numero di versione della firma Percorso della versione della firma per Symantec 12: **Percorsi del Registro di sistema "CurrentVersion"SharedDefs" -Valore "SRTSP"** 

* Percorso della versione della firma per Symantec 14: **Percorsi del Registro di sistema "CurrentVersion"SharedDefs"SDSDefs" - Valore "SRTSP"**

Percorsi del Registro di sistema:

* **"HKLM:'Software'Symantec'Symantec Endpoint Protection" - $Path;**
* **"HKLM:'Software'Wow6432Node'Symantec'Symantec Endpoint Protection" ( $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Protezione degli endpoint McAfee per Windows

Il Centro sicurezza consiglia di "Installare soluzioni di protezione endpoint nella macchina virtuale" quando non vengono soddisfatti i controlli seguenti:Security Center recommends **you "Install endpoint protection solutions on virtual machine"** when any of the following checks aren't met:

* **HKLM: SOFTWARE , McAfee , Endpoint , AV , ProductVersion** esistente

* **HKLM:**

Il Centro sicurezza consiglia di "Risolvere i problemi di integrità della protezione degli endpoint nei **computer"** quando non vengono soddisfatti i controlli seguenti:

* Versione McAfee: **HKLM >:**

* Trovare la versione della firma: **HKLM: .** . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .

* Trova la data della firma: **HKLM >: .** . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .

* Trova la data di scansione: **HKLM >:**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security per la prevenzione delle minacce Linux 

Il Centro sicurezza consiglia di "Installare soluzioni di protezione endpoint nella macchina virtuale" quando non vengono soddisfatti i controlli seguenti:Security Center recommends **you "Install endpoint protection solutions on virtual machine"** when any of the following checks aren't met:

- File **/opt/isec/ens/threatprevention/bin/isecav** esce 

- **L'output "/opt/isec/ens/threatprevention/bin/isecav--version"** è: **McAfee name - McAfee Endpoint Security for Linux Threat Prevention and McAfee version >**

Il Centro sicurezza consiglia di "Risolvere i problemi di integrità della protezione degli endpoint nei **computer"** quando non vengono soddisfatti i controlli seguenti:

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** restituisce **Scansione rapida, Scansione completa** ed entrambe le scansioni <

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** restituisce **DAT e** il tempo di aggiornamento del motore ed entrambi <7 giorni

- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** restituisce lo stato **All'analisi di accesso**

## <a name="sophos-antivirus-for-linux"></a>Antivirus Sophos per Linux 

Il Centro sicurezza consiglia di "Installare soluzioni di protezione endpoint nella macchina virtuale" quando non vengono soddisfatti i controlli seguenti:Security Center recommends **you "Install endpoint protection solutions on virtual machine"** when any of the following checks aren't met:

- File **/opt/sophos-av/bin/savdstatus** esce o cercare la posizione personalizzata **"readlink s(which savscan)"**

- **"/opt/sophos-av/bin/savdstatus --version"** restituisce il nome di Sophos - **Sophos Anti-Virus e Sophos versione >**

Il Centro sicurezza consiglia di "Risolvere i problemi di integrità della protezione degli endpoint nei **computer"** quando non vengono soddisfatti i controlli seguenti:

- **"/opt/sophos-av/bin/savlog --maxage grep -i "Scansione pianificata . \* completato" . tail -1"**, restituisce un valore

- **"/opt/sophos-av/bin/savlog --maxage grep "scansione finita"** coda -1", restituisce un valore

- **"/opt/sophos-av/bin/savdstatus --lastupdate"** restituisce lastUpdate, che deve essere <7 giorni 

- **"/opt/sophos-av/bin/savdstatus -v"** è uguale a **"Scansione all'accesso in esecuzione"** 

- **"/opt/sophos-av/bin/savconfig get LiveProtection"** restituisce abilitato

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere i problemi

I log delle estensioni di Microsoft Antimalware sono disponibili **all'indirizzo: %Systemdrive%**

### <a name="support"></a>Supporto

Per altre informazioni, contattare gli esperti di Azure nei [forum MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Oppure presentare un incidente di supporto di Azure.Or file an Azure support incident. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).