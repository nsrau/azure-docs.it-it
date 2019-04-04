---
title: Aggiornamento di Azure Stack 1902 | Microsoft Docs
description: Informazioni sull'aggiornamento 1902 per i sistemi integrati di Azure Stack, tra cui nuove funzionalità, problemi noti e dove scaricare l'aggiornamento.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/03/2019
ms.openlocfilehash: 5971692b3e6447bc790b2e34cf84eae66979f7f5
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862081"
---
# <a name="azure-stack-1902-update"></a>Aggiornamento di Azure Stack 1902

*Si applica a Sistemi integrati di Azure Stack*

Questo articolo descrive il contenuto del pacchetto di aggiornamento 1902. L'aggiornamento include nuove funzionalità per questa versione di Azure Stack, correzioni e miglioramenti. Anche in questo articolo descrive i problemi noti in questa versione e include un collegamento per scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi correlati direttamente al processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]  
> Questo pacchetto di aggiornamento è solo per i sistemi integrati di Azure Stack. Questo pacchetto di aggiornamento non sono applicabili per Azure Stack Development Kit.

## <a name="build-reference"></a>Riferimento alla compilazione

È il numero di build aggiornamento di Azure Stack 1902 **1.1902.0.69**.

## <a name="hotfixes"></a>Hotfix

