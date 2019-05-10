---
title: Eseguire il backup di un database SAP HANA in Azure con Backup di Azure | Microsoft Docs
description: Questa esercitazione illustra come eseguire il backup di un database SAP HANA in Azure con il servizio Backup di Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: raynew
ms.openlocfilehash: 5ed41013535e4591d88bff5c017c1fcf4c4053cc
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237808"
---
# <a name="back-up-an-sap-hana-database"></a>Eseguire il backup di un database SAP HANA

[Backup di Azure](backup-overview.md) supporta il backup del database SAP HANA in Azure.

> [!NOTE]
> Questa funzionalità è attualmente in anteprima pubblica. Non è attualmente pronte per la produzione e non ha un contratto di servizio garantito. 


## <a name="scenario-support"></a>Supporto degli scenari

**Supporto** | **Dettagli**
--- | ---
**Aree geografiche supportate** | Australia sud-orientale, Australia orientale <br> Brasile meridionale <br> Canada Central, Canada East <br> Asia sud-orientale, Asia orientale <br> Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro occidentali, Stati Uniti occidentali, Stati Uniti occidentali 2, North Central US, Stati Uniti centrali, Stati Uniti centro-meridionali<br> India centrale, India meridionale <br> Giappone orientale, Giappone occidentale<br> Corea del Sud centrale, Corea del Sud meridionale <br> Europa settentrionale, Europa occidentale <br> Regno Unito meridionale, Regno Unito occidentale
**Sistemi operativi della macchina virtuale** | SLES 12 SP2 o SP3.
**Versioni supportate di HANA** | SSDC on HANA 1.x, MDC on HANA 2.x <= SPS03


### <a name="current-limitations"></a>Limitazioni correnti

- È possibile solo eseguire il backup dei database di SAP HANA in esecuzione in macchine virtuali di Azure.
- Nel portale di Azure, è possibile configurare solo backup di SAP HANA. La funzionalità non può essere configurata con PowerShell, CLI o l'API REST.
- È possibile solo eseguire il backup dei database in modalità di scalabilità verticale.
- È possibile eseguire il backup dei log di database ogni 15 minuti. I backup del log solo iniziano il flusso dopo un backup completo per il database è stata completata.
- È possibile eseguire backup completi e differenziali. Backup incrementale non è attualmente supportato.
- Dopo averlo applicato per i backup di SAP HANA non è possibile modificare i criteri di backup. Se si desidera eseguire il backup con impostazioni diverse, creare un nuovo criterio o assegnare un criterio diverso. 
    - Per creare un nuovo criterio, nell'insieme di credenziali fare clic su **politiche** > **criteri di Backup** > **+ Aggiungi** > **SAP HANA in Macchina virtuale di Azure**e specificare le impostazioni dei criteri.
    - Per assegnare un criterio diverso, nelle proprietà della macchina virtuale che esegue il database, fare clic sul nome del criterio corrente. Scegliere il **criterio di Backup** pagina è possibile selezionare un criterio diverso da utilizzare per il backup.




## <a name="prerequisites"></a>Prerequisiti

Assicurarsi che eseguire le operazioni seguenti prima di configurare i backup:

1. Nella macchina virtuale che esegue il database SAP HANA, installare Microsoft ufficiale [Runtime di .NET Core 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) pacchetto. Si noti che:
    - È necessario solo il **dotnet-runtime-2.1** pacchetto. Non occorre **aspnetcore-runtime-2.1**.
    - Se la macchina virtuale non dispone di internet di accesso, eseguire il mirroring o fornire una cache offline per dotnet-runtime-2.1 (e di tutti i dipendenti rpm) dal pacchetto di Microsoft del feed specificato nella pagina.
    - Durante l'installazione del pacchetto, potrebbe essere necessario specificare un'opzione. In questo caso, specificare **soluzione 2**.

        ![Opzione di installazione pacchetto](./media/backup-azure-sap-hana-database/hana-package.png)

2.  Nella macchina virtuale, installare e abilitare i pacchetti di driver ODBC dall'ufficiale SLES pacchetto/file multimediali usando zypper, come indicato di seguito:

    ``` 
    sudo zypper update
    sudo zypper install unixODBC
    ```
