---
title: " Gestire il server di configurazione per il ripristino di emergenza di server fisici con Azure Site Recovery | Microsoft Docs"
description: Questo articolo descrive come gestire un server di configurazione esistente per il ripristino di emergenza di server fisici in Azure, con il servizio Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 2fdccade577788d3fc5bc076604547b2ab6690d9
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Gestire il server di configurazione per il ripristino di emergenza di server fisici

Si configura un server di configurazione locale quando si usa il servizio [Azure Site Recovery](site-recovery-overview.md) per il ripristino di emergenza dei server fisici in Azure. Il server di configurazione coordina le comunicazioni tra i computer locali e Azure e gestisce la replica dei dati. Questo articolo riepiloga le attività comuni per la gestione del server di configurazione dopo che è stato distribuito.

## <a name="prerequisites"></a>prerequisiti

La tabella riepiloga i prerequisiti per la distribuzione del server di configurazione locale.

| **Componente** | **Requisito** |
| --- |---|
| Core CPU| 8 |
| RAM | 12 GB|
| Numero di dischi | 3, inclusi disco del sistema operativo, disco della cache del server di elaborazione e unità di conservazione per il failback |
| Spazio libero su disco (cache del server di elaborazione) | 600 GB
| Spazio libero su disco (disco di conservazione) | 600 GB|
| Sistema operativo  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Impostazioni locali del sistema operativo | Inglese (Stati Uniti)|
| Versione di VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Ruoli di Windows Server | Non abilitare questi ruoli: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Criteri di gruppo| Non abilitare questi criteri di gruppo: <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> [Altre informazioni](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - Nessun sito Web predefinito preesistente <br> - Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br> - Nessun sito Web o applicazione preesistente in ascolto sulla porta 443<br>|
| Tipo di scheda di interfaccia di rete | VMXNET3 (quando distribuito come macchina virtuale VMware) |
| Tipo di indirizzo IP | statico |
| Accesso a Internet | Il server deve poter accedere a questi URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - dc.services.visualstudio.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (non necessario per i server di elaborazione con scalabilità orizzontale) <br> - time.nist.gov <br> - time.windows.com |
| Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati)|

## <a name="download-the-latest-installation-file"></a>Scaricare il file di installazione più recente

