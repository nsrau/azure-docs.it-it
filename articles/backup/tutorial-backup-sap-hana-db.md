---
title: 'Esercitazione: Eseguire il backup di database SAP HANA nelle VM di Azure'
description: Questa esercitazione illustra come eseguire il backup di database SAP HANA in esecuzione nelle VM di Azure in un insieme di credenziali di Servizi di ripristino di Backup di Azure.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: f64dd74ad0e038c5cad152e20ae2255de03114e3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79501442"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Esercitazione: Eseguire il backup di database SAP HANA in una VM di Azure

Questa esercitazione illustra come eseguire il backup di database SAP HANA in esecuzione nelle VM di Azure in un insieme di credenziali di Servizi di ripristino di Backup di Azure. In questo articolo viene spiegato come:

> [!div class="checklist"]
>
> * Creare e configurare un insieme di credenziali
> * Individuare i database
> * Configurare i backup

Per informazioni su tutti gli scenari attualmente supportati, vedere [qui](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare i backup, eseguire la procedura seguente:

* Consentire la connettività dalla VM a Internet, in modo che Azure sia raggiungibile, come descritto nella procedura [Configurare la connettività di rete](#set-up-network-connectivity) descritta di seguito.
* È necessario che sia presente una chiave in **hdbuserstore** che soddisfi i criteri seguenti:
  * Deve essere presente in **hdbuserstore** predefinito
  * Per MDC, la chiave deve puntare alla porta SQL di **NAMESERVER**. Nel caso di DSC, deve puntare alla porta SQL di **INDEXSERVER**
  * Deve avere le credenziali per aggiungere ed eliminare gli utenti
* Eseguire lo script di configurazione di backup SAP HANA (script di pre-registrazione) come utente radice nella macchina virtuale in cui è installato HANA. [Questo script](https://aka.ms/scriptforpermsonhana) prepara il sistema HANA per il backup. Per altre informazioni sullo script di pre-registrazione, vedere la sezione [Funzionalità dello script di pre-registrazione](#what-the-pre-registration-script-does).

## <a name="set-up-network-connectivity"></a>Configurare la connettività di rete

Per tutte le operazioni, la macchina virtuale SAP HANA richiede la connettività agli indirizzi IP pubblici di Azure. Le operazioni della macchina virtuale, ad esempio individuazione dei database, configurazione e pianificazione dei backup, ripristino dei punti di ripristino e così via, non riescono in assenza di connettività agli indirizzi IP pubblici di Azure.

Stabilire la connettività usando una delle opzioni seguenti:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Consentire gli intervalli IP del data center di Azure

Questa opzione autorizza gli [intervalli IP](https://www.microsoft.com/download/details.aspx?id=41653) nel file scaricato. Per accedere a un gruppo di sicurezza di rete, usare il cmdlet Set-AzureNetworkSecurityRule. Se l'elenco dei destinatari attendibili include solo indirizzi IP specifici dell'area, sarà anche necessario aggiornarlo con il tag del servizio Azure Active Directory (Azure AD) per abilitare l'autenticazione.

### <a name="allow-access-using-nsg-tags"></a>Consentire l'accesso con tag del gruppo di sicurezza di rete

Se si usa un gruppo di sicurezza di rete per limitare la connettività, è necessario usare il tag del servizio AzureBackup per consentire l'accesso in uscita a Backup di Azure. Inoltre, è necessario consentire la connettività per l'autenticazione e il trasferimento dei dati usando [regole](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) per Azure AD e Archiviazione di Azure. Questa operazione può essere eseguita dal portale di Azure o tramite PowerShell.

Per creare una regola tramite il portale:

  1. In **Tutti i servizi**, passare a **Gruppi di sicurezza di rete** e selezionare il gruppo di sicurezza di rete.
  2. In **Impostazioni** selezionare **Regole di sicurezza in uscita**.
  3. Selezionare **Aggiungi**. Immettere tutti i dettagli necessari per la creazione di una nuova regola, come descritto nelle [impostazioni delle regole di sicurezza](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Assicurarsi che l'opzione **Destinazione** sia impostata su **Tag del servizio** e che l'opzione **Tag del servizio di destinazione** sia impostata su **AzureBackup**.
  4. Fare clic su **Aggiungi** per salvare la regola di sicurezza in uscita appena creata.

Per creare una regola tramite PowerShell:

 1. Aggiungere le credenziali dell'account Azure e aggiornare i cloud nazionali<br/>
      `Add-AzureRmAccount`<br/>

 2. Selezionare la sottoscrizione del gruppo di sicurezza di rete<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Selezionare il gruppo di sicurezza di rete<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Aggiungere la regola per consentire l'accesso in uscita per il tag del servizio Backup di Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Aggiungere la regola per consentire l'accesso in uscita per il tag del servizio Archiviazione<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Aggiungere la regola per consentire l'accesso in uscita per il tag del servizio AzureActiveDirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Salvare il gruppo di sicurezza di rete<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Consentire l'accesso usando i tag di Firewall di Azure**. Se si usa Firewall di Azure, creare una regola dell'applicazione usando il [tag FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags) AzureBackup. In questo modo si consente l'accesso in uscita a Backup di Azure.

**Distribuire un server proxy HTTP per instradare il traffico**. Quando si esegue il backup di un database SAP HANA in una macchina virtuale di Azure, l'estensione di backup nella VM usa le API HTTPS per inviare i comandi di gestione a Backup di Azure e i dati ad Archiviazione di Azure. L'estensione di backup usa anche Azure AD per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. Le estensioni sono l'unico componente configurato per l'accesso a Internet pubblico.

Le opzioni di connettività presentano i vantaggi e gli svantaggi seguenti:

**Opzione** | **Vantaggi** | **Svantaggi**
--- | --- | ---
Consentire gli intervalli di indirizzi IP | Nessun costo aggiuntivo | Complessità di gestione a causa della variazione degli intervalli IP nel tempo <br/><br/> Accesso consentito a tutto l'ambiente di Azure, non solo ad Archiviazione di Azure
Usare i tag del servizio del gruppo di sicurezza di rete | Gestione semplificata perché le modifiche degli intervalli vengono unite automaticamente <br/><br/> Nessun costo aggiuntivo <br/><br/> | Utilizzabile solo con i gruppi di sicurezza di rete <br/><br/> Accesso consentito all'intero servizio
Usare i tag FQDN di Firewall di Azure | Gestione semplificata perché i FQDN necessari vengono gestiti automaticamente | Utilizzabile solo con Firewall di Azure
Usare un proxy HTTP | Controllo granulare nel proxy URL di archiviazione <br/><br/> Singolo punto di accesso Internet alle VM <br/><br/> Nessun effetto in caso di modifiche degli indirizzi IP di Azure | Costi aggiuntivi per l'esecuzione di una VM con il software proxy

## <a name="what-the-pre-registration-script-does"></a>Funzionalità dello script di pre-registrazione

L'esecuzione dello script di pre-registrazione esegue le funzioni seguenti:

* Installa o aggiorna tutti i pacchetti necessari richiesti dall'agente di Backup di Azure nella distribuzione.
* Esegue i controlli della connettività di rete in uscita con i server di Backup di Azure e i servizi dipendenti come Azure Active Directory e Archiviazione di Azure.
* Accede al sistema HANA usando la chiave utente elencata come parte dei [prerequisiti](#prerequisites). La chiave utente viene usata per creare un utente di backup (AZUREWLBACKUPHANAUSER) nel sistema HANA e può essere eliminata dopo che lo script di pre-registrazione è stato eseguito correttamente.
* Ad AZUREWLBACKUPHANAUSER vengono assegnati questi ruoli e autorizzazioni necessari:
  * AMMINISTRATORE DEL DATABASE (nel caso di MDC) e AMMINISTRATORE DEL BACKUP (nel caso di DSC): per creare nuovi database durante il ripristino.
  * CATALOG READ: per leggere il catalogo di backup.
  * SAP_INTERNAL_HANA_SUPPORT: per accedere ad alcune tabelle private.
* Lo script aggiunge una chiave a **hdbuserstore** per AZUREWLBACKUPHANAUSER per il plug-in di backup HANA per gestire tutte le operazioni (query su database, operazioni di ripristino, configurazione ed esecuzione del backup).

>[!NOTE]
> È possibile passare in modo esplicito la chiave utente elencata come parte dei [prerequisiti](#prerequisites) come parametro allo script di pre-registrazione: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Per informazioni sugli altri parametri accettati dallo script, usare il comando `bash msawb-plugin-config-com-sap-hana.sh --help`

Per confermare la creazione della chiave, eseguire il comando HDBSQL nella macchina virtuale HANA con le credenziali SIDADM:

```hdbsql
hdbuserstore list
```

L'output del comando dovrebbe visualizzare la chiave {SID}{DBNAME}, con l'utente visualizzato come AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Assicurarsi di avere un set univoco di file SSFS in `/usr/sap/{SID}/home/.hdb/`. Questo percorso dovrebbe contenere un'unica cartella.

## <a name="create-a-recovery-service-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali dei Servizi di ripristino è un'entità che archivia i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup associati alle macchine virtuali protette.

Per creare un insieme di credenziali dei servizi di ripristino:

1. Accedere alla propria sottoscrizione nel [portale di Azure](https://portal.azure.com/).

2. Nel menu sinistro selezionare **Tutti i servizi**

   ![Selezionare tutti i servizi](./media/tutorial-backup-sap-hana-db/all-services.png)

3. Nella finestra di dialogo **Tutti i servizi** inserire **Servizi di ripristino**. L'elenco delle risorse filtra sulla base degli input. Nell'elenco delle risorse selezionare**Insieme di credenziali di Servizi di ripristino**.

   ![Selezionare gli insiemi di credenziali di Servizi di ripristino](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Nel dashboard degli insiemi di credenziali di **Servizi di ripristino** selezionare **Aggiungi**.

   ![Aggiungere l'insieme di credenziali di Servizi di ripristino](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Si apre la finestra di dialogo **Insieme di credenziali dei Servizi di ripristino**. Specificare i valori per **Nome, Sottoscrizione, Gruppo di risorse** e **Località**

   ![Creare un insieme di credenziali di Servizi di ripristino](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Name**: il nome viene usato per identificare l'insieme di credenziali di Servizi di ripristino e deve essere univoco per la sottoscrizione di Azure. Specificare un nome che contiene almeno due caratteri, ma non più di 50. Il nome deve iniziare con una lettera e deve contenere solo lettere, numeri e trattini. Per questa esercitazione viene usato il nome **SAPHanaVault**.
   * **Sottoscrizione** scegliere la sottoscrizione da usare. Se si è un membro di una sola sottoscrizione, verrà visualizzato tale nome. Se non si è certi della sottoscrizione da usare, scegliere quella predefinita (consigliato). Sono disponibili più opzioni solo se l'account aziendale o dell'istituto di istruzione è associato a più sottoscrizioni di Azure. In questa esercitazione viene usata la sottoscrizione **SAP HANA solution lab subscription**.
   * **Gruppo di risorse**: Usare un gruppo di risorse esistente oppure crearne uno nuovo. In questa esercitazione viene usato **SAPHANADemo**.<br>
   Selezionare **Usa esistente** e nell'elenco a discesa scegliere una risorsa per visualizzare l'elenco di gruppi di risorse disponibili nella sottoscrizione. Per creare un nuovo gruppo di risorse, selezionare **Crea nuovo** e inserire il nome. Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   * **Località**: selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali deve trovarsi nella stessa area della macchina virtuale che esegue SAP HANA. In questa esercitazione viene usata l'area **Stati Uniti orientali 2**.

5. Selezionare **Rivedi e crea**.

   ![Selezionare Rivedi e crea](./media/tutorial-backup-sap-hana-db/review-create.png)

L'insieme di credenziali di Servizi di ripristino è stato creato.

## <a name="discover-the-databases"></a>Individuare i database

1. Nell'insieme di credenziali, in **Attività iniziali**, fare clic su **Backup**. In **Posizione di esecuzione del carico di lavoro** selezionare **SAP HANA in una macchina virtuale di Azure**.
2. Fare clic su **Avvia individuazione**. Viene avviata l'individuazione delle macchine virtuali Linux non protette nell'area dell'insieme di credenziali. Viene visualizzata la VM di Azure che si vuole proteggere.
3. In **Seleziona macchine virtuali** fare clic sul collegamento per scaricare lo script che fornisce le autorizzazioni per il servizio Backup di Azure per accedere alle macchine virtuali SAP HANA per l'individuazione del database.
4. Eseguire lo script nella macchina virtuale che ospita i database SAP HANA di cui eseguire il backup.
5. Dopo aver eseguito lo script nella macchina virtuale, selezionarla in **Seleziona macchine virtuali**. Quindi fare clic su **Individua database**.
6. Backup di Azure individua tutti i database SAP HANA presenti nella macchina virtuale. Durante l'individuazione, Backup di Azure registra la VM con l'insieme di credenziali e installa l'estensione. Nel database non vengono installati agenti.

   ![Individuare i database](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Configurare il backup

Dopo aver individuato i database di cui eseguire il backup, abilitare il backup.

1. Fare clic su **Configura backup**.

   ![Configurare il backup](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. In **Seleziona elementi per backup** selezionare uno o più database da proteggere, quindi fare clic su **OK**.

   ![Seleziona elementi per backup](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. In **Criteri di backup > Scegliere i criteri di backup** creare un nuovo criterio di backup per i database, seguendo le istruzioni riportate nella sezione successiva.

   ![Scegliere i criteri di backup](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Dopo aver creato il criterio, nel menu **Backup** fare clic su **Abilita backup**.

   ![Fare clic su Abilita backup](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Per tenere traccia dello stato di avanzamento della configurazione di backup, vedere l'area **Notifiche** del portale.

## <a name="creating-a-backup-policy"></a>Creazione di un criterio di backup

Un criterio di backup definisce quando vengono acquisiti i backup e per quanto tempo vengono conservati.

* Un criterio viene creato a livello di insieme di credenziali.
* Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.

Specificare le impostazioni del criterio come segue:

1. In **Nome criterio** immettere un nome per il nuovo criterio. In questo caso, immettere **SAPHANA**.

   ![Immettere un nome per il nuovo criterio](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. In **Criteri di backup completo** selezionare un valore per **Frequenza di backup**. È possibile scegliere **Giornaliera** o **Settimanale**. Per questa esercitazione, scegliere la frequenza di backup **Giornaliera**.

   ![Selezionare una frequenza di backup](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. In **Intervallo conservazione** configurare le impostazioni di conservazione per il backup completo.
   * Per impostazione predefinita, sono selezionate tutte le opzioni. Deselezionare gli eventuali limiti dell'intervallo di conservazione che non si vogliono usare e impostare quelli da usare.
   * Il periodo di conservazione minimo di qualsiasi tipo di backup (completo/differenziale/del log) è di sette giorni.
   * I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
   * Il backup di un giorno specifico viene contrassegnato e conservato in base all'intervallo e all'impostazione di conservazione settimanale.
   * L'intervallo di conservazione mensile e annuale si comporta allo stesso modo.
4. Nel menu **Criteri di backup completo** fare clic su **OK** per accettare le impostazioni.
5. Quindi selezionare **Backup differenziale** per aggiungere un criterio differenziale.
6. Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione. È stato abilitato un backup differenziale da eseguire ogni **Domenica** alle **2:00**, che viene conservato per **30 giorni**.

   ![Criteri di backup differenziale](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >I backup incrementali non sono attualmente supportati.
   >

7. Fare clic su **OK** per salvare il criterio e tornare nel menu principale **Criteri di backup**.
8. Selezionare **Backup del log** per aggiungere un criterio per i backup del log delle transazioni.
   * Per impostazione predefinita, l'opzione **Backup del log** è impostata su **Abilita**. Questa opzione non può essere disabilitata perché SAP HANA gestisce tutti i backup del log.
   * Per la pianificazione del backup è stata impostata l'opzione **2 ore** e per il periodo di conservazione **15 giorni**.

    ![Criteri di backup del log](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > I backup del log iniziano a fluire solo dopo il corretto completamento di un backup completo.
   >

9. Fare clic su **OK** per salvare il criterio e tornare nel menu principale **Criteri di backup**.
10. Dopo aver completato la definizione dei criteri di backup, fare clic su **OK**.

I backup per i database SAP HANA sono stati correttamente configurati.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [eseguire backup su richiesta dei database SAP HANA in esecuzione nelle VM di Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Informazioni su come [ripristinare i database SAP HANA in esecuzione nelle VM di Azure](sap-hana-db-restore.md)
* Informazioni su come [gestire i database SAP HANA di cui è stato eseguito il backup con Backup di Azure](sap-hana-db-manage.md)
* Informazioni su come [risolvere i problemi comuni che si verificano durante il backup di database SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)
