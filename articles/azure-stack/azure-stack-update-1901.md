---
title: Aggiornamento di Azure Stack 1901 | Microsoft Docs
description: Informazioni sull'aggiornamento 1901 per i sistemi integrati di Azure Stack, tra cui nuove funzionalità, problemi noti e dove scaricare l'aggiornamento.
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
ms.date: 02/28/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 0bbf76e16334ae4847ec6f7fbf3aa88fb508e84d
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731137"
---
# <a name="azure-stack-1901-update"></a>Aggiornamento di Azure Stack 1901

*Si applica a: Sistemi integrati di Azure Stack*

Questo articolo descrive il contenuto del pacchetto di aggiornamento 1901. L'aggiornamento include nuove funzionalità per questa versione di Azure Stack, correzioni e miglioramenti. Anche in questo articolo descrive i problemi noti in questa versione e include un collegamento per scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi correlati direttamente al processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]  
> Questo pacchetto di aggiornamento è solo per i sistemi integrati di Azure Stack. Questo pacchetto di aggiornamento non sono applicabili per Azure Stack Development Kit.

## <a name="build-reference"></a>Riferimento alla compilazione

È il numero di build di aggiornamento di Azure Stack 1901 **1.1901.0.95** oppure **1.1901.0.99** dopo il 26 febbraio 2019. Vedere la nota seguente:

> [!IMPORTANT]  
> Microsoft ha individuato un problema che può influire sulle si esegue l'aggiornamento da 1811 (1.1811.0.101) a 1901 e ha pubblicato un pacchetto 1901 aggiornato per risolvere il problema: 1.1901.0.99, aggiornato da 1.1901.0.95 di compilazione. I clienti che hanno già aggiornato a 1.1901.0.95 non sono necessario eseguire ulteriori operazioni.
>
> Connessi ai clienti che si trovano in 1811 visualizzeranno automaticamente il nuovo pacchetto 1901 (1.1901.0.99) disponibile nel portale di amministrazione e deve essere installato quando si è pronti. I clienti disconnessi possono scaricare e importare il nuovo pacchetto 1901 usando lo stesso processo [descritte di seguito](azure-stack-apply-updates.md).
>
> I clienti con entrambe le versioni di 1901 non saranno interessati quando si installa il pacchetto completo o un hotfix successivo.

## <a name="hotfixes"></a>Hotfix

