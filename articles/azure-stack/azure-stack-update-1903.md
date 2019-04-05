---
title: Aggiornamento di Azure Stack 1903 | Microsoft Docs
description: Informazioni sull'aggiornamento 1903 per i sistemi integrati di Azure Stack, tra cui nuove funzionalità, problemi noti e dove scaricare l'aggiornamento.
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
ms.date: 04/05/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/05/2019
ms.openlocfilehash: a62c4dced78ef75588ef0fcc90e56bd6969c15a9
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59048810"
---
# <a name="azure-stack-1903-update"></a>Azure Stack 1903 update

*Si applica a Sistemi integrati di Azure Stack*

Questo articolo descrive il contenuto del pacchetto di aggiornamento 1903. L'aggiornamento include nuove funzionalità per questa versione di Azure Stack, correzioni e miglioramenti. Anche in questo articolo descrive i problemi noti in questa versione e include un collegamento per scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi correlati direttamente al processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]  
> Questo pacchetto di aggiornamento è solo per i sistemi integrati di Azure Stack. Questo pacchetto di aggiornamento non sono applicabili per Azure Stack Development Kit.

## <a name="build-reference"></a>Riferimento alla compilazione

È il numero di build di aggiornamento di Azure Stack 1903 **1.1903.0.35**.

> [!IMPORTANT]
> Il payload 1903 non include una versione ASDK.

## <a name="hotfixes"></a>Hotfix

