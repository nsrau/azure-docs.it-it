---
title: Gestire il server di configurazione per il ripristino di emergenza di VMware e di server fisici con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come gestire un server di configurazione esistente per il ripristino di emergenza di macchine virtuali VMware e di server fisici in Azure con Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: ramamill
ms.openlocfilehash: d8b0d78d33c0780b05ac6f4bd8a21c6423d801f4
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445057"
---
# <a name="manage-the-configuration-server-for-vmware-vm-disaster-recovery"></a>Gestire il server di configurazione per il ripristino di emergenza di macchine virtuali VMware

Un server di configurazione locale viene impostato quando si usa [Azure Site Recovery](site-recovery-overview.md) per il ripristino di emergenza di server fisici e macchine virtuali VMware in Azure. Il server di configurazione coordina le comunicazioni tra VMware locale e Azure e gestisce la replica dei dati. Questo articolo riepiloga le attività comuni per la gestione del server di configurazione dopo che è stato distribuito.

## <a name="access-configuration-server"></a>Accedere al server di configurazione

È possibile accedere al server di configurazione come indicato di seguito:

* Accedere alla macchina virtuale in cui è distribuito e avviare **Gestione configurazione di Azure Site Recovery** dal collegamento sul desktop.
* In alternativa, è possibile accedere al server di configurazione in modalità remota da https://*NomeServerConfigurazione*/:44315/ Accedere con le credenziali di amministratore.

## <a name="modify-vmware-server-settings"></a>Modificare le impostazioni del server VMware

1. Per associare un server VMware diverso al server di configurazione dopo l'[accesso](#access-configuration-server), selezionare **Aggiungi server vCenter/vSphere ESXi**.
2. Immettere i dettagli e quindi selezionare **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Modificare le credenziali per l'individuazione automatica

