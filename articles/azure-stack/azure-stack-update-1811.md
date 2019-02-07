---
title: Aggiornamento di Azure Stack 1811 | Microsoft Docs
description: Informazioni sull'aggiornamento 1811 per i sistemi integrati di Azure Stack, tra cui nuove funzionalità, problemi noti e dove scaricare l'aggiornamento.
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
ms.date: 01/30/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 6c498f51bffeadce7a7c0dba8491942f1d4d0248
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766096"
---
# <a name="azure-stack-1811-update"></a>Aggiornamento di Azure Stack 1811

*Si applica a: Sistemi integrati di Azure Stack*

Questo articolo descrive il contenuto del pacchetto di aggiornamento 1811. Il pacchetto di aggiornamento include nuove funzionalità per questa versione di Azure Stack, correzioni e miglioramenti. Anche in questo articolo descrive i problemi noti in questa versione e include un collegamento in modo che è possibile scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi correlati direttamente al processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]  
> Questo pacchetto di aggiornamento è solo per i sistemi integrati di Azure Stack. Questo pacchetto di aggiornamento non sono applicabili per Azure Stack Development Kit.

## <a name="build-reference"></a>Riferimento alla compilazione

È il numero di build di aggiornamento di Azure Stack 1811 **1.1811.0.101**.

## <a name="hotfixes"></a>Hotfix

