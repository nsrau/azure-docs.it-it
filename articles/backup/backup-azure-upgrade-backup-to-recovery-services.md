---
title: Aggiornare un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino (Anteprima) | Microsoft Docs
description: Comandi e informazioni di supporto per l&quot;aggiornamento di un insieme di credenziali di Backup di Azure a un insieme di credenziali di Servizi di ripristino.
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: required
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/10/2017
ms.author: markgal;arunak
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 00744d70a7644d80cfd99acbcf96966574f4512e
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Aggiornare un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino

Questo articolo spiega come aggiornare un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino. Il processo di aggiornamento non inciderà sui processi di backup in esecuzione e non verrà perso alcun dato di backup. I motivi principali per cui aggiornare un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino sono descritti di seguito:
- Tutte le funzionalità di un insieme di credenziali di Backup vengono mantenute in un insieme di credenziali di Servizi di ripristino.
- Gli insiemi di credenziali di Servizi di ripristino dispongono di maggiori funzionalità rispetto agli insiemi di credenziali di Backup, tra cui una migliore protezione, un monitoraggio integrato, ripristini più veloci e ripristini a livello di elemento, gestione degli elementi di backup da un portale migliorato e semplificato.
-  Le nuove funzionalità verranno rilasciate solo per gli insiemi di credenziali di Servizi di ripristino.

## <a name="impact-to-operations-during-upgrade"></a>Impatto sulle operazioni eseguite durante l'aggiornamento

Quando si aggiorna un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino, le operazioni del piano dati non vengono influenzate. Tutti i processi di backup continuano come al solito e i processi di ripristino attivi continuano senza interruzioni. Durante l'aggiornamento, la gestione delle operazioni subisce una breve inattività e non è possibile proteggere i nuovi elementi o creare processi di backup adhoc. I processi di ripristino per le macchine virtuali IaaS non vengono eseguiti durante l'aggiornamento. <MG>: qual è la differenza tra i processi di ripristino attivi che si interromperanno come di consueto e i processi di ripristino della macchina virtuale IaaS?
Il completamento dell'aggiornamento dell'insieme di credenziali richiede meno di un'ora. Al completamento, l'insieme di credenziali di Servizi di ripristino sostituisce l'insieme di credenziali di Backup.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Modifiche all'automazione e agli strumenti dopo l'aggiornamento

Durante la preparazione dell'infrastruttura per l'aggiornamento dell'insieme di credenziali, è necessario aggiornare l'automazione esistente o gli strumenti per verificare che continuino a funzionare dopo l'aggiornamento.
Consultare i riferimenti ai cmdlet di PowerShell per il [modello di distribuzione di Service Manager](backup-client-automation-classic.md) e il [modello di distribuzione di Resource Manager](backup-client-automation.md)


## <a name="before-you-upgrade"></a>Prima dell'aggiornamento

Controllare i seguenti problemi prima di aggiornare gli insiemi di credenziali di Backup agli insiemi di credenziali di Servizio di ripristino.

- **Versione minima dell'agente**: per aggiornare l'insieme di credenziali, assicurarsi che l'agente Servizi di ripristino di Microsoft Azure (MARS) abbia almeno la versione 2.0.9070.0. Se l'agente MARS ha una versione antecedente a 2.0.9070.0, aggiornare l'agente prima di avviare il processo di aggiornamento.
- **Modello di fatturazione basato su istanza**: gli insiemi di credenziali del servizio di ripristino supportano solo il modello di fatturazione basato su istanza. Se si dispone di un insieme di credenziali di Backup che usa il modello di fatturazione basato sull'archiviazione precedente, è possibile convertire il modello di fatturazione durante l'aggiornamento.
- **Nessuna operazione di configurazione del backup in corso**: durante l'aggiornamento, l'accesso al piano di gestione è limitato. Completare tutte le azioni del piano di gestione e quindi avviare l'aggiornamento.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Uso degli script di PowerShell per aggiornare l'insieme di credenziali