Azure Stack rilascia gli aggiornamenti rapidi a intervalli regolari. Assicurarsi di installare il [hotfix più recente di Azure Stack](#azure-stack-hotfixes) per 1811 prima di aggiornare Azure Stack per 1901.

Azure Stack hotfix sono applicabili solo ai sistemi integrati di Azure Stack. non tentare di installare gli aggiornamenti rapidi nel ASDK.

> [!TIP]  
> Sottoscrivere seguenti *RSS* oppure *Atom* i feed di stare al passo con gli aggiornamenti rapidi di Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Formato Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Aggiornamenti rapidi di Azure Stack

- **1809**: [KB 4481548 – aggiornamento rapido di Azure Stack 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Nessun hotfix corrente disponibile.
- **1901**: Nessun hotfix corrente disponibile.

## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
- Installare il [hotfix più recente di Azure Stack](#azure-stack-hotfixes) per 1811 (se presente) prima di aggiornare a 1901.

- Prima di iniziare l'installazione di questo aggiornamento, eseguire [Test-AzureStack](azure-stack-diagnostic-test.md) con i parametri seguenti per convalidare lo stato di Azure Stack e risolvere eventuali problemi operativi trovati, inclusi tutti gli avvisi e gli errori. Anche gli avvisi attivi rivedere e risolvere gli eventuali che richiedono un'azione:

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Quando Azure Stack è gestito da System Center Operations Manager (SCOM), assicurarsi di aggiornare il Management Pack per Microsoft Azure Stack alla versione 1.0.3.11 prima di applicare 1901.

## <a name="new-features"></a>Nuove funzionalità

Questo aggiornamento include le seguenti nuove funzionalità e miglioramenti per Azure Stack:

- Immagini gestite su enable Azure Stack le macchine virtuali in futuro è possibile creare un oggetto immagine gestita in una macchina virtuale generalizzata (sia non gestite e gestite) che può creare solo gestita su disco. Per altre informazioni, vedere [Managed Disks di Azure Stack](user/azure-stack-managed-disk-considerations.md#managed-images).

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         Correzione di bug: `Import-AzureRmContext` per deserializzare il token salvato correttamente.  
   * **AzureRm.Resources**  
         Correzione di bug: `Get-AzureRmResource` al caso di query e minuscole per tipo di risorsa.  
   * **Azure.Storage**  
         Modulo di rollup AzureRm ora include la versione già pubblicata 4.5.0 che supportano il **api-version 2017-07-29**.  
   * **AzureRm.Storage**  
         Modulo di rollup AzureRm ora include la versione già pubblicata 5.0.4 che supportano il **2017-10-01 versione api-version**.  
   * **AzureRm.Compute**  
         Nei set di parametri semplici aggiunto `New-AzureRMVM` e `NewAzureRMVMSS`, `-ImageName` parametro supporta la specifica immagini dell'utente.  
   * **AzureRm.Insights**  
         Modulo di rollup AzureRm ora include la versione già pubblicata 5.1.5 che supportano il **2018-01 01 api-version** per le metriche, i tipi di risorsa delle definizioni delle metriche.

- **AzureStack 1.7.0** questa versione di una modifica sostanziale. Per informazioni dettagliate, fare riferimento a https://aka.ms/azspshmigration170
   * **Modulo Azs.Backup.Admin**  
         Modifica che causa un'interruzione: passaggio dal backup alla modalità di crittografia basata su certificato. Il supporto delle chiavi simmetriche è deprecato.  
   * **Azs.Fabric.Admin Module**  
         `Get-AzsInfrastructureVolume` è stato deprecato. Usare il nuovo cmdlet `Get-AzsVolume`.  
         `Get-AzsStorageSystem` è stato deprecato.  Usare il nuovo cmdlet `Get-AzsStorageSubSystem`.  
         `Get-AzsStoragePool` è stato deprecato. Il `StorageSubSystem` oggetto contiene la proprietà di capacità.  
   * **Modulo Azs.Compute.Admin**  
         Correzione di bug - `Add-AzsPlatformImage`, `Get-AzsPlatformImage`: La chiamata `ConvertTo-PlatformImageObject` solo nel percorso di esito positivo.  
         BugFix - `Add-AzsVmExtension`, `Get-AzsVmExtension`: La chiamata a ConvertTo-VmExtensionObject solo nel percorso di esito positivo.  
   * **Modulo Azs.Storage.Admin**  
         Correzione di bug: nuova Quota di archiviazione Usa valori predefiniti se non specificato.

Per esaminare le informazioni di riferimento per i moduli aggiornati, vedere [riferimento al modulo di Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0).

## <a name="fixed-issues"></a>Problemi risolti

- Risolto un problema in cui il portale è stato illustrato un'opzione per creare criteri in base al gateway VPN, che non sono supportati in Azure Stack. Questa opzione è stata rimossa dal portale.

<!-- 16523695 – IS, ASDK -->
- Risolto un problema in cui dopo l'aggiornamento delle impostazioni DNS per la rete virtuale **usare DNS di Azure Stack** al **DNS personalizzato**, le istanze non sono state aggiornate con la nuova impostazione.

- <!-- 3235634 – IS, ASDK --> Risolto un problema in cui distribuire le macchine virtuali con dimensioni che contiene un **v2** suffisso; ad esempio, **Standard_A2_v2**, che specifica il suffisso come obbligatorio **Standard_A2_v2** ( v lettere minuscole). Come con Azure globale, è ora possibile usare **Standard_A2_V2** (lettere maiuscole V).

<!-- 2869209 – IS, ASDK --> 
- Risolto un problema quando si usa la [cmdlet Add-AzsPlatformImage](/powershell/module/azs.compute.admin/add-azsplatformimage), in cui era necessario utilizzare il **- OsUri** parametro come URI in cui è stato caricato il disco dell'account di archiviazione. È ora possibile usare anche il percorso locale su disco.

<!--  2795678 – IS, ASDK --> 
- Risolto un problema che ha generato un avviso quando è stato usato il portale per creare macchine virtuali (VM) in una dimensione di VM premium (DS, Ds_v2, ADFS, FSv2). La macchina virtuale è stata creata in un account di archiviazione standard. Anche se ciò non ha modificato a livello funzionale, numero di IOPs o di fatturazione, l'avviso è stato risolto.

<!-- 1264761 - IS ASDK -->  
- Risolto un problema con il **controller integrità** componente che generava avvisi seguenti. Gli avvisi può essere tranquillamente ignorati:

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


<!-- 3507629 - IS, ASDK --> 
- Risolto un problema quando si imposta il valore delle quote di Managed Disks sotto [tipi di quota di calcolo](azure-stack-quota-types.md#compute-quota-types) su 0, è equivalente al valore predefinito di 2048 GiB. A questo punto il valore zero quota viene rispettato.

<!-- 2724873 - IS --> 
- Risolto un problema quando si usano i cmdlet di PowerShell **Start-AzsScaleUnitNode** oppure **Stop-AzsScaleUnitNode** per gestire le unità di scala, in cui il primo tentativo di avviare o arrestare l'unità di scala potrebbe non riuscire.

<!-- 2724961- IS ASDK --> 
- Risolto un problema in cui è registrato il **Microsoft.Insight** provider di risorse nelle impostazioni di sottoscrizione e creato una macchina virtuale Windows con Guest OS diagnostica è abilitata, ma non viene visualizzato il grafico della percentuale di CPU nella pagina di panoramica della macchina virtuale dati delle metriche. I dati ora vengono correttamente visualizzano.

- Risolto un problema in cui eseguire la **Get-AzureStackLog** cmdlet non è riuscito dopo aver eseguito **Test AzureStack** nella stessa sessione di endpoint con privilegi (PEP). È ora possibile usare la stessa sessione PEP nella quale è stata eseguita **Test-AzureStack**.

<!-- bug 3615401, IS -->
- Risolto un problema con i backup automatici in cui il servizio Utilità di pianificazione verrà inseriti in stato disabilitato in modo imprevisto. 

<!--2850083, IS ASDK -->
- Rimossa il **Reimposta Gateway** pulsante dal portale di Azure Stack, che ha generato un errore se è stato scelto il pulsante. Questo pulsante non funge da nessuna funzione nello Stack di Azure, Azure Stack è un gateway multi-tenant invece di istanze di macchine Virtuali dedicate per ogni tenant Gateway VPN, è stata rimossa per evitare confusione. 

<!-- 3209594, IS ASDK -->
- Rimossa il **regole di sicurezza effettive** collegamento dal **delle proprietà di rete** pannello come questa funzionalità non è supportato in Azure Stack. Creare un collegamento presentano ha l'impressione che questa funzionalità era supportata ma non funziona. Per ovviare a confusione, abbiamo rimosso il collegamento.

<!-- 3139614 | IS -->
- Risolto un problema in cui, dopo che è stato applicato un aggiornamento allo Stack di Azure da un OEM, il **aggiornamento disponibile** notifica non venivano visualizzati nel portale di amministrazione di Azure Stack.

## <a name="changes"></a>Modifiche

<!-- 3083238 IS -->
- Miglioramenti della sicurezza in questo aggiornamento comporterà un aumento nella dimensione del backup del ruolo del servizio directory. Per aggiornato ridimensionamento materiale sussidiario per il percorso di archiviazione esterno, vedere la [documentazione di backup infrastructure](azure-stack-backup-reference.md#storage-location-sizing). Questa modifica comporta tempi più lunghi per completare il backup a causa il trasferimento dei dati di dimensioni più grande. Questa modifica interessa i sistemi integrati. 

- A partire da gennaio 2019, è possibile distribuire i cluster Kubernetes in Active Directory Federated Services (ADFS) è registrati, connesse moduli per Azure Stack (è richiesto l'accesso a internet). Seguire le istruzioni riportate [qui](azure-stack-solution-template-kubernetes-cluster-add.md) per scaricare il nuovo elemento del Marketplace di Kubernetes. Seguire le istruzioni riportate [qui](user/azure-stack-solution-template-kubernetes-adfs.md) per distribuire un cluster Kubernetes. Notare i nuovi parametri per che indica se il sistema di destinazione è ADD o AD FS registrati. Se si tratta di AD FS, nuovi campi disponibili immettere i parametri di Key Vault in cui è archiviato il certificato di distribuzione.

   Si noti che anche con il supporto di AD FS, la distribuzione di cluster Kubernetes richiede l'accesso a internet.

- Dopo l'installazione di aggiornamenti oppure hotfix per Azure Stack, potrebbero essere introdotte nuove funzionalità che richiedono nuove autorizzazioni da concedere a una o più applicazioni di identità. Concedere queste autorizzazioni richiede accesso amministrativo alla home directory e in modo non può essere eseguita automaticamente. Ad esempio: 

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- È una considerazione nuove per la pianificazione in modo accurato la capacità di Azure Stack. Con l'aggiornamento 1901, è ora previsto un limite al numero totale di macchine virtuali che possono essere creati.  Questo limite viene considerato temporanea per evitare l'instabilità soluzione. L'origine del problema della stabilità in un numero elevato di macchine virtuali di cui viene effettuato, ma una sequenza temporale specifica per la correzione non è stata ancora determinata. Con l'aggiornamento 1901, è ora disponibile un limite di server di 60 VM con un limite totale delle soluzioni di 700.  Ad esempio, un limite di macchina virtuale di Azure Stack 8 server sarebbe 480 (8 * 60).  Per un server di 12 e 16 soluzione in Azure Stack, il limite sarebbe 700. Questo limite è stato creato tenendo tutte le risorse di calcolo considerazioni sulla capacità presenti, ad esempio la riserva di resilienza e la CPU virtuale rapporto fisico che un operatore vuole gestire sul modulo. Per altre informazioni, vedere la nuova versione del pianificatore di capacità.  
Nel caso in cui viene raggiunto il limite di scalabilità di macchine Virtuali, verrebbe restituiti come risultato i codici di errore seguente: VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded. 
 

- La versione dell'API di calcolo è aumentate a 2017-12-01.

- Backup dell'infrastruttura richiede ora un certificato con solo una chiave pubblica (. Area a esecuzione vincolata) per la crittografia dei dati di backup. Supporto di chiavi di crittografia simmetrica è deprecato a partire da 1901. Se è configurato il backup di infrastruttura prima di aggiornare a 1901 le chiavi di crittografia verranno rimangono invariati. È necessario almeno su 2 più Aggiorna con le versioni precedenti il supporto di compatibilità per aggiornare le impostazioni di backup. Per altre informazioni, vedere [Azure Stack dell'infrastruttura consigliate sui backup](azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Esposizione e vulnerabilità comuni

Questo aggiornamento installa gli aggiornamenti di sicurezza seguenti:  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


Per altre informazioni su questi problemi di protezione, fare clic su collegamenti precedenti o vedere gli articoli della Microsoft Knowledge Base [4480977](https://support.microsoft.com/en-us/help/4480977).

## <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

- Quando si esegue [Test AzureStack](azure-stack-diagnostic-test.md), se il valore di **AzsInfraRoleSummary** o la **AzsPortalApiSummary** test ha esito negativo, viene richiesto di eseguire  **Test-AzureStack** con il `-Repair` flag.  Se si esegue questo comando, avrà esito negativo con messaggio di errore seguente:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

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
- Dopo aver applicato la 1901 aggiornare, possono verificarsi i problemi seguenti durante la distribuzione di macchine virtuali con Managed Disks:

   - Se la sottoscrizione è stata creata prima dell'aggiornamento 1808, distribuzione di una VM con Managed Disks potrei avere esito negativo con un messaggio di errore interno. Per risolvere l'errore, seguire questi passaggi per ogni sottoscrizione:
      1. Nel portale Tenant, passare a **sottoscrizioni** e individuare la sottoscrizione. Selezionare **provider di risorse**, quindi selezionare **Microsoft. COMPUTE**, quindi fare clic su **registrare nuovamente**.
      2. Nella stessa sottoscrizione, passare a **controllo di accesso (IAM)** e verificare che **AzureStack-DiskRP-Client** sia elencato.
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
- Azure Stack non supporta il collegamento più di 4 interfacce di rete (NIC) alle istanze VM oggi stesso, indipendentemente dalle dimensioni delle istanze.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Servizio app

<!-- 2352906 - IS ASDK --> 
- È necessario registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>Scaricare l'aggiornamento

È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1901 dal [qui](https://aka.ms/azurestackupdatedownload). 

In scenari connessi, le distribuzioni di Azure Stack verificare periodicamente un endpoint protetto e notificare automaticamente se è disponibile un aggiornamento per il cloud. Per altre informazioni, vedere [gestione degli aggiornamenti per Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di gestione degli aggiornamenti in Azure Stack, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md).  
- Per altre informazioni su come applicare gli aggiornamenti con Azure Stack, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
- Per esaminare i criteri di manutenzione per i sistemi integrati di Azure Stack e le operazioni da eseguire per mantenere il sistema in uno stato supportato, vedere [criteri di manutenzione Azure Stack](azure-stack-servicing-policy.md).  
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti in Azure Stack tramite l'endpoint con privilegi](azure-stack-monitor-update.md).  