4.  Consentire la connettività da VM a internet, in modo da poter raggiungere Azure, come descritto nella procedura riportata di seguito.


### <a name="set-up-network-connectivity"></a>Configurare la connettività di rete

Per tutte le operazioni, la macchina virtuale di SAP HANA richiede la connettività agli indirizzi IP pubblici Azure. Operazioni delle macchine Virtuali (individuazione del database, configurare i backup, pianificare i backup, ripristinare i punti di ripristino e così via) non possono funzionare senza la connettività. Stabilire la connettività consentendo l'accesso agli intervalli IP di Data Center di Azure: 

- È possibile scaricare il [intervalli di indirizzi IP](https://www.microsoft.com/download/details.aspx?id=41653) per Data Center di Azure e quindi consentire l'accesso a questi indirizzi IP.
- Se si usa gruppi di sicurezza di rete (Nsg), è possibile usare il AzureCloud [tag del servizio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) per consentire l'IP pubblico di Azure tutti gli indirizzi. È possibile usare la [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) per modificare le regole di sicurezza di rete.

## <a name="onboard-to-the-public-preview"></a>Eseguire l'onboarding per l'anteprima pubblica

Eseguire l'onboarding per l'anteprima pubblica come indicato di seguito:

- Nel portale, registrare l'ID sottoscrizione per il provider di servizi di ripristino Services dalla [seguendo questo articolo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- Per PowerShell, eseguire questo cmdlet. L'operazione dovrebbe completarsi come "Registrato".

    ```
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Individuazione di database


1. Nell'insieme di credenziali, in **Guida introduttiva**, fare clic su **Backup**. Nelle **in cui viene eseguito il carico di lavoro?**, selezionare **SAP HANA in macchine Virtuali di Azure**.
2. Fare clic su **avvia individuazione**. Verrà avviata l'individuazione delle VM di Linux non protetto nell'area dell'insieme di credenziali.

   - Dopo l'individuazione, le macchine virtuali non protette vengono visualizzate nel portale, elencati in base al nome e gruppo di risorse.
   - Se una macchina virtuale non è elencata come previsto, controllare se si è già eseguito il backup in un insieme di credenziali.
   - Più macchine virtuali possono avere lo stesso nome ma appartengono a gruppi di risorse diversi.

3. Nelle **Seleziona macchine virtuali**, fare clic sul collegamento per scaricare lo script che fornisce le autorizzazioni per il servizio Backup di Azure accedere alle macchine virtuali SAP HANA per l'individuazione di database
4. Eseguire lo script in ogni macchina virtuale che ospita il database SAP HANA che si desidera eseguire il backup.
5. Dopo aver eseguito lo script nelle macchine virtuali, in **Seleziona macchine virtuali**, selezionare le macchine virtuali. Quindi fare clic su **DBs individuare**.
6. Backup di Azure consente di individuare tutti i database di SAP HANA nella macchina virtuale. Durante l'individuazione, Backup di Azure regista la VM con l'insieme di credenziali e consente di installare un'estensione nella macchina virtuale. Nessun agente installato nel database.

    ![Individuazione database di SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)
    
## <a name="configure-backup"></a>Configurare il backup  

A questo punto abilitare il backup.

1. Nel passaggio 2, fare clic su **configurare il Backup**.
2. Nelle **selezionare gli elementi di backup**, selezionare tutti i database che si desidera proteggere > **OK**.
3. Nelle **criterio di Backup** > **scegliere Criteri di backup**, creare un nuovo criterio di backup per i database, in base alle istruzioni riportate di seguito.
4. Dopo aver creato il criterio, nella **Backup** menu, fare clic su **Abilita backup**.
5. Tenere traccia dello stato di configurazione del backup nel **notifiche** area del portale.

### <a name="create-a-backup-policy"></a>Creare un criterio di backup
Un criterio di backup definisce quando i backup vengono eseguiti e per quanto tempo si sta conservati.

- Un criterio viene creato a livello di insieme di credenziali.
- Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.

Specificare le impostazioni dei criteri nel modo seguente:

1. In **Nome criterio** immettere un nome per il nuovo criterio.
2. Nel criterio **Backup completo** selezionare una **frequenza di backup** scegliendo **Giornaliero** o **Settimanale**.
   - **Giornaliera**: Selezionare l'ora e fuso orario in cui inizia il processo di backup.
   
       - È necessario eseguire un backup completo. È possibile disabilitare questa opzione.
       - Fare clic su **Backup completo** per visualizzare il criterio.
       - Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.
       
   - **Settimanale**: Selezionare il giorno della settimana, ora e fuso orario in cui viene eseguito il processo di backup.
3. Nelle **mantenimento**, configurare le impostazioni di conservazione per il backup completo.
    - Per impostazione predefinita tutte le opzioni sono selezionate. Deselezionare eventuali limiti di intervallo di conservazione che non si vuole usare e quelli che è impostato.
    - Il periodo di memorizzazione minimo per qualsiasi tipo di backup (completa/differenziale/log) è sette giorni.
    - I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
    - Il backup per un giorno specifico è contrassegnato e conservato in base l'impostazione e l'intervallo di conservazione settimanale.
    - L'intervallo di conservazione mensile e annuale si comporta allo stesso modo.

4. Nel **dei criteri di Backup completo** menu, fare clic su **OK** per accettare le impostazioni.
5. Selezionare **Backup differenziale** per aggiungere un criterio di backup differenziale.
6. Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione.
    - Al massimo, è possibile attivare un backup differenziale al giorno.
    - I backup differenziali possono essere conservati al massimo per 180 giorni. Se è necessario conservarli più a lungo, usare i backup completi.

    > [!NOTE]
    > I backup incrementali non sono attualmente supportati. 

7. Fare clic su **OK** per salvare il criterio e tornare al principale **criteri di Backup** menu.
8. Selezionare **Backup di Log** per aggiungere un criterio di backup del log delle transazioni,
    - Nelle **Backup del Log**, selezionare **abilitare**.
    - Impostare i controlli di frequenza e conservazione.

    > [!NOTE]
    > I backup del log iniziano solo dopo aver completato un backup completo del flusso.

9. Fare clic su **OK** per salvare il criterio e tornare al principale **criteri di Backup** menu.
10. Dopo aver definito i criteri di backup, fare clic su **OK**.


## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

I backup vengono eseguiti in conformità con la pianificazione dei criteri. È possibile eseguire un backup su richiesta come indicato di seguito:


1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. Nelle **elementi di Backup**, selezionare la macchina virtuale che esegue il database SAP HANA e quindi fare clic su **Esegui Backup ora**.
3. Nelle **Esegui Backup ora**, usare il controllo calendario per selezionare l'ultimo giorno che deve essere mantenuto il punto di ripristino. Fare quindi clic su **OK**.
4. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **processi di Backup** > In corso. A seconda delle dimensioni del database, creazione del backup iniziale potrebbe richiedere qualche minuto.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Eseguire il backup di SAP HANA Studio in un database con Backup di Azure abilitata

Se si desidera eseguire un backup locale (tramite HANA Studio) di un database che viene eseguito il con Backup di Azure, eseguire le operazioni seguenti:

1. Attesa per qualsiasi full o i backup del log per il database di completamento. Controllare lo stato in SAP HANA Studio.
2. Disabilitare i backup del log e impostare il catalogo di backup nel file System per il database pertinente.
3. A tale scopo, fare doppio clic su **systemdb** > **Configuration** > **seleziona Database** > **filtro (Log)** .
4. Impostare **enable_auto_log_backup** al **No**.
5. Impostare **log_backup_using_backint** al **False**.
6. Eseguire il backup del database di un completo ad hoc.
7. Attendere che il backup completo e il backup del catalogo per terminare.
8. Ripristinare le impostazioni precedenti a quelle per Azure:
    - Impostare **enable_auto_log_backup** al **Yes**.
    - Impostare **log_backup_using_backint** al **True**.



## <a name="next-steps"></a>Passaggi successivi

[Informazioni su](backup-azure-arm-vms-prepare.md) backup di macchine virtuali di Azure.