L'ultima versione del file di installazione del server di configurazione è disponibile nel portale di Site Recovery. Può essere inoltre scaricato direttamente dall'[Area download Microsoft](http://aka.ms/unifiedsetup).

1. Accedere al portale di Azure e passare all'insieme di credenziali di Servizi di ripristino.
2. Passare a **Infrastruttura di Site Recovery** > **Server di configurazione** (in For VMware & Physical Machines (Computer VMware e fisici)).
3. Fare clic sul pulsante **+Servers** (+Server).
4. Nella pagina **Add Server** (Aggiungi server) fare clic sul pulsante Download (Scarica) per scaricare la chiave di registrazione. Questa chiave viene usata durante l'installazione del server di configurazione ai fini della registrazione con il servizio Azure Site Recovery.
5. Fare clic sul collegamento **Download the Microsoft Azure Site Recovery Unified Setup** (Scarica l'installazione unificata di Microsoft Azure Site Recovery) per scaricare la versione più recente del server di configurazione.

  ![Pagina di download](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Installare e registrare il server

1. Eseguire il file di installazione per l'Installazione unificata.
2. In **Before you begin** (Prima di iniziare) selezionare **Install the configuration server and process server** (Installare il server di configurazione e il server di elaborazione).

    ![Prima di iniziare](./media/physical-manage-configuration-server/combined-wiz1.png)

3. In **Licenza software di terze parti** fare clic su **Accetto** per scaricare e installare MySQL.
4. In **Impostazioni Internet** specificare in che modo il provider in esecuzione nel server di configurazione si connette ad Azure Site Recovery tramite Internet. Accertarsi di aver autorizzato gli URL necessari.

    - Per connettersi al proxy attualmente configurato nel computer, selezionare **Connetti ad Azure Site Recovery usando un server proxy**.
    - Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
    - Se per il proxy esistente è necessaria l'autenticazione o si vuole usare un proxy personalizzato per la connessione del provider, selezionare **Connect with custom proxy settings** (Connetti con le impostazioni proxy personalizzate) e specificare indirizzo, porta e credenziali.
     ![Firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. In **Controllo dei prerequisiti** il programma di installazione esegue un controllo per assicurarsi che sia possibile eseguire l'installazione. Se viene visualizzato un avviso relativo al **Global time sync check** (Controllo della sincronizzazione ora globale), verificare che l'ora del clock di sistema, nelle impostazioni di **Data e ora**, corrisponda al fuso orario.

    ![prerequisiti](./media/physical-manage-configuration-server/combined-wiz5.png)
7. In **MySQL Configuration** (Configurazione MySQL) creare le credenziali per l'accesso all'istanza del server MySQL che viene installata.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. In **Dettagli ambiente** specificare se si vuole eseguire la replica di VM VMware. In caso affermativo, il programma di installazione verifica quindi se è installato PowerCLI 6.0.
9. In **Percorso di installazione** specificare il percorso di installazione dei file binari e di archiviazione della cache. L'unità selezionata deve avere almeno 5 GB di spazio su disco disponibile, ma è consigliabile usare un'unità cache con almeno 600 GB di spazio disponibile.

    ![Percorso di installazione](./media/physical-manage-configuration-server/combined-wiz8.png)
10. In **Network Selection** (Selezione rete) specificare il listener, ovvero la scheda di rete e la porta SSL, in cui il server di configurazione deve inviare e ricevere i dati di replica. La porta 9443 è la porta predefinita per l'invio e la ricezione del traffico di replica, ma è possibile modificare il numero di porta in base ai requisiti dell'ambiente. Oltre alla porta 9443, viene aperta anche la porta 443, che viene usata da un server Web per orchestrare le operazioni di replica. Non usare la porta 443 per inviare o ricevere traffico di replica.

    ![Selezione rete](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Esaminare le informazioni nella pagina **Riepilogo** e fare clic su **Installa**. Al termine dell'installazione verrà generata una passphrase. Sarà necessaria quando si abilita la replica, è quindi consigliabile copiarla e conservarla in un luogo sicuro.


Al termine della registrazione, il server viene visualizzato nel pannello **Impostazioni** > **Server** nell'insieme di credenziali.


## <a name="install-from-the-command-line"></a>Eseguire l'installazione dalla riga di comando

Eseguire il file di installazione come segue:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Esempio di utilizzo
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametri

|Nome parametro| type | DESCRIZIONE| Valori|
|-|-|-|-|
| /Modalità server|Obbligatoria|Specifica se devono essere installati i server di configurazione e di elaborazione o solo il server di elaborazione|CS<br>PS|
|/InstallLocation|Obbligatoria|Cartella in cui sono installati i componenti| Qualsiasi cartella del computer|
|/MySQLCredsFilePath|Obbligatoria|Percorso del file in cui sono archiviate le credenziali del server MySQL|Il file deve essere nel formato specificato di seguito|
|/VaultCredsFilePath|Obbligatoria|Percorso del file di credenziali dell'insieme di credenziali|Percorso del file valido|
|/EnvType|Obbligatoria|Tipo di ambiente che si desidera proteggere |VMware<br>NonVMware|
|/PSIP|Obbligatoria|Indirizzo IP della scheda di interfaccia di rete da utilizzare per il trasferimento di dati di replica| Qualsiasi indirizzo IP valido|
|/CSIP|Obbligatoria|Indirizzo IP della scheda di interfaccia di rete su cui il server di configurazione è in ascolto| Qualsiasi indirizzo IP valido|
|/PassphraseFilePath|Obbligatoria|Percorso completo del file della passphrase|Percorso del file valido|
|/BypassProxy|Facoltativo|Specifica che il server di configurazione si connette ad Azure senza un proxy|Per ottenere questo valore da Venu|
|/ProxySettingsFilePath|Facoltativo|Impostazioni proxy, il proxy predefinito richiede l'autenticazione o un proxy personalizzato|Il file deve essere nel formato specificato di seguito|
|DataTransferSecurePort|Facoltativo|Numero di porta su PSIP da usare per i dati di replica| Numero di porta valido (il valore predefinito è 9433)|
|/SkipSpaceCheck|Facoltativo|Ignora la verifica dello spazio per il disco della cache| |
|/AcceptThirdpartyEULA|Obbligatoria|Il flag implica l'accettazione dell'EULA di terze parti| |
|/ShowThirdpartyEULA|Facoltativo|Visualizza le condizioni di licenza di terze parti. Se specificato come input, tutti gli altri parametri vengono ignorati| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Creare il file di input per MYSQLCredsFilePath

Il parametro MySQLCredsFilePath usa un file come input. Creare il file usando il formato seguente e passarlo come parametro MySQLCredsFilePath di input.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Creare il file di input per ProxySettingsFilePath
Il parametro ProxySettingsFilePath prende un file come input. Creare il file usando il formato seguente e passarlo come parametro ProxySettingsFilePath di input.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Modificare le impostazioni proxy

È possibile modificare le impostazioni proxy per il server di configurazione come segue:

1. Accedere al server di configurazione.
2. Avviare il file cspsconfigtool.exe usando il relativo collegamento.
3. Fare clic sulla scheda **Vault Registration** (Registrazione dell'insieme di credenziali).
4. Scaricare un nuovo file di registrazione dell'insieme di credenziali dal portale e fornirlo come input allo strumento.

  ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Specificare i dettagli del nuovo proxy e fare clic sul pulsante **Register** (Registra).
6. Aprire una finestra di prompt dei comandi di PowerShell per amministratore.
7. Eseguire il comando seguente:
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Se sono presenti altri server di elaborazione associati al server di configurazione, è necessario [correggere le impostazioni del proxy in tutti i server di elaborazione con scalabilità orizzontale](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) nella distribuzione.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Registrare di nuovo un server di configurazione con lo stesso insieme di credenziali
  1. Accedere al server di configurazione.
  2. Avviare il file cspsconfigtool.exe usando il relativo collegamento sul desktop.
  3. Fare clic sulla scheda **Vault Registration** (Registrazione dell'insieme di credenziali).
  4. Scaricare un nuovo file di registrazione dal portale e fornirlo come input allo strumento.
        ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
  5. Specificare i dettagli del server proxy e fare clic sul pulsante **Register** (Registra).  
  6. Aprire una finestra di prompt dei comandi di PowerShell per amministratore.
  7. Eseguire il comando seguente

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Se si hanno più server di elaborazione, è necessario [registrarli nuovamente](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrare un server di configurazione con un insieme di credenziali diverso

> [!WARNING]
> Il passaggio seguente rimuove l'associazione tra il server di configurazione e l'insieme di credenziali corrente e la replica di tutte le macchine virtuali protette nel server di configurazione viene arrestata.

1. Accedere al server di configurazione
2. Da un prompt dei comandi di amministratore eseguire il comando:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Avviare il file cspsconfigtool.exe usando il relativo collegamento sul desktop.
4. Fare clic sulla scheda **Vault Registration** (Registrazione dell'insieme di credenziali).
5. Scaricare un nuovo file di registrazione dal portale e fornirlo come input allo strumento.
6. Specificare i dettagli del server proxy e fare clic sul pulsante **Register** (Registra).  
7. Aprire una finestra di prompt dei comandi di PowerShell per amministratore.
8. Eseguire il comando seguente
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Aggiornare un server di configurazione

Per aggiornare il server di configurazione si eseguono aggiornamenti cumulativi. È possibile applicare gli aggiornamenti a un massimo di N-4 versioni. Ad esempio: 

- Se si esegue la versione 9.7, 9.8, 9.9 o 9.10, è possibile eseguire l'aggiornamento direttamente alla versione a 9.11.
- Se si esegue la versione 9.6 o una versione precedente e si desidera eseguire l'aggiornamento alla 9.11, è necessario prima eseguire l'aggiornamento alla versione 9.7 e poi alla 9.11.

I collegamenti agli aggiornamenti cumulativi per tutte le versioni del server di configurazione sono disponibili nella [pagina wiki degli aggiornamenti](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Per aggiornare il server, seguire questa procedura:

1. Scaricare il file del programma di installazione dell'aggiornamento nel server di configurazione.
2. Fare doppio clic per eseguire il programma di installazione.
3. Il programma di installazione rileva la versione corrente in esecuzione nel computer.
4. Fare clic su **OK** per confermare ed eseguire l'aggiornamento. 


## <a name="delete-or-unregister-a-configuration-server"></a>Eliminare o annullare la registrazione di un server di configurazione

> [!WARNING]
> Prima di iniziare a rimuovere le autorizzazioni per il server di configurazione, assicurarsi di eseguire queste operazioni.
> 1. [Disabilitare la protezione](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) di tutte le macchine virtuali in questo server di configurazione.
> 2. [Annullare l'associazione](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) ed [eliminare](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) tutti i criteri di replica dal server di configurazione.
> 3. [Eliminare](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) tutti i server vCenter/host vSphere associati al server di configurazione.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Eliminare il server di configurazione dal portale di Azure
1. Nel portale di Azure passare a **Infrastruttura di Site Recovery** > **Server di configurazione** dal menu Insieme di credenziali.
2. Fare clic sul server di configurazione per cui si vogliono rimuovere le autorizzazioni.
3. Nella pagina dei dettagli del server di configurazione fare clic sul pulsante **Elimina**.
4. Fare clic su **Sì** per confermare l'eliminazione del server.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Disinstallare il server di configurazione e le relative dipendenze
  > [!TIP]
  Se si intende riutilizzare il server di configurazione con Azure Site Recovery, è possibile andare direttamente al passaggio 4

1. Accedere al server di configurazione come amministratore.
2. Scegliere Pannello di controllo > Programma > Disinstallare programmi
3. Disinstallare i programmi nella sequenza seguente:
  * Agente di Servizi di ripristino di Microsoft Azure
  * Servizio Mobility di Microsoft Azure Site Recovery/server di destinazione master
  * Provider di Microsoft Azure Site Recovery
  * Server di elaborazione/Server di configurazione di Microsoft Azure Site Recovery
  * Dipendenze del server di configurazione di Microsoft Azure Site Recovery
  * MySQL Server 5.5
4. Al prompt dei comandi dell'amministratore, eseguire questo comando:
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Eliminare o annullare la registrazione di un server di configurazione (PowerShell)

1. [Installare](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) il modulo Azure PowerShell
2. Accedere al proprio account di Azure usando il comando
    
    `Login-AzureRmAccount`
3. Selezionare la sottoscrizione in cui è presente l'insieme di credenziali

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Configurare ora il contesto dell'insieme di credenziali
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Selezionare il server di configurazione

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Eliminare il server di configurazione

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> L'opzione **-Force** in Remove-AzureRmSiteRecoveryFabric può essere usata per forzare la rimozione/eliminazione del server di configurazione.

## <a name="renew-ssl-certificates"></a>Rinnovare i certificati SSL
Il server di configurazione include un server Web integrato che orchestra le attività del servizio Mobility, dei server di elaborazione e dei server di destinazione master connessi. Il server Web usa un certificato SSL per autenticare i client. Il certificato scade dopo tre anni e può essere rinnovato in qualsiasi momento.

### <a name="check-expiry"></a>Controllare la scadenza

Per le distribuzioni del server di configurazione precedenti maggio 2016, la scadenza del certificato è stata impostata su un anno. Se si ha un certificato che sta per scadere, si verifica quanto segue:

- Quando mancano due mesi o meno alla data di scadenza, il servizio avvia l'invio di notifiche nel portale e tramite posta elettronica (se è stata effettuata la sottoscrizione alle notifiche di Azure Site Recovery).
- Un banner di notifica viene visualizzato nella pagina delle risorse dell'insieme di credenziali. Fare clic sul banner per altri dettagli.
- Se viene visualizzato il pulsante **Aggiorna ora**, significa che esistono alcuni componenti nell'ambiente che non sono stati aggiornati alla versione 9.4.xxxx.x o versioni successive. Aggiornare i componenti prima di rinnovare il certificato. Non è possibile eseguire il rinnovo per le versioni precedenti.

### <a name="renew-the-certificate"></a>Rinnovare il certificato

1. Nell'insieme di credenziali aprire **Site Recovery Infrastructure** (Infrastruttura di Site Recovery) > **Server di configurazione** e fare clic sul server di configurazione necessario.
2. La data di scadenza viene visualizzata in **Integrità del server di configurazione**
3. Fare clic su **Rinnova certificati**. 




## <a name="common-issues"></a>Problemi comuni
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Passaggi successivi

Rivedere le esercitazioni per la configurazione del ripristino di emergenza dei [server fisici](tutorial-physical-to-azure.md) in Azure.