Azure Stack rilascia gli aggiornamenti rapidi a intervalli regolari. Assicurarsi di installare il [hotfix più recente di Azure Stack](#azure-stack-hotfixes) per 1902 prima di aggiornare Azure Stack per 1903.

Azure Stack hotfix sono applicabili solo ai sistemi integrati di Azure Stack. non tentare di installare gli aggiornamenti rapidi nel ASDK.

> [!TIP]  
> Sottoscrivere seguenti *RSS* oppure *Atom* i feed di stare al passo con gli aggiornamenti rapidi di Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Formato Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Aggiornamenti rapidi di Azure Stack

- **1902**: [KB 4481548 – aggiornamento rapido di Azure Stack 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="improvements"></a>Miglioramenti

- Il payload 1903 update contiene un aggiornamento ai componenti di Azure Stack che non includono il sistema operativo host Azure Stack. In questo modo alcuni aggiornamenti all'ambito. Di conseguenza, previsto tempo necessario completare l'aggiornamento 1903 è minore di (circa 16 ore, ma in casi esatti possono variare). Questa riduzione in fase di esecuzione è specifica per l'aggiornamento 1903 e gli aggiornamenti successivi possono contenere aggiornamenti al sistema operativo, implicando diversi Runtime. Gli aggiornamenti futuri forniranno indicazioni simili per il tempo previsto che l'aggiornamento diverrà effettivo, a seconda del payload incluso.

- Risolto un bug in rete che impediva le modifiche per il **timeout di inattività (minuti)** pari a un **indirizzo IP pubblico** dall'entrata in vigore. In precedenza, sono state ignorate le modifiche apportate a questo valore, in modo che indipendentemente dalle eventuali modifiche apportate, il valore predefinito verrà impostato su 4 minuti. Questa impostazione controlla il numero di minuti per mantenere una connessione TCP aperta senza affidarsi ai client di inviare messaggi keep-alive. Nota Questo bug interessato solo l'istanza di livello gli indirizzi IP pubblici, indirizzi IP pubblici non assegnato a un servizio di bilanciamento del carico.

- Miglioramenti all'affidabilità del motore di aggiornamento, tra cui auto-correzione dei problemi comuni in modo che si applicano gli aggiornamenti senza interruzioni.

- Miglioramenti al rilevamento e monitoraggio e aggiornamento delle condizioni di spazio su disco insufficiente.

### <a name="secret-management"></a>Gestione dei segreti

- Azure Stack supporta ora la rotazione del certificato radice utilizzata dai certificati per la rotazione segreta esterna. Per altre informazioni, [, vedere l'articolo](azure-stack-rotate-secrets.md).

- 1903 contiene miglioramenti delle prestazioni per la rotazione segreta che riducono il tempo impiegato per l'esecuzione interna rotazione segreta.

## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Installare il [hotfix più recente di Azure Stack](#azure-stack-hotfixes) per 1902 (se presente) prima di aggiornare a 1903.

- Assicurarsi di usare la versione più recente del [capacity planner di Azure Stack](https://aka.ms/azstackcapacityplanner) per eseguire il carico di lavoro di pianificazione e di ridimensionamento. La versione più recente include correzioni di bug e fornisce nuove funzionalità che vengono rilasciate con ogni aggiornamento di Azure Stack.

- Prima di iniziare l'installazione di questo aggiornamento, eseguire [Test-AzureStack](azure-stack-diagnostic-test.md) con il parametro seguente per convalidare lo stato di Azure Stack e risolvere eventuali problemi operativi trovati, inclusi tutti gli avvisi e gli errori. Anche gli avvisi attivi rivedere e risolvere gli eventuali che richiedono un'azione:

    ```PowerShell
   Test-AzureStack -Group UpdateReadiness  
    ```

- Quando Azure Stack è gestito da System Center Operations Manager, assicurarsi di aggiornare il [il Management Pack per Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) alla versione 1.0.3.11 prima di applicare 1903.

- Il formato del pacchetto per l'aggiornamento di Azure Stack è stato modificato da **.bin/.exe/.xml** al **.zip/.xml** partire dalla versione 1902. Verranno visualizzati dai clienti con unità di scala di Azure Stack connessa di **aggiornamento disponibile** messaggi nel portale. I clienti che non sono connessi a questo punto è sufficiente scaricare e importare il file con estensione zip con il file. XML corrispondente.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

- Quando si esegue [Test-AzureStack](azure-stack-diagnostic-test.md), viene visualizzato un messaggio di avviso da Baseboard Management Controller (BMC). È possibile ignorare questo avviso.

- <!-- 2468613 - IS --> Durante l'installazione di questo aggiornamento, si potrebbero visualizzare avvisi con il titolo **Error: modello per FaultType UserAccounts.New è manca.** È possibile ignorare questi avvisi. Gli avvisi chiudono automaticamente al termine dell'installazione dell'aggiornamento.

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

<!-- Daniel 3/28 -->
- Nel portale per gli utenti, quando si passa a un blob all'interno di un account di archiviazione e si tenta di aprire **criteri di accesso** dall'albero di navigazione, la finestra successiva non è possibile caricare.

<!-- Daniel 3/28 -->
- Nel portale per gli utenti, quando si prova a caricare un blob utilizzando il **OAuth(preview)** opzione, l'attività ha esito negativo con un messaggio di errore. Per risolvere questo problema, caricare il blob usando il **SAS** opzione.

- Quando si è connessi i portali di Azure Stack è possibile visualizzare le notifiche sul portale di Azure pubblico. È possibile ignorare queste notifiche, non sono attualmente applicabili ad Azure Stack (ad esempio, "1 nuovo aggiornamento - gli aggiornamenti seguenti sono ora disponibili: Azure portale aprile 2019 update").

<!-- ### Health and monitoring -->

### <a name="compute"></a>Calcolo

- Quando si crea una nuova macchina virtuale di Windows (VM), verrà visualizzato l'errore seguente:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   L'errore si verifica se si abilita la diagnostica di avvio in una macchina virtuale ma eliminare l'account di archiviazione di diagnostica di avvio. Per risolvere questo problema, ricreare l'account di archiviazione con lo stesso nome è stato usato in precedenza.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- L'esperienza di creazione di Set di scalabilità di macchine virtuali offre basata su CentOS 7.2 come un'opzione per la distribuzione. Poiché tale immagine non è disponibile in Azure Stack, selezionare un altro sistema operativo per la distribuzione o usare un modello di Azure Resource Manager specificando un'altra immagine CentOS che è stata scaricata prima della distribuzione dal marketplace per il operatore.  

<!-- TBD - IS ASDK --> 
- Dopo aver applicato la 1903 aggiornare, possono verificarsi i problemi seguenti durante la distribuzione di macchine virtuali con Managed Disks:

   - Se la sottoscrizione è stata creata prima dell'aggiornamento 1808, distribuzione di una VM con Managed Disks potrei avere esito negativo con un messaggio di errore interno. Per risolvere l'errore, seguire questi passaggi per ogni sottoscrizione:
      1. Nel portale Tenant, passare a **sottoscrizioni** e individuare la sottoscrizione. Selezionare **provider di risorse**, quindi selezionare **Microsoft. COMPUTE**, quindi fare clic su **registrare nuovamente**.
      2. Nella stessa sottoscrizione, passare a **controllo di accesso (IAM)** e verificare che **Azure Stack-Managed Disks** sia elencato.
   - Se è stato configurato un ambiente multi-tenant, la distribuzione di macchine virtuali in una sottoscrizione associata a una directory guest potrebbe non riuscire con un messaggio di errore interno. Per risolvere l'errore, seguire questa procedura nel [questo articolo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) riconfigurare tutte le directory di guest.

- Una macchina virtuale 18.04 di Ubuntu creata con l'autorizzazione di SSH abilitato non consentirà di usare le chiavi SSH per accedere. Come soluzione alternativa, usare l'accesso della macchina virtuale per l'estensione Linux per implementare le chiavi SSH dopo il provisioning o utilizzano l'autenticazione basata su password.

- Se non è un Host di ciclo di vita dell'Hardware (HLH): prima compilazione 1902, era necessario impostare i criteri di gruppo **Configurazione computer\Impostazioni di Windows\Impostazioni sicurezza\Criteri Locali\opzioni di protezione** a **Invia LM e NTLM: usare la sicurezza della sessione NTLMv2 se negoziato**. Da compilazione 1902, è necessario lasciare l'impostazione **non definito** oppure impostare **Invia solo risposta NTLMv2** (ovvero il valore predefinito). In caso contrario, non sarà in grado di stabilire una sessione remota di PowerShell e verrà visualizzato un **l'accesso è negato** errore:

   ```shell
   PS C:\Users\Administrator> $session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint  -Credential $cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the 
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

### <a name="networking"></a>Rete  

<!-- 3239127 - IS, ASDK -->
- Nel portale di Azure Stack, quando si modifica un indirizzo IP statico per una configurazione IP che è associato a una scheda di rete collegata a un'istanza di macchina virtuale, verrà visualizzato un messaggio di avviso che indica 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

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

È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1903 dal [qui](https://aka.ms/azurestackupdatedownload). 

In scenari connessi, le distribuzioni di Azure Stack verificare periodicamente un endpoint protetto e notificare automaticamente se è disponibile un aggiornamento per il cloud. Per altre informazioni, vedere [gestione degli aggiornamenti per Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di gestione degli aggiornamenti in Azure Stack, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md).  
- Per altre informazioni su come applicare gli aggiornamenti con Azure Stack, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
- Per esaminare i criteri di manutenzione per i sistemi integrati di Azure Stack e le operazioni da eseguire per mantenere il sistema in uno stato supportato, vedere [criteri di manutenzione Azure Stack](azure-stack-servicing-policy.md).  
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti in Azure Stack tramite l'endpoint con privilegi](azure-stack-monitor-update.md).  