1. Per aggiornare le credenziali usate per connettersi al server VMware per l'individuazione automatica delle macchine virtuali VMware, dopo l'[accesso](#access-configuration-server), scegliere l'account e fare clic su **Modifica**.
2. Immettere le nuove credenziali e quindi selezionare **OK**.

    ![Modificare VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

È possibile modificare le credenziali anche tramite CSPSConfigtool.exe.

1. Accedere al server di configurazione e avviare CSPSConfigtool.exe.
2. Scegliere l'account che si vuole modificare e fare clic su **Modifica**.
3. Immettere le credenziali modificate e fare clic su **OK**.

## <a name="modify-credentials-for-mobility-service-installation"></a>Modificare le credenziali per l'installazione del servizio Mobility

Modificare le credenziali usate per installare automaticamente il servizio Mobility nelle macchine virtuali VMware abilitate per la replica.

1. Dopo l'[accesso](#access-configuration-server), selezionare **Gestisci credenziali della macchina virtuale**.
2. Scegliere l'account che si vuole modificare e fare clic su **Modifica**.
3. Immettere le nuove credenziali e quindi selezionare **OK**.

    ![Modificare le credenziali del servizio Mobility](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

È possibile modificare le credenziali anche tramite CSPSConfigtool.exe.

1. Accedere al server di configurazione e avviare CSPSConfigtool.exe.
2. Scegliere l'account che si vuole modificare e fare clic su **Modifica**.
3. Immettere le nuove credenziali e fare clic su **OK**.

## <a name="add-credentials-for-mobility-service-installation"></a>Aggiungere le credenziali per l'installazione del servizio Mobility

Se non si sono aggiunte le credenziali durante la distribuzione OVF del server di configurazione:

1. Dopo l'[accesso](#access-configuration-server), selezionare **Gestisci credenziali della macchina virtuale**.
2. Fare clic su **Aggiungi credenziali della macchina virtuale**.
    ![add-mobility-credentials](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Immettere le nuove credenziali e fare clic su **Aggiungi**.

È possibile aggiungere le credenziali anche tramite CSPSConfigtool.exe.

1. Accedere al server di configurazione e avviare CSPSConfigtool.exe.
2. Fare clic su **Aggiungi**, immettere le nuove credenziali e fare clic su **OK**.

## <a name="modify-proxy-settings"></a>Modificare le impostazioni proxy

Modificare le impostazioni proxy usate dal computer server di configurazione per l'accesso Internet ad Azure. Se si ha un computer server di elaborazione, oltre al server di elaborazione predefinito in esecuzione nel computer server di configurazione, modificare le impostazioni in entrambi i computer.

1. Dopo l'[accesso](#access-configuration-server) al server di configurazione, selezionare **Gestisci connettività**.
2. Aggiornare i valori del proxy. quindi selezionare **Salva** per aggiornare le impostazioni.

## <a name="add-a-network-adapter"></a>Aggiungere una scheda di rete

Il modello OVF (Open Virtualization Format) distribuisce la macchina virtuale del server di configurazione con una sola scheda di rete.

- È possibile [aggiungere un'altra scheda alla macchina virtuale](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), ma è necessario aggiungerla prima di registrare il server di configurazione nell'insieme di credenziali.
- Per aggiungere una scheda dopo aver registrato il server di configurazione nell'insieme di credenziali, aggiungere la scheda nelle proprietà della macchina virtuale. È quindi necessario [registrare di nuovo](#reregister-a-configuration-server-in-the-same-vault) il server nell'insieme di credenziali.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Registrare di nuovo un server di configurazione nello stesso insieme di credenziali

È possibile registrare nuovamente il server di configurazione nello stesso insieme di credenziali, se necessario. Se si ha un altro computer server di elaborazione, oltre al server di elaborazione predefinito in esecuzione nel computer server di configurazione, registrare nuovamente entrambi i computer.


1. Nell'insieme di credenziali aprire **Gestisci** > **Site Recovery Infrastructure** (Infrastruttura di Site Recovery) > **Server di configurazione**.
2. In **Server** selezionare **Scarica chiave di registrazione** per scaricare il file di credenziali dell'insieme di credenziali.
3. Accedere al computer del server di configurazione.
4. In **%ProgramData%\ASR\home\svsystems\bin** aprire **cspsconfigtool.exe**.
5. Nella scheda **Registrazione insieme di credenziali** selezionare **Sfoglia** e individuare il file di credenziali dell'insieme di credenziali scaricato.
6. Se necessario, fornire i dettagli del server proxy. Selezionare quindi **Register** (Registra).
7. Aprire una finestra dei comandi di PowerShell per amministratore ed eseguire il comando seguente:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Per **eseguire il pull dei certificati più recenti** dal server di configurazione al server di elaborazione scale-out, eseguire il comando *“<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe>" --registermt*

8. Infine, riavviare obengine eseguendo il comando seguente.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrare un server di configurazione con un insieme di credenziali diverso

> [!WARNING]
> Il passaggio seguente rimuove l'associazione tra il server di configurazione e l'insieme di credenziali corrente e la replica di tutte le macchine virtuali protette nel server di configurazione viene arrestata.

1. Accedere al server di configurazione.
2. Aprire una finestra dei comandi di PowerShell per amministratore ed eseguire il comando seguente:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Avviare il portale del browser dell'appliance del server di configurazione usando il collegamento sul desktop.
4. Eseguire la procedura di registrazione simile alla [registrazione](vmware-azure-tutorial.md#register-the-configuration-server) di un nuovo server di configurazione.

## <a name="upgrade-the-configuration-server"></a>Aggiornare il server di configurazione

Per aggiornare il server di configurazione si eseguono aggiornamenti cumulativi. È possibile applicare gli aggiornamenti a un massimo di N-4 versioni. Ad esempio: 

- Se si esegue la versione 9.7, 9.8, 9.9 o 9.10, è possibile eseguire l'aggiornamento direttamente alla versione 9.11.
- Se si esegue la versione 9.6 o una versione precedente e si desidera eseguire l'aggiornamento alla 9.11, è necessario prima eseguire l'aggiornamento alla versione 9.7 e poi alla 9.11.

Per indicazioni dettagliate sul supporto dei componenti di Azure Site Recovery, vedere [qui](https://aka.ms/asr_support_statement) l'informativa sul supporto.
I collegamenti agli aggiornamenti cumulativi a tutte le versioni del server di configurazione sono disponibili [qui](https://aka.ms/asr_update_rollups).

> [!IMPORTANT]
> Con ogni nuova versione 'N' di un componente di Azure Site Recovery rilasciata, tutte le versioni precedenti a 'N-4' sono considerate non supportate. È sempre consigliabile eseguire l'aggiornamento alle versioni più recenti disponibili.</br>
> Per indicazioni dettagliate sul supporto dei componenti di Azure Site Recovery, vedere [qui](https://aka.ms/asr_support_statement) l'informativa sul supporto.

Per aggiornare il server, seguire questa procedura:

1. Nell'insieme di credenziali passare a **Gestisci** > **Infrastruttura di Site Recovery** > **Server di configurazione**.
2. Se è disponibile un aggiornamento, verrà visualizzato un collegamento nella colonna **Versione agente**.
    ![Aggiornamento](./media/vmware-azure-manage-configuration-server/update2.png)
3. Scaricare il file del programma di installazione dell'aggiornamento nel server di configurazione.

    ![Aggiornamento](./media/vmware-azure-manage-configuration-server/update1.png)

4. Fare doppio clic per eseguire il programma di installazione.
5. Il programma di installazione rileva la versione corrente in esecuzione nel computer. Fare clic su **Sì** per avviare l'aggiornamento.
6. Al termine dell'aggiornamento viene convalidata la configurazione del server.

    ![Aggiornamento](./media/vmware-azure-manage-configuration-server/update3.png)

7. Fare clic su **Fine** per chiudere il programma di installazione.
8. Per eseguire l'aggiornamento degli altri componenti di Site Recovery, vedere le [indicazioni sugli aggiornamenti](https://aka.ms/asr_vmware_upgrades).

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Aggiornare il server di configurazione/elaborazione dalla riga di comando

Eseguire il file di installazione come segue:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Esempio di utilizzo
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametri

|Nome parametro| Type | DESCRIZIONE| Valori|
|-|-|-|-|
| /Modalità server|Obbligatorio|Specifica se devono essere installati i server di configurazione e di elaborazione o solo il server di elaborazione|CS<br>PS|
|/InstallLocation|Obbligatorio|Cartella in cui sono installati i componenti| Qualsiasi cartella del computer|
|/MySQLCredsFilePath|Obbligatorio|Percorso del file in cui sono archiviate le credenziali del server MySQL|Il file deve essere nel formato specificato di seguito|
|/VaultCredsFilePath|Obbligatorio|Percorso del file di credenziali dell'insieme di credenziali|Percorso del file valido|
|/EnvType|Obbligatorio|Tipo di ambiente che si vuole proteggere |VMware<br>NonVMware|
|/PSIP|Obbligatorio|Indirizzo IP della scheda di interfaccia di rete da utilizzare per il trasferimento di dati di replica| Qualsiasi indirizzo IP valido|
|/CSIP|Obbligatorio|Indirizzo IP della scheda di interfaccia di rete su cui il server di configurazione è in ascolto| Qualsiasi indirizzo IP valido|
|/PassphraseFilePath|Obbligatorio|Percorso completo del file della passphrase|Percorso del file valido|
|/BypassProxy|Facoltativo|Specifica che il server di configurazione si connette ad Azure senza un proxy|Per ottenere questo valore da Venu|
|/ProxySettingsFilePath|Facoltativo|Impostazioni proxy, il proxy predefinito richiede l'autenticazione o un proxy personalizzato|Il file deve essere nel formato specificato di seguito|
|DataTransferSecurePort|Facoltativo|Numero di porta su PSIP da usare per i dati di replica| Numero di porta valido (il valore predefinito è 9433)|
|/SkipSpaceCheck|Facoltativo|Ignora la verifica dello spazio per il disco della cache| |
|/AcceptThirdpartyEULA|Obbligatorio|Il flag implica l'accettazione dell'EULA di terze parti| |
|/ShowThirdpartyEULA|Facoltativo|Visualizza le condizioni di licenza di terze parti. Se specificato come input, tutti gli altri parametri vengono ignorati| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Creare il file di input per MYSQLCredsFilePath

Il parametro MySQLCredsFilePath usa un file come input. Creare il file usando il formato seguente e passarlo come parametro MySQLCredsFilePath di input.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Creare il file di input per ProxySettingsFilePath
Il parametro ProxySettingsFilePath prende un file come input. Creare il file usando il formato seguente e passarlo come parametro ProxySettingsFilePath di input.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```

## <a name="delete-or-unregister-a-configuration-server"></a>Eliminare o annullare la registrazione di un server di configurazione

1. [Disabilitare la protezione](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) per tutte le macchine virtuali nel server di configurazione.
2. [Annullare l'associazione](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) ed [eliminare](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) tutti i criteri di replica dal server di configurazione.
3. [Eliminare](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) tutti i server vCenter/host vSphere associati al server di configurazione.
4. Nell'insieme di credenziali aprire **Site Recovery Infrastructure** (Infrastruttura di Site Recovery) > **Server di configurazione**.
5. Selezionare il server di configurazione che si vuole rimuovere, quindi nella pagina **Dettagli** selezionare **Elimina**.

    ![Eliminare il server di configurazione](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Eseguire l'eliminazione con PowerShell

Facoltativamente, è possibile eliminare il server di configurazione usando PowerShell.

1. [Installare](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) il modulo Azure PowerShell.
2. Accedere al proprio account di Azure con il seguente comando:

    `Connect-AzureRmAccount`
3. Selezionare la sottoscrizione dell'insieme di credenziali.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Impostare il contesto dell'insieme di credenziali.

    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Recuperare il server di configurazione.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Eliminare il server di configurazione.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> È possibile usare l'opzione **-Force** in Remove-AzureRmSiteRecoveryFabric per l'eliminazione forzata del server di configurazione.

## <a name="generate-configuration-server-passphrase"></a>Generare il passphrase del server di configurazione

1. Accedere al server di configurazione e quindi aprire una finestra del prompt dei comandi come amministratore.
2. Per modificare la directory nella cartella cestino, eseguire il comando **cd %ProgramData%\ASR\home\svsystems\bin**
3. Per generare il file della passphrase, eseguire **genpassphrase.exe -v > MobSvc.passphrase**.
4. La passphrase sarà archiviata nel file che si trova in **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="renew-ssl-certificates"></a>Rinnovare i certificati SSL

Il server di configurazione include un server Web integrato che orchestra le attività del servizio Mobility, dei server di elaborazione e dei server di destinazione master connessi. Il server Web usa un certificato SSL per autenticare i client. Il certificato scade dopo tre anni e può essere rinnovato in qualsiasi momento.

### <a name="check-expiry"></a>Controllare la scadenza

Per le distribuzioni del server di configurazione precedenti maggio 2016, la scadenza del certificato è stata impostata su un anno. Se si ha un certificato che sta per scadere, si verifica quanto segue:

- Quando mancano due mesi o meno alla data di scadenza, il servizio avvia l'invio di notifiche nel portale e tramite posta elettronica (se è stata effettuata la sottoscrizione alle notifiche di Site Recovery).
- Un banner di notifica viene visualizzato nella pagina delle risorse dell'insieme di credenziali. Per altre informazioni, selezionare il banner.
- Se viene visualizzato il pulsante **Aggiorna ora**, alcuni componenti nell'ambiente non sono stati aggiornati alla versione 9.4.xxxx.x o versioni successive. Aggiornare i componenti prima di rinnovare il certificato. Non è possibile eseguire il rinnovo per le versioni precedenti.

### <a name="renew-the-certificate"></a>Rinnovare il certificato

1. Nell'insieme di credenziali aprire **Site Recovery Infrastructure** (Infrastruttura di Site Recovery) > **Server di configurazione**. Selezionare il server di configurazione richiesto.
2. La data di scadenza viene visualizzata in **Integrità del server di configurazione**.
3. Selezionare **Rinnova certificati**.

## <a name="refresh-configuration-server"></a>Aggiornare il server di configurazione

1. Nel portale di Azure passare a **Insieme di credenziali di Servizi di ripristino** > **Gestisci** > **Infrastruttura di Site Recovery**  >  **Per VMware e computer fisici** > **Server di configurazione**
2. Fare clic sul server di configurazione che si vuole aggiornare.
3. Nel pannello con i dettagli del server di configurazione scelto fare clic su **Altro** > **Aggiorna server**.
4. Monitorare lo stato del processo in **Insieme di credenziali di Servizi di ripristino** > **Monitoraggio** > **Processi di Site Recovery**.

## <a name="update-windows-license"></a>Aggiornare la licenza di Windows

La licenza fornita con il modello OVF è una licenza di valutazione valida per 180 giorni. Per un utilizzo senza interruzioni, è necessario attivare Windows con una licenza acquistata.

## <a name="failback-requirements"></a>Requisiti di failback

Durante la riprotezione e il failback, il server di configurazione locale deve essere in esecuzione e connesso. Per la riuscita del failback, la macchina virtuale di cui viene eseguito il failback deve esistere nel database del server di configurazione.

Verificare di eseguire regolarmente i backup pianificati del server di configurazione. Se si verifica un'emergenza e il server di configurazione viene perso, è necessario innanzitutto ripristinare il server di configurazione da una copia di backup e verificare che il server di configurazione ripristinato abbia lo stesso indirizzo IP con cui è stato registrato nell'insieme di credenziali. Il failback non ha esito positivo se per il server di configurazione ripristinato viene usato un indirizzo IP diverso.

## <a name="next-steps"></a>Passaggi successivi

Rivedere le esercitazioni per la configurazione del ripristino di emergenza delle [macchine virtuali VMware](vmware-azure-tutorial.md) in Azure.