È possibile usare gli script di PowerShell per eseguire l'aggiornamento degli insiemi di credenziali di Backup per gli insiemi di credenziali dei servizi di ripristino. Verificare di avere i componenti di PowerShell necessari per attivare l'aggiornamento dell'insieme di credenziali. Gli script di PowerShell per gli insiemi di credenziali di Backup non funzionano per gli insiemi di credenziali dei servizi di ripristino. Preparare l'ambiente per aggiornare gli insiemi di credenziali:

1. Installare o aggiornare [Windows Management Framework, WMF, alla versione 5](https://www.microsoft.com/download/details.aspx?id=50395) o alla versione successiva.
2. Scaricare e [installare Azure PowerShell 3.7.0](https://github.com/Azure/azure-powershell/releases/download/v3.7.0-March2017/azure-powershell.3.7.0.msi).
3. Aggiornare i moduli di PowerShell:
  - Per Windows Server:
    - Install-Module -Name Azure -RequiredVersion 3.8.0
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0
  - Per Windows 10
    - Install-Module -Name Azure -RequiredVersion 3.8.0 -AllowClobber
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0 -AllowClobber
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0 -AllowClobber
4. Scaricare gli [script di PowerShell](http://download.microsoft.com/download/1/c/6/1c6ed72e-38f9-4675-9cf9-9b8a06da7cd3/recoveryservicesvaultupgrade.ps1) per aggiornare l'insieme di credenziali.

### <a name="run-the-powershell-script"></a>Eseguire lo script di PowerShell

Usare lo script seguente per aggiornare l'insieme di credenziali. Lo script di esempio seguente contiene le spiegazioni dei parametri.

RecoveryServicesVaultUpgrade.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`


SubscriptionID: il numero ID della sottoscrizione dell'insieme di credenziali che si sta aggiornando.
VaultName: il nome dell'insieme di credenziali di Backup che si sta aggiornando.
Location: percorso dell'insieme di credenziali che si sta aggiornando.
ResourceType: usare BackupVault.
TargetResourceGroupName: poiché si sta aggiornando l'insieme di credenziali a una distribuzione basata su Resource Manager, specificare un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno inserendo un nome nuovo. Se si inserisce il nome sbagliato per un gruppo di risorse, è possibile creare un nuovo gruppo di risorse. Per altre informazioni sui gruppi di risorse, leggere questa [panoramica di gruppi di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> I nomi dei gruppi di risorse hanno dei limiti. Assicurarsi di seguire le linee guida; in caso contrario, l'aggiornamento dell'insieme di credenziali potrebbe non riuscire.
>
>

Il frammento di codice seguente è un esempio di come dovrebbe apparire il comando di PowerShell:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

È inoltre possibile eseguire lo script senza parametri, mentre all'utente viene richiesto di inserire gli input per tutti i parametri obbligatori.

Lo script di PowerShell richiede di immettere le credenziali. Immettere le credenziali due volte: una volta per l'account Service Manager e una seconda volta per l'account di Resource Manager.

### <a name="pre-requisites-checking"></a>Verifica dei prerequisiti
Dopo averne immesso le credenziali, Azure verifica che l'ambiente soddisfi i prerequisiti seguenti:

- **Versione minima dell'agente**: l'aggiornamento degli insiemi di credenziali di Backup agli insiemi di credenziali di Servizi di ripristino richiede almeno la versione 2.0.9070 per l'agente MARS. Se si dispone di elementi registrati in un insieme di credenziali di Backup con un agente precedente alla versione 2.0.9070, il controllo dei prerequisiti ha esito negativo. Se il controllo dei prerequisiti non riesce, aggiornare l'agente e provare ad aggiornare nuovamente l'insieme di credenziali. È possibile scaricare la versione più recente dell'agente da [http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Processi di configurazione in corso**: se durante la verifica un utente esegue la configurazione di un processo per un insieme di credenziali di Backup impostato per l'aggiornamento o registra un elemento, il controllo dei prerequisiti ha esito negativo. Completare la configurazione o terminare la registrazione dell'elemento e quindi avviare il processo di aggiornamento dell'insieme di credenziali.
- **Modello di fatturazione basato sull'archiviazione**: gli insiemi di credenziali del servizio di ripristino supportano il modello di fatturazione basato su istanza. Se si esegue l'aggiornamento dell'insieme di credenziali in un insieme di credenziali di Backup che usa il modello di fatturazione basato sull'archiviazione, verrà chiesto di aggiornare il modello di fatturazione con l'insieme di credenziali. Altrimenti, è possibile aggiornare prima il modello di fatturazione e quindi eseguire l'aggiornamento dell'insieme di credenziali.
- Identificare un gruppo di risorse per l'insieme di credenziali di Servizi di ripristino. Per poter sfruttare le funzionalità di distribuzione di Resource Manager, è necessario inserire un insieme di credenziali di Servizi di ripristino in un gruppo di risorse. Se non si sa quale gruppo di risorse usare, specificare un nome in modo che il processo di aggiornamento crei il gruppo di risorse per l'utente. Il processo di aggiornamento associa anche l'insieme di credenziali con il nuovo gruppo di risorse.

Quando il processo di aggiornamento termina la verifica dei prerequisiti, viene richiesto di avviare l'aggiornamento dell'insieme di credenziali. Dopo la conferma, il completamento del processo di aggiornamento in genere richiede circa 15-20 minuti, a seconda delle dimensioni dell'insieme di credenziali. Se si dispone di un insieme di credenziali di grandi dimensioni, l'aggiornamento può richiedere fino a 90 minuti.

## <a name="managing-your-recovery-services-vaults"></a>Gestione degli insiemi di credenziali dei servizi di ripristino

Le schermate seguenti mostrano un nuovo insieme di credenziali di Servizi di ripristino, aggiornato dall'insieme di credenziali di Backup, nel portale di Azure. La prima schermata mostra il dashboard dell'insieme di credenziali che visualizza le entità principali per l'insieme di credenziali.

![esempio dell'insieme di credenziali di Servizi di ripristino aggiornato da un insieme di credenziali di Backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

La seconda schermata mostra i collegamenti della guida disponibili che consentono di iniziare a usare l'insieme di credenziali di Servizi di ripristino.

![collegamenti alla guida del pannello Avvio rapido](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="frequently-asked-questions"></a>Domande frequenti

**Il piano di aggiornamento influenza il backup in corso?**</br>
No. I backup in corso proseguono senza interruzioni durante e dopo l'aggiornamento.

**Cosa accade agli insiemi di credenziali dell'utente se non è previsto un aggiornamento a breve?**</br>
Poiché tutte le nuove funzionalità sono state pianificate per l'insieme di credenziali di Servizi di ripristino e l'uso del portale classico verrà deprecato, Microsoft imposterà come deprecati gli insiemi di credenziali di Backup e il modello di gestione del servizio. In futuro Microsoft attiverà un aggiornamento automatico di tutti gli insiemi di credenziali all'insieme di credenziali di Servizi di ripristino. Fino ad allora, i clienti possono decidere quando aggiornare i propri insiemi di credenziali.

**Qual è l'impatto di questo piano di aggiornamento per gli strumenti esistenti?**</br>  
L'aggiornamento degli strumenti al modello di distribuzione di Resource Manager su cui si basano gli insiemi di credenziali dei servizi di ripristino è una delle modifiche più importanti da tenere in considerazione per i piani di aggiornamento.

**Quanto dura il tempo di inattività?**</br>
Dipende dal numero di risorse sottoposte ad aggiornamento. Per distribuzioni di piccole dimensioni, ovvero poche decine di istanze protette, l'intero aggiornamento dovrebbe richiedere meno di 20 minuti. Per distribuzioni di grandi dimensioni, dovrebbe richiedere al massimo un'ora.

**È possibile eseguire il ripristino dello stato precedente dopo l'aggiornamento?**</br>
No. Il ripristino dello stato precedente non è supportato dopo il completamento dell'aggiornamento delle risorse.

**È possibile convalidare la sottoscrizione o le risorse per verificare che siano in grado di eseguire l'aggiornamento?**</br>
Sì. Il primo passaggio del processo di aggiornamento verifica che le risorse siano in grado di eseguire l'aggiornamento. In caso di esito negativo della convalida dei prerequisiti, si ricevono messaggi per tutti i motivi che impediscono il completamento dell'aggiornamento.

**Di quali autorizzazioni è necessario disporre per attivare l'aggiornamento dell'insieme di credenziali?**</br>
Per eseguire l'aggiornamento dell'insieme di credenziali, è necessario essere aggiunti come co-amministratori della sottoscrizione nel portale di Azure classico. Questo è un passaggio necessario anche se si è già stati aggiunti come proprietari nel portale di Azure. Provare ad aggiungere un co-amministratore per la sottoscrizione nel portale di Azure classico per scoprire se si è già co-amministratori per la sottoscrizione. Se non è possibile aggiungere un co-amministratore, contattare un amministratore o un co-amministratore del servizio per essere aggiunti alla sottoscrizione.

**È possibile aggiornare l'insieme di credenziali di Backup basato su CSP?**</br>
No. Attualmente non è possibile aggiornare gli insiemi di credenziali di Backup basati su CSP. Nelle versioni successive verrà aggiunto il supporto per questa funzionalità.

**È possibile visualizzare l'insieme di credenziali classico in seguito all'aggiornamento?**</br>
No. Non è possibile visualizzare o gestire l'insieme di credenziali classico in seguito all'aggiornamento. L'utente potrà usare il nuovo portale di Azure solo per tutte le operazioni di gestione nell'insieme di credenziali.

**L'aggiornamento non è riuscito ed è necessario aggiornare l'agente in un computer che non esiste più. Quali sono le operazioni da eseguire in questo caso?**</br>
Se è necessario usare l'archivio di backup del computer per la conservazione a lungo termine, l'utente non sarà in grado di aggiornare l'insieme di credenziali. Microsoft aggiungerà il supporto per consentire di eseguire l'aggiornamento dell'insieme di credenziali nelle versioni future.
Se non è più necessario archiviare i backup del computer, annullare la registrazione del computer dall'insieme di credenziali e ritentare l'aggiornamento.

**Perché non è possibile visualizzare le informazioni sui processi per le risorse locali in seguito all'aggiornamento?**</br>
Il monitoraggio dei backup locali, agente MARS, DPM e MABS, è una nuova funzionalità che si ottiene quando si aggiorna l'insieme di credenziali di Backup all'insieme di credenziali di Servizi di ripristino. La sincronizzazione delle informazioni sul monitoraggio con il servizio richiede fino a 12 ore.

**Come si segnala un problema?**</br>
In caso di errori durante l'aggiornamento, tenere presente gli OperationId elencati nel messaggio di errore. Il supporto tecnico Microsoft si impegnerà per risolvere il problema. È possibile contattare il supporto tecnico o inviare une messaggio di posta elettronica all'indirizzo rsvaultupgrade@service.microsoft.com indicando l'ID della sottoscrizione, il nome dell'insieme di credenziali e l'ID dell'operazione. Il problema verrà risolto appena possibile. Non ripetere l'operazione a meno che non venga esplicitamente richiesto da Microsoft.


## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per:</br>
[Eseguire il backup di una macchina virtuale IaaS](backup-azure-arm-vms-prepare.md)</br>
[Eseguire il backup del server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</br>
[Eseguire il backup di Windows Server](backup-configure-vault.md).

