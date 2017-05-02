---
title: " Gestire un server di configurazione in Azure Site Recovery | Microsoft Docs"
description: L&quot;articolo descrive come impostare e gestire un server di configurazione.
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/14/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 3b03b59fd338fa31c3248b0798463ee659e7e5ff
ms.lasthandoff: 04/12/2017

---

# <a name="manage-a-configuration-server"></a>Gestire un server di configurazione

Il server di configurazione funge da coordinatore tra i servizi di Site Recovery e l'infrastruttura locale in uso. Questo articolo illustra come impostare, configurare e gestire il server di configurazione.

## <a name="prerequisites"></a>Prerequisiti
La tabella seguente elenca i requisiti minimi hardware, software e di rete richiesti per impostare un server di configurazione.

> [!NOTE]
> La [pianificazione della capacità](site-recovery-capacity-planner.md) è un passaggio importante per assicurarsi di distribuire un server di configurazione con una configurazione adatta ai requisiti di carico. Per altre informazioni, vedere la sezione [Requisiti di dimensione per un server di configurazione](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-configuration-server-software"></a>Download del software del server di configurazione
1. Accedere al portale di Azure e passare all'insieme di credenziali di Servizi di ripristino.
2. Passare a **Infrastruttura di Site Recovery** > **Server di configurazione** (in For VMware & Physical Machines (Computer VMware e fisici)).

  ![Pagina di aggiunta server](./media/site-recovery-vmware-to-azure-manage-configuration-server/AddServers.png)
3. Fare clic sul pulsante **+Servers** (+Server).
4. Nella pagina **Add Server** (Aggiungi server) fare clic sul pulsante Download (Scarica) per scaricare la chiave di registrazione. Questa chiave viene usata durante l'installazione del server di configurazione ai fini della registrazione con il servizio Azure Site Recovery.
5. Fare clic sul collegamento **Download the Microsoft Azure Site Recovery Unified Setup** (Scarica l'installazione unificata di Microsoft Azure Site Recovery) per scaricare la versione più recente del server di configurazione.

  ![Pagina di download](./media/site-recovery-vmware-to-azure-manage-configuration-server/downloadcs.png)

  > [!TIP]
  La versione più recente del server di configurazione può essere scaricata direttamente dalla [pagina di download dell'Area download Microsoft](http://aka.ms/unifiedsetup)

## <a name="installing-and-registering-a-configuration-server-from-gui"></a>Installazione e registrazione di un server di configurazione dall'interfaccia utente grafica
[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

## <a name="installing-and-registering-a-configuration-server-using-command-line"></a>Installazione e registrazione di un server di configurazione dalla riga di comando

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Esempio di utilizzo
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="configuration-server-installer-command-line-arguments"></a>Argomenti della riga di comando del programma di installazione del server di configurazione.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-mysql-credentials-file"></a>Creare un file di credenziali di MySql
Il parametro MySQLCredsFilePath prende un file come input. Creare il file usando il formato seguente e passarlo come parametro MySQLCredsFilePath di input.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-a-proxy-settings-configuration-file"></a>Creare un file di configurazione delle impostazioni del proxy
Il parametro ProxySettingsFilePath prende un file come input. Creare il file usando il formato seguente e passarlo come parametro ProxySettingsFilePath di input.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-configuration-server"></a>Modifica delle impostazioni del proxy per il server di configurazione
1. Accedere al server di configurazione.
2. Avviare il file cspsconfigtool.exe usando il relativo collegamento.
3. Fare clic sulla scheda **Vault Registration** (Registrazione dell'insieme di credenziali).
4. Scaricare un nuovo file di registrazione dell'insieme di credenziali dal portale e fornirlo come input allo strumento.

  ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
5. Specificare i dettagli del nuovo server proxy e fare clic sul pulsante **Register** (Registra).
6. Aprire una finestra di prompt dei comandi di PowerShell per amministratore.
7. Eseguire il comando seguente
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Se sono presenti server di elaborazione con scalabilità orizzontale associati a questo server di configurazione, è necessario [correggere le impostazioni del proxy in tutti i server di elaborazione con scalabilità orizzontale](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server) nella distribuzione.

## <a name="re-register-a-configuration-server-with-the-same-recovery-services-vault"></a>Registrare un server di configurazione con lo stesso insieme di credenziali di Servizi di ripristino
  1. Accedere al server di configurazione.
  2. Avviare il file cspsconfigtool.exe usando il relativo collegamento sul desktop.
  3. Fare clic sulla scheda **Vault Registration** (Registrazione dell'insieme di credenziali).
  4. Scaricare un nuovo file di registrazione dal portale e fornirlo come input allo strumento.
        ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
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
  Se sono presenti server di elaborazione con scalabilità orizzontale associati a questo server di configurazione, è necessario [registrare di nuovo tutti i server di elaborazione con scalabilità orizzontale](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server) nella distribuzione.

## <a name="registering-a-configuration-server-with-a-different-recovery-services-vault"></a>Registrazione di un server di configurazione con un insieme di credenziali di Servizi di ripristino diverso.
1. Accedere al server di configurazione.
2. Al prompt dei comandi dell'amministratore, eseguire il comando

```
reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
net stop dra
```
3. Avviare il file cspsconfigtool.exe usando il relativo collegamento.
4. Fare clic sulla scheda **Vault Registration** (Registrazione dell'insieme di credenziali).
5. Scaricare un nuovo file di registrazione dal portale e fornirlo come input allo strumento.

    ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
6. Specificare i dettagli del server proxy e fare clic sul pulsante **Register** (Registra).  
7. Aprire una finestra di prompt dei comandi di PowerShell per amministratore.
8. Eseguire il comando seguente
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="decommissioning-a-configuration-server"></a>Rimozione delle autorizzazioni per un server di configurazione
Prima di iniziare a rimuovere le autorizzazioni per il server di configurazione, assicurarsi di eseguire queste operazioni.
1. Disabilitare la protezione di tutte le macchine virtuali in questo server di configurazione.
2. Annullare l'associazione di tutti i criteri di replica dal server di configurazione.
3. Eliminare tutti i server VCenter/host vSphere associati al server di configurazione.

### <a name="delete-the-configuration-server-from-azure-portal"></a>Eliminare il server di configurazione dal portale di Azure
1. Nel portale di Azure passare a **Infrastruttura di Site Recovery** > **Server di configurazione** dal menu Insieme di credenziali.
2. Fare clic sul server di configurazione per cui si desidera rimuovere le autorizzazioni.
3. Nella pagina dei dettagli del server di configurazione fare clic sul pulsante Elimina.

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.PNG)
4. Fare clic su **Sì** per confermare l'eliminazione del server.

  >[!WARNING]
  Se il server di configurazione è associato a macchine virtuali, criteri di replica o server vCenter/host vSphere, non è possibile eliminarlo. Eliminare queste entità prima di provare a eliminare l'insieme di credenziali.

### <a name="uninstall-the-configuration-server-software-and-its-dependencies"></a>Disinstallare il software del server di configurazione e le relative dipendenze
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

## <a name="renew-configuration-server-secure-socket-layerssl-certificates"></a>Rinnovare i certificati SSL (Server Secure Socket Layer) di configurazione
Il server di configurazione include un server Web integrato che si occupa di orchestrare le attività del servizio Mobility, dei server di elaborazione e dei server di destinazione master connessi al server di configurazione. Il server Web del server di configurazione usa un certificato SSL per l'autenticazione dei client. Questo certificato ha una scadenza di tre anni e può essere rinnovato in qualsiasi momento usando il metodo seguente:

> [!WARNING]
La scadenza del certificato può avvenire solo nella versione 9.4.XXXX.X o versione successiva. Aggiornare tutti i componenti di Azure Site Recovery (server di configurazione, server di elaborazione, server di destinazione master e servizio Mobility) prima di avviare il flusso di lavoro relativo al rinnovo dei certificati.

1. Nel portale di Azure passare a Insieme di credenziali > Infrastruttura di Site Recovery > Server di configurazione.
2. Fare clic sul server di configurazione per il quale è necessario rinnovare il certificato SSL.
3. Nell'ambito dell'integrità del server di configurazione, è possibile visualizzare la data di scadenza del certificato SSL.
4. Rinnovare il certificato facendo clic sull'azione **Rinnova i certificati** come illustrato nell'immagine seguente:

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/renew-cert-page.png)

### <a name="secure-socket-layer-certificate-expiry-warning"></a>Avviso di scadenza del certificato SSL

> [!NOTE]
La validità del certificato SSL per tutte le installazioni eseguite prima di maggio 2016 è stata impostata su un anno. Nel portale di Azure hanno iniziato a comparire notifiche di scadenza certificato.

1. Due mesi prima che il certificato SSL del server di configurazione scada il servizio inizia ad avvertire gli utenti tramite il portale di Azure e la posta elettronica. Per ricevere questi avvisi è necessario sottoscrivere le notifiche di Azure Site Recovery. Si inizia a comparire un banner di notifica nella pagina delle risorse dell'insieme di credenziali.

  ![certificate-notification](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-renew-notification.png)
2. Fare clic sul banner per visualizzare informazioni aggiuntive sulla scadenza del certificato.

  ![certificate-details](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-expiry-details.png)

  >[!TIP]
  Se al posto del pulsante **Renew Now** (Rinnova ora) viene visualizzato un pulsante **Upgrade Now** (Aggiorna ora). Significa che esistono alcuni componenti nell'ambiente che non sono stati ancora aggiornati alla versione 9.4.xxxx.x o versioni successive.

## <a name="sizing-requirements-for-a-configuration-server"></a>Requisiti di dimensione per un server di configurazione

| **CPU** | **Memoria** | **Dimensione disco cache** | **Frequenza di modifica dei dati** | **Computer protetti** |
| --- | --- | --- | --- | --- |
| 8 vCPU (2 socket * 4 core a 2,5 GHz) |16 GB |300 GB |500 GB o inferiore |Replicare meno di 100 computer. |
| 12 vCPU (2 socket * 6 core a 2,5 GHz) |18 GB |600 GB |Da 500 GB a 1 TB |Replicare tra 100 e 150 computer. |
| 16 vCPU (2 socket * 8 core a 2,5 GHz) |32 GB |1 TB |Da 1 TB a 2 TB |Replicare tra 150 e 200 computer. |

  >[!TIP]
  Se la varianza dei dati giornalieri supera 2 TB o si intende replicare più di 200 macchine virtuali, è consigliabile distribuire ulteriori server di elaborazione per bilanciare il traffico di replica. Altre informazioni su come distribuire i server di elaborazione con scalabilità orizzontale.


## <a name="common-issues"></a>Problemi comuni
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

