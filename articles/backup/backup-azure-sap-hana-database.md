---
title: Eseguire il backup di un database di SAP HANA in Azure con backup di Azure | Microsoft Docs
description: Questa esercitazione illustra come eseguire il backup di un database di SAP HANA in Azure con il servizio backup di Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: dacurwin
ms.openlocfilehash: 8d99ff6f2d8a21a501631a3a062be6b05130c05b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931812"
---
# <a name="back-up-an-sap-hana-database-to-azure"></a>Eseguire il backup di un database di SAP HANA in Azure

[Backup di Azure](backup-overview.md) supporta il backup dei database di SAP Hana in Azure.

> [!NOTE]
> Questa funzionalità è attualmente in anteprima pubblica. Attualmente non è pronta per la produzione e non ha un contratto di lavoro garantito.

## <a name="scenario-support"></a>Supporto degli scenari

**Supporto** | **Dettagli**
--- | ---
**Aree geografiche supportate** | Australia sud-orientale, Australia orientale <br> Brasile meridionale <br> Canada centrale, Canada orientale <br> Asia orientale sud, Asia orientale <br> Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro-occidentali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali<br> India centrale, India meridionale <br> Giappone orientale, Giappone occidentale<br> Corea del Sud centrale, Corea del Sud meridionale <br> Europa settentrionale, Europa occidentale <br> Regno Unito meridionale, Regno Unito occidentale
**Sistemi operativi VM supportati** | SLES 12 con SP2, SP3 o SP4.
**Versioni supportate di HANA** | DSC su HANA 1. x, MDC su HANA 2. x < = SPS04 Rev 43

### <a name="current-limitations"></a>Limitazioni correnti

- È possibile eseguire il backup solo di SAP HANA database in esecuzione in macchine virtuali di Azure.
- È possibile eseguire solo il backup solo SAP HANA istanza in esecuzione in una singola macchina virtuale di Azure. Attualmente non sono supportate più istanze HANA nella stessa VM di Azure.
- È possibile eseguire il backup dei database solo in modalità di scalabilità verticale. Scalabilità orizzontale, ad esempio, un'istanza HANA in più macchine virtuali di Azure non è attualmente supportata per il backup.
- Non è possibile eseguire il backup di SAP HANA istanza con suddivisione in livelli dinamica nel server esteso, ad esempio la suddivisione in livelli dinamica presente in un altro nodo. Si tratta essenzialmente di una scalabilità orizzontale, che non è supportata.
- Non è possibile eseguire il backup di SAP HANA istanza con la suddivisione in livelli dinamica abilitata nello stesso server. La suddivisione in livelli dinamica non è attualmente supportata.
- È possibile configurare solo SAP HANA backup nel portale di Azure. La funzionalità non può essere configurata con PowerShell, CLI.
- È possibile eseguire il backup dei log del database ogni 15 minuti. Il flusso dei backup del log inizia solo dopo il completamento di un backup completo per il database.
- È possibile eseguire backup completi e differenziali. Il backup incrementale non è attualmente supportato.
- Non è possibile modificare il criterio di backup dopo averlo applicato per SAP HANA backup. Se si vuole eseguire il backup con impostazioni diverse, creare un nuovo criterio o assegnare un criterio diverso.
  - Per creare un nuovo criterio, nell'insieme di credenziali fare clic su **criteri** > **criteri di backup** >  **+ Aggiungi** > **SAP Hana nella macchina virtuale di Azure**e specificare le impostazioni dei criteri.
  - Per assegnare un criterio diverso, fare clic sul nome del criterio corrente nelle proprietà della macchina virtuale che esegue il database. Nella pagina **criteri di backup** è quindi possibile selezionare un criterio diverso da usare per il backup.

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare i backup, assicurarsi di eseguire le operazioni seguenti:

1. Nella macchina virtuale che esegue il database di SAP HANA, installare e abilitare i pacchetti driver ODBC dal pacchetto/supporto di SLES ufficiale usando Zypper, come indicato di seguito:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

    > [!NOTE]
    > Se non si aggiornano i repository, verificare che la versione di unixODBC sia min 2.3.4. Per informazioni sulla versione di uniXODBC, eseguire ```odbcinst -j``` come radice