Azure Stack rilascia gli aggiornamenti rapidi a intervalli regolari. Assicurarsi di installare il [hotfix più recente di Azure Stack](#azure-stack-hotfixes) per 1809 prima di aggiornare Azure Stack per 1811.

> [!TIP]  
> Sottoscrivere seguenti *RSS* oppure *Atom* i feed di stare al passo con gli aggiornamenti rapidi di Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Formato Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Aggiornamenti rapidi di Azure Stack

- **1809**: [KB 4481548 – aggiornamento rapido di Azure Stack 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Nessun hotfix corrente disponibile.

## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Durante l'installazione dell'aggiornamento 1811, è necessario assicurarsi che siano chiuse tutte le istanze del portale dell'amministratore. Il portale per gli utenti può rimanere aperto, ma è necessario chiudere il portale di amministrazione.

- Preparare la distribuzione di Azure Stack per l'host dell'estensione Azure Stack. Preparare il sistema usando le linee guida seguenti: [Preparazione per l'host dell'estensione per Azure Stack](azure-stack-extension-host-prepare.md). 
 
- Installare il [hotfix più recente di Azure Stack](#azure-stack-hotfixes) per 1809 prima di aggiornare a 1811.

- Prima di iniziare l'installazione di questo aggiornamento, eseguire [Test-AzureStack](azure-stack-diagnostic-test.md) con i parametri seguenti per convalidare lo stato di Azure Stack e risolvere eventuali problemi operativi trovati, inclusi tutti gli avvisi e gli errori. Anche gli avvisi attivi rivedere e risolvere gli eventuali che richiedono un'azione.  

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Se non è soddisfatti, i requisiti di host estensione il `Test-AzureStack` output viene visualizzato il messaggio seguente: 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare`

- L'aggiornamento di Azure Stack 1811 richiede che sono stati importati correttamente i certificati host estensione obbligatorio nell'ambiente Azure Stack. Per procedere all'installazione dell'aggiornamento 1811, è necessario importare i certificati SSL necessari per l'host dell'estensione. Per importare i certificati, vedere [in questa sezione](azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Se si ignora ogni avviso e comunque scegliere di installare l'aggiornamento 1811, l'aggiornamento avrà esito negativo in circa 1 ora con il messaggio seguente:   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    Dopo che sono stati importati correttamente i certificati host estensione obbligatorio, è possibile riprendere l'aggiornamento 1811 dal portale di amministratore. Mentre si consiglia di operatori di Azure Stack per pianificare una finestra di manutenzione durante il processo di aggiornamento, un errore a causa di certificati host estensione mancante non dovrebbe influire i carichi di lavoro o i servizi esistenti.  

    Durante l'installazione di questo aggiornamento, il portale utenti di Azure Stack è disponibile mentre viene configurato l'host dell'estensione. La configurazione dell'host di estensione può richiedere fino a 5 ore. Durante questo periodo, è possibile controllare lo stato di un aggiornamento o riprendere un'installazione di un aggiornamento non riuscito con [PowerShell per amministratori di Azure Stack o l'endpoint con privilegi](azure-stack-monitor-update.md).

## <a name="new-features"></a>Nuove funzionalità

Questo aggiornamento include le seguenti nuove funzionalità e miglioramenti per Azure Stack:

- Con questa versione, il [host dell'estensione](azure-stack-extension-host-prepare.md) è abilitata. L'host dell'estensione semplifica l'integrazione della rete e migliora le condizioni di sicurezza di Azure Stack.

- Aggiunta del supporto per l'autenticazione del dispositivo con Active Directory Federated Services (ADFS), quando si usa Azure CLI in particolare. Per altre informazioni, vedere [i profili delle versioni API di utilizzo con il comando di Azure in Azure Stack](./user/azure-stack-version-profiles-azurecli2.md)

- Aggiunta del supporto per le entità servizio tramite un segreto client con Active Directory Federated Services (ADFS). Per altre informazioni, vedere [creare un'entità servizio per AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Questa versione aggiunge il supporto per le versioni di API del servizio di archiviazione di Azure seguenti: **2017-07-29**, **2017-11-09**. Viene inoltre aggiunto il supporto per le seguenti versioni di API di Provider di risorse di archiviazione Azure: **2016-05-01**, **2016-12-01**, **2017-06-01**, e **2017-10-01**. Per altre informazioni, vedere [archiviazione di Azure Stack: Differenze e considerazioni](./user/azure-stack-acs-differences.md).

- Aggiunta di nuovi endpoint privileged comandi per aggiornare e rimuovere l'entità servizio per ad FS. Per altre informazioni, vedere [creare un'entità servizio per AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Aggiunte nuove operazioni di nodo di unità di scala che consentono a un operatore di Azure Stack avviare, arrestare e arrestare un nodo di unità di scala. Per altre informazioni, vedere [unit nodo azioni di ridimensionamento in Azure Stack](azure-stack-node-actions.md).

- Aggiungere un nuovo Pannello proprietà di area che consente di visualizzare i dettagli di registrazione dell'ambiente. È possibile visualizzare queste informazioni facendo il **gestione delle aree** riquadro nel dashboard predefinito nel portale di amministrazione e quindi selezionando **proprietà**.

- Aggiunto un nuovo comando di endpoint con privilegi per aggiornare le credenziali BMC usando nome utente e password, usato per comunicare con i computer fisici. Per altre informazioni, vedere [aggiornare i baseboard management controller \(BMC) credenziale](azure-stack-rotate-secrets.md).

- Aggiunta la possibilità di accedere a roadmap per Azure tramite l'icona della Guida e supporto (punto interrogativo) in alto a destra dei portali amministratore e utente, simili al modo in cui che è disponibile nel portale di Azure.

- Aggiungere una gestione migliorata di Marketplace una migliore esperienza per gli utenti non connessi. Il processo di caricamento per pubblicare un elemento del Marketplace in un ambiente disconnesso è semplificato per un unico passaggio, invece di caricare l'immagine e il pacchetto di Marketplace separatamente. Il prodotto caricato anche saranno visibile nel Pannello di gestione del Marketplace. Per altre informazioni, vedere [questa sezione](azure-stack-download-azure-marketplace-item.md#import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher). 

- Questa versione consente di ridurre la finestra di manutenzione necessarie per la rotazione segreta aggiungendo la possibilità di ruotare solo certificati esterni durante [rotazione segreta di Azure Stack](azure-stack-rotate-secrets.md).

- [PowerShell per Azure Stack](azure-stack-powershell-install.md) è stata aggiornata alla versione 1.6.0. L'aggiornamento include il supporto per le nuove funzionalità correlati all'archiviazione in Azure Stack. Per altre informazioni, vedere le note sulla versione per il [modulo di amministrazione di Azure Stack 1.6.0 in PowerShell Gallery](https://www.powershellgallery.com/packages/AzureStack/1.6.0) per informazioni sull'aggiornamento o l'installazione di PowerShell per Azure Stack, vedere [installare PowerShell per Azure Stack](azure-stack-powershell-install.md).

- I dischi gestiti è ora abilitata per impostazione predefinita durante la creazione di macchine virtuali usando il portale di Azure Stack. Vedere le [problemi noti](#known-issues-post-installation) sezione per i passaggi aggiuntivi necessari per Managed Disks per evitare errori durante la creazione della macchina virtuale.

- Questa versione introduce avviso **Repair** azioni per l'operatore di Azure Stack. Alcuni avvisi in 1811 forniscono un **Repair** pulsante nell'avviso che è possibile selezionare per risolvere il problema. Per altre informazioni, vedere [monitorare l'integrità e gli avvisi in Azure Stack](azure-stack-monitor-health.md).

## <a name="fixed-issues"></a>Problemi risolti

<!-- TBD - IS ASDK --> 
- Risolto un problema in cui i dati di contatore sull'utilizzo di indirizzi IP pubblici è stato illustrato lo stesso **EventDateTime** valore per ogni record anziché il **TimeDate** timestamp indicante quando il record è stato creato. È ora possibile usare questi dati per eseguire l'accounting accurato dell'utilizzo di indirizzi IP pubblico.

<!-- 3099544 – IS, ASDK --> 
- Risolto un problema che si sono verificati durante la creazione di una nuova macchina virtuale (VM) tramite il portale di Azure Stack. Selezionando le sue dimensioni della macchina virtuale ha causata il **USD al mese** colonna per visualizzare un **disponibile** messaggio. Questa colonna non viene più visualizzata; visualizzare la macchina virtuale dei prezzi di colonna non è supportato in Azure Stack.

<!-- 2930718 - IS ASDK --> 
- Risolto un problema in cui il portale dell'amministratore, quando si accede ai dettagli di qualsiasi sottoscrizione utente, dopo aver chiuso il pannello e facendo clic su **recenti**, il nome della sottoscrizione utente non venivano visualizzati. Viene ora visualizzato il nome della sottoscrizione utente.

<!-- 3060156 - IS ASDK --> 
- Risolto un problema nei portali di amministratore e utente: facendo clic sulle impostazioni del portale e selezionando **Elimina tutte le impostazioni e i dashboard privati** non ha funzionato come previsto e che è stata visualizzata una notifica di errore. Questa opzione ora funziona correttamente.

<!-- 2930799 - IS ASDK --> 
- Risolto un problema nei portali di amministratore e utente: sotto **tutti i servizi**, l'asset **piani di protezione DDoS** era indicato erroneamente. Non è disponibile in Azure Stack. L'elenco è stato rimosso.
 
<!--2760466 – IS  ASDK --> 
- Risolto un problema che si sono verificati durante l'installazione di un nuovo ambiente di Azure Stack, in cui l'avviso che indica **attivazione obbligatoria** non ha visualizzato. Ora in modo corretto viene visualizzato.

<!--1236441 – IS  ASDK --> 
- Risolto un problema che impediva l'applicazione dei criteri RBAC a un gruppo di utenti quando si usa ad FS.

<!--3463840 - IS, ASDK --> 
- Risolto un problema con backup di infrastruttura non riusciti a causa di un server file inaccessibile dalla rete VIP pubblico. Questa correzione sposta nuovamente il servizio backup di infrastruttura alla rete dell'infrastruttura pubblico. Se è stata applicata la versione più recente [aggiornamento rapido di Azure Stack per 1809](#azure-stack-hotfixes) che risolve questo problema, l'aggiornamento 1811 non effettuerà ulteriori modifiche. 

<!-- 2967387 – IS, ASDK --> 
- Risolto un problema in cui l'account usato per accedere al portale di amministratore o utente di Azure Stack è visualizzato come **utente non identificato**. Veniva visualizzato il messaggio quando l'account non dispone di un **primo** o **ultima** nome specificato.   

<!--  2873083 - IS ASDK --> 
- Risolto un problema in cui tramite il portale per creare un set di scalabilità di macchine virtuali (VMSS) ha causato il **dimensioni istanze** elenco a discesa non viene caricato correttamente quando si usa Internet Explorer. Questo browser ora funziona correttamente.  

<!-- 3190553 - IS ASDK -->
- Risolto un problema che ha generato avvisi fastidiosi che indica che non era disponibile un'istanza del ruolo di infrastruttura o nodo di unità di scala era offline.

<!-- 2724961 - IS ASDK -->
- Fissa un problema in cui la pagina di panoramica della macchina virtuale non è possibile visualizzare correttamente il grafico delle metriche della macchina virtuale. 

## <a name="changes"></a>Modifiche

- Un nuovo modo per visualizzare e modificare le quote in un piano è stato introdotto in 1811. Per altre informazioni, vedere [consente di visualizzare una quota esistente](azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- Miglioramenti della sicurezza in questo aggiornamento comporterà un aumento nella dimensione del backup del ruolo del servizio directory. Per aggiornato ridimensionamento materiale sussidiario per il percorso di archiviazione esterno, vedere la [documentazione di backup infrastructure](azure-stack-backup-reference.md#storage-location-sizing). Questa modifica comporta tempi più lunghi per completare il backup a causa il trasferimento dei dati di dimensioni più grande. Questa modifica interessa i sistemi integrati. 

- Il cmdlet esistente di PEP per recuperare le chiavi di ripristino di BitLocker viene rinominato in 1811, da Get-AzsCsvsRecoveryKeys a Get-AzsRecoveryKeys. Per altre informazioni su come recuperare le chiavi di ripristino di BitLocker, vedere [istruzioni su come recuperare le chiavi](azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Esposizione e vulnerabilità comuni

Questo aggiornamento installa gli aggiornamenti di sicurezza seguenti:  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

Per altre informazioni su questi problemi di protezione, fare clic su collegamenti precedenti o vedere gli articoli della Microsoft Knowledge Base [4478877](https://support.microsoft.com/help/4478877).

## <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

- Quando si esegue la **Get-AzureStackLog** cmdlet di PowerShell dopo aver eseguito **Test AzureStack** nella stessa sessione di endpoint con privilegi (PEP), **Get-AzureStackLog** ha esito negativo. Per risolvere questo problema, chiudere la sessione PEP nella quale è stata eseguita **Test-AzureStack**, quindi aprire una nuova sessione per l'esecuzione **Get-AzureStackLog**.

- Durante l'installazione di 1811 l'aggiornamento, assicurarsi che tutte le istanze del portale di amministratore siano chiuse durante questo periodo. Il portale per gli utenti può rimanere aperto, ma è necessario chiudere il portale di amministrazione.

- Quando si esegue [Test AzureStack](azure-stack-diagnostic-test.md), se il valore di **AzsInfraRoleSummary** o la **AzsPortalApiSummary** test ha esito negativo, viene richiesto di eseguire  **Test-AzureStack** con il `-Repair` flag.  Se si esegue questo comando, avrà esito negativo con messaggio di errore seguente:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  Questo problema verrà risolto in una versione futura.

- Durante l'installazione dell'aggiornamento 1811, usare il portale di Azure Stack è disponibile mentre viene configurato l'host dell'estensione. La configurazione dell'host di estensione può richiedere fino a 5 ore. Durante questo periodo, è possibile controllare lo stato di un aggiornamento o riprendere un'installazione di un aggiornamento non riuscito con [PowerShell per amministratori di Azure Stack o l'endpoint con privilegi](azure-stack-monitor-update.md). 

- Durante l'installazione dell'aggiornamento 1811, il dashboard del portale utente potrebbe non essere disponibile e le personalizzazioni possono essere perse. È possibile ripristinare il dashboard per l'impostazione predefinita dopo il completamento dell'aggiornamento, aprire le impostazioni del portale e selezionare **ripristinare le impostazioni predefinite**.

- Quando si esegue [Test-AzureStack](azure-stack-diagnostic-test.md), viene visualizzato un messaggio di avviso da Baseboard Management Controller (BMC). È possibile ignorare questo avviso.

- <!-- 2468613 - IS --> Durante l'installazione di questo aggiornamento, si potrebbero visualizzare avvisi con il titolo `Error – Template for FaultType UserAccounts.New is missing.` è possibile ignorare questi avvisi. Gli avvisi chiudono automaticamente al termine dell'installazione dell'aggiornamento.

- <!-- 3139614 | IS --> Se è stato applicato un aggiornamento ad Azure Stack da OEM, il **aggiornamento disponibile** notifica potrebbe non essere visualizzati nel portale di amministrazione di Azure Stack. Per installare l'aggiornamento di Microsoft, scaricare e importarlo manualmente seguendo le istruzioni contenute in questo caso [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).

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

<!-- TBD - IS ASDK --> 
- Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, prima di tutto eliminare l'intero gruppo di risorse o risorse utente e quindi eliminare le sottoscrizioni utente.

<!-- TBD - IS ASDK --> 
- Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare [PowerShell per verificare le autorizzazioni](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e

<!-- 1264761 - IS ASDK -->  
- Si potrebbero visualizzare avvisi per i **controller integrità** componente con i dettagli seguenti:  

    - Avviso #1:
       - NOME:  Ruolo di infrastruttura non integro
       - GRAVITÀ: Avviso
       - COMPONENTE: Controller di integrità
       - DESCRIZIONE: Il controller di integrità dello Scanner di Heartbeat non è disponibile. Ciò può influire sulle metriche e i report sull'integrità.  

    - Avviso #2:
       - NOME:  Ruolo di infrastruttura non integro
       - GRAVITÀ: Avviso
       - COMPONENTE: Controller di integrità
       - DESCRIZIONE: Il controller di integrità errore Scanner è disponibile. Ciò può influire sulle metriche e i report sull'integrità.

    Entrambi gli avvisi possono essere tranquillamente ignorati. Verranno chiusi automaticamente nel corso del tempo.  

### <a name="compute"></a>Calcolo

- Quando si crea un nuovo Windows macchina virtuale (VM), il **impostazioni** pannello, è necessario selezionare una porta in ingresso pubblica per poter continuare. In 1811, questa impostazione è necessaria, ma non ha alcun effetto. Questo avviene perché la funzionalità dipende dal Firewall di Azure, che non è implementato in Azure Stack. È possibile selezionare **porte in ingresso pubbliche No**, o una delle altre opzioni per procedere con la creazione della VM. L'impostazione avrà alcun effetto.

- Quando si crea una nuova macchina virtuale di Windows (VM), verrà visualizzato l'errore seguente:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   L'errore si verifica se si abilita la diagnostica di avvio in una macchina virtuale ma eliminare l'account di archiviazione di diagnostica di avvio. Per risolvere questo problema, ricreare l'account di archiviazione con lo stesso nome è stato usato in precedenza.

- Quando si crea una [VM serie Dv2](./user/azure-stack-vm-considerations.md#virtual-machine-sizes), da D11-14v2 VM consentono di creare 4, 8, 16 e dischi 32 dati rispettivamente. Tuttavia, nel riquadro di creazione della macchina virtuale Mostra 8, 16, 32 e 64 dischi di dati.

- Record di utilizzo in Azure Stack può contenere minuscole imprevisto. Per esempio:

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   In questo esempio, il nome del gruppo di risorse deve essere **AndrewRG**. È possibile ignorare questa incoerenza.

<!-- 3235634 – IS, ASDK -->
- Per distribuire le macchine virtuali con dimensioni che contiene un **v2** suffisso; ad esempio **Standard_A2_v2**, specificare il suffisso come **Standard_A2_v2** (v lettere minuscole). Non utilizzare **Standard_A2_V2** (lettere maiuscole V). Ciò funziona in Azure globale ed è un'incoerenza nello Stack di Azure.

<!-- 2869209 – IS, ASDK --> 
- Quando si usa la [ **Add-AzsPlatformImage** cmdlet](/powershell/module/azs.compute.admin/add-azsplatformimage), è necessario usare il **- OsUri** parametro come URI in cui è stato caricato il disco dell'account di archiviazione. Se si usa il percorso locale del disco, il cmdlet non riesce con l'errore seguente: 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- Quando si usa il portale per creare macchine virtuali (VM) in una dimensione di VM premium (DS, Ds_v2, ADFS, FSv2), viene creata la macchina virtuale in un account di archiviazione standard. La creazione di un account di archiviazione standard non influisce sul livello funzionale, numero di IOPs, o fatturazione. È possibile ignorare l'avviso con la dicitura: 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- L'esperienza di creazione macchina virtuale (VMSS) set di scalabilità offre basata su CentOS 7.2 come un'opzione per la distribuzione. Poiché tale immagine non è disponibile in Azure Stack, selezionare un altro sistema operativo per la distribuzione o usare un modello di Azure Resource Manager specificando un'altra immagine CentOS che è stata scaricata prima della distribuzione dal marketplace per il operatore.  

<!-- 2724873 - IS --> 
- Quando si usano i cmdlet di PowerShell **Start-AzsScaleUnitNode** oppure **Stop-AzsScaleunitNode** per gestire le unità di scala, il primo tentativo di avviare o arrestare l'unità di scala potrebbe non riuscire. Se il cmdlet non riesce durante la prima esecuzione, eseguire il cmdlet una seconda volta. La seconda esecuzione debba completare correttamente l'operazione. 

<!-- TBD - IS ASDK --> 
- Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, si supponga che il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

<!-- 1662991 IS ASDK --> 
- Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica.  

<!-- 3507629 - IS, ASDK --> 
- Managed Disks vengono creati due nuovi [tipi di quota di calcolo](azure-stack-quota-types.md#compute-quota-types) per limitare la capacità massima di dischi gestiti che è possibile eseguire il provisioning. Per impostazione predefinita, 2048 GiB viene allocata per ogni tipo di quota di dischi gestiti. Tuttavia, è possibile riscontrare i problemi seguenti:

   - Per le quote create prima dell'aggiornamento 1808, la quota di Managed Disks visualizzerà i valori 0 nel portale di amministrazione, anche se viene allocata 2048 GiB. È possibile aumentare o diminuire il valore in base a esigenze effettive e appena impostato il valore della quota sostituisce il valore predefinito GiB di 2048.
   - Se si aggiorna il valore di quota a 0, è equivalente al valore predefinito di 2048 GiB. In alternativa, impostare il valore di quota a 1.

<!-- TBD - IS ASDK --> 
- Dopo aver applicato la 1811 aggiornamento, si possono verificarsi i problemi seguenti durante la distribuzione di macchine virtuali con Managed Disks:

   - Se la sottoscrizione è stata creata prima dell'aggiornamento 1808, distribuzione di una VM con Managed Disks potrei avere esito negativo con un messaggio di errore interno. Per risolvere l'errore, seguire questi passaggi per ogni sottoscrizione:
      1. Nel portale Tenant, passare a **sottoscrizioni** e individuare la sottoscrizione. Selezionare **provider di risorse**, quindi selezionare **Microsoft. COMPUTE**, quindi fare clic su **registrare nuovamente**.
      2. Nella stessa sottoscrizione, passare a **controllo di accesso (IAM)** e verificare che il **AzureStack-DiskRP-Client** ruolo è elencato.
   - Se è stato configurato un ambiente multi-tenant, la distribuzione di macchine virtuali in una sottoscrizione associata a una directory guest potrebbe non riuscire con un messaggio di errore interno. Per risolvere l'errore, seguire questa procedura nel [questo articolo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) riconfigurare tutte le directory di guest.

- Una macchina virtuale 18.04 di Ubuntu creata con l'autorizzazione di SSH abilitato non consentirà di usare le chiavi SSH per accedere. Come soluzione alternativa, usare l'accesso della macchina virtuale per l'estensione Linux per implementare le chiavi SSH dopo il provisioning o utilizzano l'autenticazione basata su password.

### <a name="networking"></a>Rete  

<!-- 1766332 - IS ASDK --> 
- Sotto **Networking**, se si fa clic su **creare Gateway VPN** per configurare una connessione VPN, **basata su criteri** viene elencato come un tipo di VPN. Non selezionare questa opzione. Solo le **basato su Route** opzione è supportata in Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack supporta un'unica *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni tenant. Dopo la creazione della prima rete locale connessione gateway, successivi tentativi di creare una risorsa gateway di rete locale con lo stesso indirizzo IP vengono rifiutate.

<!-- 16309153 - IS ASDK --> 
- In una rete virtuale che è stata creata con l'impostazione server DNS **automatica**, la modifica in un server DNS personalizzato ha esito negativo. Le impostazioni aggiornate non vengono inviate alle macchine virtuali in tale rete virtuale.

<!-- 2529607 - IS ASDK --> 
- Durante l'Azure Stack *rotazione segreta*, è previsto un periodo in cui gli indirizzi IP pubblici non sono raggiungibili da due a cinque minuti.

<!-- 2664148 - IS ASDK --> 
-   Negli scenari in cui il tenant è l'accesso a macchine virtuali tramite un tunnel VPN S2S, si può verificare uno scenario in cui i tentativi di connessione non riuscire se la subnet locale è stata aggiunta al gateway di rete locale dopo che il gateway è già stato creato. 

- Nel portale di Azure Stack, quando si modifica un indirizzo IP statico per una configurazione IP che è associato a una scheda di rete collegata a un'istanza di macchina virtuale, verrà visualizzato un messaggio di avviso che indica 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    È possibile ignorare questo messaggio. l'indirizzo IP verrà modificato anche se l'istanza di macchina virtuale non viene riavviato.

- Nel portale nel **le proprietà di rete** pannello è presente un collegamento per **regole di sicurezza effettive** per ogni scheda di rete. Se si seleziona questo collegamento, viene visualizzato un nuovo pannello che mostra il messaggio di errore `Not Found.` questo errore si verifica perché Azure Stack non supporta ancora **regole di sicurezza effettive**.

- Nel portale, se si aggiunge una regola di sicurezza in ingresso e selezionare **Tag del servizio** come origine, vengono visualizzate le opzioni diverse nel **Tag origine** elenco che non sono disponibili per Azure Stack. Le uniche opzioni valide in Azure Stack sono come segue:

    - **Internet**
    - **VirtualNetwork**
    - **AzureLoadBalancer**
  
    Le altre opzioni non sono supportate come i tag di origine in Azure Stack. Analogamente, se si aggiunge una regola di sicurezza in uscita e selezionare **Tag del servizio** come destinazione, lo stesso elenco di opzioni per **Tag origine** viene visualizzato. Le uniche opzioni valide sono uguali a quelle per **Tag origine**, come descritto nell'elenco precedente.

- Il **New-AzureRmIpSecPolicy** cmdlet di PowerShell non supporta l'impostazione **DHGroup24** per il `DHGroup` parametro.

- Gruppi di sicurezza di rete (Nsg) non funzionano nello Stack di Azure nel portale di Azure stesso modo a livello globale. In Azure, è possibile impostare più porte per una regola NSG (usando il portale, PowerShell e i modelli di Resource Manager). In Azure Stack, non è possibile impostare più porte su una regola NSG tramite il portale. Per risolvere questo problema, usare un modello di Resource Manager per impostare queste regole aggiuntive.

### <a name="infrastructure-backup"></a>Backup dell'infrastruttura

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- Dopo aver abilitato i backup automatici, il servizio Utilità di pianificazione entra in stato disabilitato in modo imprevisto. Il servizio controller backup rileva che i backup automatici sono disabilitati e generano un avviso nel portale di amministrazione. Questo avviso è previsto quando i backup automatici sono disabilitati. 
    - Causa: Questo problema è dovuto a un bug nel servizio che comporta una perdita di configurazione dell'utilità di pianificazione. Questo bug non modificare il percorso di archiviazione, nome utente, password o chiave di crittografia.   
    - Correzione: Per attenuare questo problema, aprire il pannello delle impostazioni di backup controller nel provider di risorse dell'infrastruttura di Backup e selezionare **abilitare i backup automatici**. Assicurarsi di impostare il periodo di conservazione e la frequenza desiderato.
    - Occorrenza: Basso 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Servizio app

<!-- 2352906 - IS ASDK --> 
- È necessario registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Scaricare l'aggiornamento

È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1811 dal [qui](https://aka.ms/azurestackupdatedownload). 

In scenari connessi, le distribuzioni di Azure Stack verificare periodicamente un endpoint protetto e notificare automaticamente se è disponibile un aggiornamento per il cloud. Per altre informazioni, vedere [gestione degli aggiornamenti per Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Passaggi successivi

- Per esaminare i criteri di manutenzione per i sistemi integrati di Azure Stack e le operazioni da eseguire per mantenere il sistema in uno stato supportato, vedere [criteri di manutenzione Azure Stack](azure-stack-servicing-policy.md).  
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti in Azure Stack tramite l'endpoint con privilegi](azure-stack-monitor-update.md).  
- Per una panoramica di gestione degli aggiornamenti in Azure Stack, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md).  
- Per altre informazioni su come applicare gli aggiornamenti con Azure Stack, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).  