Azure Stack rilascia gli aggiornamenti rapidi a intervalli regolari. Assicurarsi di installare il [hotfix più recente di Azure Stack](#azure-stack-hotfixes) per 1901 prima di aggiornare Azure Stack per 1902.

Azure Stack hotfix sono applicabili solo ai sistemi integrati di Azure Stack. non tentare di installare gli aggiornamenti rapidi nel ASDK.

> [!TIP]  
> Sottoscrivere seguenti *RSS* oppure *Atom* i feed di stare al passo con gli aggiornamenti rapidi di Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Formato Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Aggiornamenti rapidi di Azure Stack

- **1809**: [KB 4481548 – aggiornamento rapido di Azure Stack 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Nessun hotfix corrente disponibile.
- **1901**: [KB 4495662 – aggiornamento rapido di Azure Stack 1.1901.3.105](https://support.microsoft.com/help/4495662)
- **1902**: [KB 4494719 – aggiornamento rapido di Azure Stack 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> È possibile installare 1902 direttamente dai [1.1901.0.95 o 1.1901.0.99](azure-stack-update-1901.md#build-reference) release, senza prima aver installato eventuali hotfix 1901. Tuttavia, se è stato installato il precedente **1901.2.103** hotfix, è necessario installare la versione più recente [1901.3.105 hotfix](https://support.microsoft.com/help/4495662) prima di passare alla 1902.

- Prima di iniziare l'installazione di questo aggiornamento, eseguire [Test-AzureStack](azure-stack-diagnostic-test.md) con i parametri seguenti per convalidare lo stato di Azure Stack e risolvere eventuali problemi operativi trovati, inclusi tutti gli avvisi e gli errori. Anche gli avvisi attivi rivedere e risolvere gli eventuali che richiedono un'azione:

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Quando Azure Stack è gestito da System Center Operations Manager (SCOM), assicurarsi di aggiornare il [il Management Pack per Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) alla versione 1.0.3.11 prima di applicare 1902.

- Il formato del pacchetto per l'aggiornamento di Azure Stack è stato modificato da **.bin/.exe/.xml** al **.zip/.xml** partire dalla versione 1902. Verranno visualizzati dai clienti con unità di scala di Azure Stack connessa di **aggiornamento disponibile** messaggi nel portale. I clienti che non sono connessi a questo punto è sufficiente scaricare e importare il file con estensione zip con il file. XML corrispondente.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Miglioramenti

- La compilazione 1902 introduce una nuova interfaccia utente nel portale di Azure Stack amministratore per la creazione di piani, offerte, quote e piani aggiuntivi. Per altre informazioni, incluse le schermate, vedere [creare i piani, offerte e quote](azure-stack-create-plan.md).

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Miglioramenti all'affidabilità di espansione della capacità durante l'aggiunta nodo quando si passa lo stato di unità di scala da "Archivio di espansione" in stato di esecuzione.

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Per migliorare la sicurezza e integrità del pacchetto, nonché una gestione più semplice per l'inserimento non in linea, Microsoft ha modificato il formato del pacchetto di aggiornamento dai file con estensione bin e .exe in un file con estensione zip. Il nuovo formato aggiunge garantire maggiore affidabilità del processo di decompressione che in alcuni casi, possono causare la preparazione dell'aggiornamento bloccato. Lo stesso formato di pacchetto si applica anche per aggiornare i pacchetti da OEM.
- Per migliorare l'esperienza dell'operatore Azure Stack durante l'esecuzione di Test-AzureStack, gli operatori possono ora è sufficiente usare, "Test-AzureStack-gruppo UpdateReadiness" invece di passare parametri aggiuntivi per dieci dopo un'istruzione di inclusione.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- Per migliorare la disponibilità dei servizi di infrastruttura di base e l'affidabilità complessive durante il processo di aggiornamento, il provider di risorse native di aggiornamento come parte del piano di azione di aggiornamento rileverà e richiamare correzioni globale automatica secondo necessità. I flussi di lavoro di ripristino"correzione globale" includono:
    - Verifica per le macchine virtuali dell'infrastruttura che sono in uno stato non ottimale e tenta di ripristinarli quando necessario 
    - Verificare la presenza di problemi del servizio SQL come parte del piano di controllo e tentare di ripristinare tali esigenze
    - Controllare lo stato del servizio di bilanciamento carico Software (SLB) come parte del Controller di rete (NC) e tentare di ripristinare tali esigenze
    - Controllare lo stato del servizio di Controller di rete (controller di rete) e tentare il ripristino in base alle esigenze
    - Controllare lo stato dei nodi di service fabric emergenze ripristino Console del servizio (ERCS) e ripristinarli in base alle esigenze
    - Controllare lo stato dei nodi XRP service fabric e ripristinarli in base alle esigenze
    - Controllare lo stato dei nodi di archiviazione coerenti con Azure (ACS) service fabric e ripristinarli in base alle esigenze

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Miglioramenti all'affidabilità di espansione della capacità durante l'aggiunta nodo quando si passa lo stato di unità di scala da "Archivio di espansione" in stato di esecuzione.    

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Miglioramenti apportati ad Azure stack di strumenti di diagnostica per migliorare le prestazioni e affidabilità di raccolta log. Registrazione aggiuntiva per i servizi di rete e di identità. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- Miglioramenti all'affidabilità di Test-AzureStack predisposizione di rotazione segreta per eseguire il test.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- Miglioramenti per aumentare l'affidabilità di Graph di AD durante la comunicazione con l'ambiente Active Directory del cliente

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Raccolta dell'inventario hardware miglioramenti in Get-AzureStackStampInformation.

- Per migliorare l'affidabilità delle operazioni in esecuzione sull'infrastruttura ERCS, la memoria per ogni istanza ERCS aumenta da 8 a 12 GB. In un'installazione di sistemi integrati di Azure Stack, ciò comporta un aumento di 12 GB complessiva.

> [!IMPORTANT]
> Per assicurarsi che il processo di patch e aggiornamento determina la quantità minima di tempo di inattività di tenant, verificare che l'indicatore di Azure Stack ha più di 12 GB di spazio disponibile nel **capacità** pannello. È possibile visualizzare questa memoria aumenta riflessa nel **capacità** pannello dopo la corretta installazione dell'aggiornamento.

## <a name="common-vulnerabilities-and-exposures"></a>Esposizione e vulnerabilità comuni

Questo aggiornamento installa gli aggiornamenti di sicurezza seguenti:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

Per altre informazioni su questi problemi di protezione, fare clic su collegamenti precedenti o vedere gli articoli della Microsoft Knowledge Base [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

- Quando si esegue [Test-AzureStack](azure-stack-diagnostic-test.md), viene visualizzato un messaggio di avviso da Baseboard Management Controller (BMC). È possibile ignorare questo avviso.

- <!-- 2468613 - IS --> Durante l'installazione di questo aggiornamento, si potrebbero visualizzare avvisi con il titolo `Error – Template for FaultType UserAccounts.New is missing.` è possibile ignorare questi avvisi. Gli avvisi chiudono automaticamente al termine dell'installazione dell'aggiornamento.

## <a name="post-update-steps"></a>Passaggi di post-aggiornamento

- Dopo l'installazione di questo aggiornamento, installare eventuali hotfix applicabili. Per altre informazioni, vedere [hotfix](#hotfixes), nonché nostro [criteri per la manutenzione](azure-stack-servicing-policy.md).  

- Recuperare i dati delle chiavi di crittografia di rest e archiviarle in modo sicuro di fuori della distribuzione di Azure Stack. Seguire le [istruzioni su come recuperare le chiavi](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

Di seguito sono problemi noti di post-installazione per questa versione di build.

### <a name="portal"></a>Portale

<!-- 2930820 - IS ASDK --> 
- Nei portali di amministratore e utente, se si cerca "Docker", l'elemento viene restituito in modo non corretto. Non è disponibile in Azure Stack. Se si tenta di crearla, viene visualizzato un pannello con l'indicazione di errore. 

<!-- 2931230 – IS  ASDK --> 
- Impossibile eliminare i piani che vengono aggiunti a una sottoscrizione utente come un piano del componente aggiuntivo, anche quando si rimuove il piano dalla sottoscrizione utente. Il piano rimarrà fino a quando non vengono eliminate anche le sottoscrizioni che fanno riferimento il piano aggiuntivo. 

<!-- TBD - IS ASDK --> 
- I due tipi di sottoscrizione amministrativi che sono state introdotte con la versione 1804 non devono essere utilizzati. I tipi di sottoscrizione siano **sottoscrizione di misurazione**, e **sottoscrizione consumo**. Questi tipi di sottoscrizione sono visibili in nuovi ambienti Azure Stack partire dalla versione 1804 ma non sono ancora pronti per l'uso. È consigliabile continuare a usare il **Provider predefinito** tipo di sottoscrizione.

<!-- 3557860 - IS ASDK --> 
- Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, prima di tutto eliminare l'intero gruppo di risorse o risorse utente e quindi eliminare le sottoscrizioni utente.

<!-- 1663805 - IS ASDK --> 
- Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare [PowerShell per verificare le autorizzazioni](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Calcolo

- Quando si crea una nuova macchina virtuale di Windows (VM), verrà visualizzato l'errore seguente:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   L'errore si verifica se si abilita la diagnostica di avvio in una macchina virtuale ma eliminare l'account di archiviazione di diagnostica di avvio. Per risolvere questo problema, ricreare l'account di archiviazione con lo stesso nome è stato usato in precedenza.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- L'esperienza di creazione macchina virtuale (VMSS) set di scalabilità offre basata su CentOS 7.2 come un'opzione per la distribuzione. Poiché tale immagine non è disponibile in Azure Stack, selezionare un altro sistema operativo per la distribuzione o usare un modello di Azure Resource Manager specificando un'altra immagine CentOS che è stata scaricata prima della distribuzione dal marketplace per il operatore.  

<!-- TBD - IS ASDK --> 
- Dopo aver applicato la 1902 di aggiornamento, si possono verificarsi i problemi seguenti durante la distribuzione di macchine virtuali con Managed Disks:

   - Se la sottoscrizione è stata creata prima dell'aggiornamento 1808, distribuzione di una VM con Managed Disks potrei avere esito negativo con un messaggio di errore interno. Per risolvere l'errore, seguire questi passaggi per ogni sottoscrizione:
      1. Nel portale Tenant, passare a **sottoscrizioni** e individuare la sottoscrizione. Selezionare **provider di risorse**, quindi selezionare **Microsoft. COMPUTE**, quindi fare clic su **registrare nuovamente**.
      2. Nella stessa sottoscrizione, passare a **controllo di accesso (IAM)** e verificare che **Azure Stack-Managed Disks** sia elencato.
   - Se è stato configurato un ambiente multi-tenant, la distribuzione di macchine virtuali in una sottoscrizione associata a una directory guest potrebbe non riuscire con un messaggio di errore interno. Per risolvere l'errore, seguire questa procedura nel [questo articolo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) riconfigurare tutte le directory di guest.

- Una macchina virtuale 18.04 di Ubuntu creata con l'autorizzazione di SSH abilitato non consentirà di usare le chiavi SSH per accedere. Come soluzione alternativa, usare l'accesso della macchina virtuale per l'estensione Linux per implementare le chiavi SSH dopo il provisioning o utilizzano l'autenticazione basata su password.

### <a name="networking"></a>Rete  

<!-- 3239127 - IS, ASDK -->
- Nel portale di Azure Stack, quando si modifica un indirizzo IP statico per una configurazione IP che è associato a una scheda di rete collegata a un'istanza di macchina virtuale, verrà visualizzato un messaggio di avviso che indica 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    È possibile ignorare questo messaggio. l'indirizzo IP verrà modificato anche se l'istanza di macchina virtuale non viene riavviato.

<!-- 3632798 - IS, ASDK -->
- Nel portale, se si aggiunge una regola di sicurezza in ingresso e selezionare **Tag del servizio** come origine, vengono visualizzate le opzioni diverse nel **Tag origine** elenco che non sono disponibili per Azure Stack. Le uniche opzioni valide in Azure Stack sono come segue:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  Le altre opzioni non sono supportate come i tag di origine in Azure Stack. Analogamente, se si aggiunge una regola di sicurezza in uscita e selezionare **Tag del servizio** come destinazione, lo stesso elenco di opzioni per **Tag origine** viene visualizzato. Le uniche opzioni valide sono uguali a quelle per **Tag origine**, come descritto nell'elenco precedente.

- Gruppi di sicurezza di rete (Nsg) non funzionano nello Stack di Azure nel portale di Azure stesso modo a livello globale. In Azure, è possibile impostare più porte per una regola NSG (usando il portale, PowerShell e i modelli di Resource Manager). In Azure Stack, tuttavia, è possibile impostare più porte su una regola NSG tramite il portale. Per risolvere questo problema, usare un modello di Resource Manager o PowerShell per impostare queste regole aggiuntive.

<!-- 3203799 - IS, ASDK -->
- Azure Stack non supporta il collegamento più di 4 interfacce di rete (NIC) a un'istanza di macchina virtuale oggi stesso, indipendentemente dalle dimensioni delle istanze.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Servizio app

<!-- 2352906 - IS ASDK --> 
- È necessario registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>Scaricare l'aggiornamento

È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1902 dal [qui](https://aka.ms/azurestackupdatedownload). 

In scenari connessi, le distribuzioni di Azure Stack verificare periodicamente un endpoint protetto e notificare automaticamente se è disponibile un aggiornamento per il cloud. Per altre informazioni, vedere [gestione degli aggiornamenti per Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di gestione degli aggiornamenti in Azure Stack, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md).  
- Per altre informazioni su come applicare gli aggiornamenti con Azure Stack, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
- Per esaminare i criteri di manutenzione per i sistemi integrati di Azure Stack e le operazioni da eseguire per mantenere il sistema in uno stato supportato, vedere [criteri di manutenzione Azure Stack](azure-stack-servicing-policy.md).  
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti in Azure Stack tramite l'endpoint con privilegi](azure-stack-monitor-update.md).  