2. Consentire la connettività dalla macchina virtuale a Internet, in modo che possa raggiungere Azure, come descritto nella procedura [seguente](#set-up-network-connectivity).

3. Eseguire lo script di pre-registrazione nella macchina virtuale in cui HANA viene installato come utente root. Lo script viene fornito [nel portale](#discover-the-databases) nel flusso ed è necessario per configurare le [autorizzazioni corrette](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions).

### <a name="set-up-network-connectivity"></a>Configurare la connettività di rete

Per tutte le operazioni, la macchina virtuale SAP HANA richiede la connettività agli indirizzi IP pubblici di Azure. Le operazioni della macchina virtuale (individuazione del database, configurazione dei backup, pianificazione dei backup, ripristino dei punti di ripristino e così via) non possono funzionare senza connettività. Stabilire la connettività consentendo l'accesso agli intervalli IP del Data Center di Azure: 

- È possibile scaricare gli [intervalli di indirizzi IP](https://www.microsoft.com/download/details.aspx?id=41653) per i Data Center di Azure e consentire l'accesso a questi indirizzi IP.
- Se si usano i gruppi di sicurezza di rete (gruppi), è possibile usare il [tag del servizio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) AzureCloud per consentire tutti gli indirizzi IP pubblici di Azure. È possibile usare il [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) per modificare le regole di NSG.
- la porta 443 deve essere consentita perché il trasporto è tramite HTTPS.

## <a name="onboard-to-the-public-preview"></a>Eseguire l'onboarding nell'anteprima pubblica

Eseguire l'onboarding nell'anteprima pubblica come indicato di seguito:

- Nel portale, registrare l'ID sottoscrizione al provider di servizi di servizi di ripristino [seguendo questo articolo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- Per PowerShell, eseguire questo cmdlet. Deve essere completato come "registrato".

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Individuare i database

1. Nell'insieme di credenziali, in **Introduzione**, fare clic su **backup**. In **dove è in esecuzione il carico di lavoro?** selezionare **SAP Hana nella macchina virtuale di Azure**.
2. Fare clic su **Avvia individuazione**. Viene avviata l'individuazione delle macchine virtuali Linux non protette nell'area dell'insieme di credenziali.

   - Al termine dell'individuazione, le macchine virtuali non protette vengono visualizzate nel portale, elencate in base al nome e al gruppo di risorse.
   - Se una macchina virtuale non è elencata come previsto, controllare se è già stato eseguito il backup in un insieme di credenziali.
   - Più macchine virtuali possono avere lo stesso nome ma appartengono a gruppi di risorse diversi.

3. In **Seleziona macchine virtuali**fare clic sul collegamento per scaricare lo script che fornisce le autorizzazioni per il servizio backup di Azure per accedere alle macchine virtuali SAP HANA per l'individuazione del database
4. Eseguire lo script in ogni macchina virtuale che ospita SAP HANA database di cui si vuole eseguire il backup.
5. Dopo aver eseguito lo script nelle VM, selezionare le VM in **Seleziona macchine virtuali**. Quindi fare clic su **individua**database.
6. Backup di Azure individua tutti i database di SAP HANA nella macchina virtuale. Durante l'individuazione, backup di Azure registra la macchina virtuale con l'insieme di credenziali e installa un'estensione nella macchina virtuale. Nessun agente installato nel database.

    ![Individuazione di database SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Configurare il backup  

A questo punto, abilitare il backup.

1. Nel passaggio 2 fare clic su **Configura backup**.
2. In **Seleziona elementi di cui eseguire il backup**selezionare tutti i database che si desidera proteggere > **OK**.
3. In **criteri di backup** > **scegliere criteri**di backup, creare un nuovo criterio di backup per i database, in base alle istruzioni riportate di seguito.
4. Dopo aver creato il criterio, scegliere **Abilita backup**dal menu **backup** .
5. Tenere traccia dello stato di avanzamento della configurazione del backup nell'area **notifiche** del portale.

### <a name="create-a-backup-policy"></a>Creare un criterio di backup

Un criterio di backup definisce quando vengono eseguiti i backup e per quanto tempo vengono conservati.

- Un criterio viene creato a livello di insieme di credenziali.
- Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.

Specificare le impostazioni dei criteri come indicato di seguito:

1. In **Nome criterio** immettere un nome per il nuovo criterio.
2. Nel criterio **Backup completo** selezionare una **frequenza di backup** scegliendo **Giornaliero** o **Settimanale**.
   - **Giornaliero**: selezionare l'ora e il fuso orario in cui inizia il processo di backup.
   
       - È necessario eseguire un backup completo. Non è possibile disattivare questa opzione.
       - Fare clic su **Backup completo** per visualizzare il criterio.
       - Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.
       
   - **Settimanale**: selezionare il giorno della settimana, l'ora e il fuso orario in cui viene eseguito il processo di backup.
3. In periodo di **mantenimento**dati configurare le impostazioni di conservazione per il backup completo.
    - Per impostazione predefinita, tutte le opzioni sono selezionate. Cancellare i limiti del periodo di mantenimento dati che non si vuole usare e impostarli.
    - Il periodo di memorizzazione minimo per qualsiasi tipo di backup (completo/differenziale/log) è di sette giorni.
    - I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
    - Il backup di un giorno specifico viene contrassegnato e mantenuto in base al periodo di mantenimento settimanale e all'impostazione.
    - L'intervallo di conservazione mensile e annuale si comporta allo stesso modo.

4. Nel menu **criteri di backup completo** fare clic su **OK** per accettare le impostazioni.
5. Selezionare **backup differenziale** per aggiungere un criterio differenziale.
6. Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione.
    - Al massimo, è possibile attivare un backup differenziale al giorno.
    - I backup differenziali possono essere conservati al massimo per 180 giorni. Se è necessario conservarli più a lungo, usare i backup completi.

    > [!NOTE]
    > I backup incrementali non sono attualmente supportati. 

7. Fare clic su **OK** per salvare il criterio e tornare al menu principale del **criterio di backup** .
8. Selezionare **backup del log** per aggiungere un criterio di backup del log transazionale,
    - In **backup del log**selezionare **Abilita**.
    - Impostare la frequenza e i controlli di conservazione.

    > [!NOTE]
    > Il flusso dei backup del log inizia solo dopo il completamento di un backup completo.

9. Fare clic su **OK** per salvare il criterio e tornare al menu principale del **criterio di backup** .
10. Al termine della definizione dei criteri di backup, fare clic su **OK**.


## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

I backup vengono eseguiti in base alla pianificazione dei criteri. È possibile eseguire un backup su richiesta nel modo seguente:


1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. In **elementi di backup**selezionare la macchina virtuale che esegue il database di SAP Hana, quindi fare clic su **Esegui backup ora**.
3. In **backup ora**usare il controllo calendario per selezionare l'ultimo giorno in cui deve essere mantenuto il punto di ripristino. Fare quindi clic su **OK**.
4. Monitorare le notifiche del portale. È possibile monitorare lo stato di avanzamento del processo nel dashboard dell'insieme di credenziali > **processi di Backup** > in corso. A seconda delle dimensioni del database, la creazione del backup iniziale potrebbe richiedere qualche minuto.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Eseguire il backup di SAP HANA studio su un database con backup di Azure abilitato

Se si vuole eseguire un backup locale (usando HANA Studio) di un database di cui viene eseguito il backup con backup di Azure, eseguire le operazioni seguenti:

1. Attendere il completamento di tutti i backup completi o del log per il database. Verificare lo stato in SAP HANA Studio.
2. Disabilitare i backup del log e impostare il catalogo di backup sul file system per il database pertinente.
3. A tale scopo, fare doppio clic su **systemdb** > **Configuration** > **selezionare database** > **Filter (log)** .
4. Impostare **enable_auto_log_backup** su **No**.
5. Impostare **log_backup_using_backint** su **false**.
6. Eseguire un backup completo ad hoc del database.
7. Attendere il completamento del backup completo e del catalogo.
8. Ripristinare le impostazioni precedenti a quelle per Azure:
    - Impostare **enable_auto_log_backup** su **Sì**.
    - Impostare **log_backup_using_backint** su **true**.


## <a name="upgrading-protected-10-dbs-to-20"></a>Aggiornamento di database 1,0 protetti a 2,0

Se si sta proteggendo SAP HANA database 1,0 e si vuole eseguire l'aggiornamento a 2,0, eseguire i passaggi descritti di seguito.

- Arrestare la protezione con Mantieni i dati per il database DSC precedente.
- Eseguire nuovamente lo script di pre-registrazione con i dettagli corretti di (SID e MDC). 
- Ri-Register Extension (backup-> View Details-> selezionare la relativa VM di Azure-> Re-Register). 
- Fare clic su riindividuare i database per la stessa macchina virtuale. Verranno visualizzati i nuovi database nel passaggio 2 con i dettagli corretti (SYSTEMDB e tenant DB, non DSC). 
- Proteggi questi nuovi database.

## <a name="next-steps"></a>Passaggi successivi

Informazioni [su](backup-azure-sap-hana-database-troubleshoot.md) come risolvere gli errori comuni durante l'uso di SAP Hana backup nelle VM di Azure.
Informazioni [sul backup di](backup-azure-arm-vms-prepare.md) macchine virtuali di Azure.
