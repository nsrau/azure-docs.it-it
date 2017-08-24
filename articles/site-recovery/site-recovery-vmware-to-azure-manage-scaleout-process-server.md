---
title: " Gestire un server di elaborazione con scalabilità orizzontale in Azure Site Recovery | Microsoft Docs"
description: "Questo articolo descrive come configurare e gestire un server di elaborazione con scalabilità orizzontale in Azure Site Recovery."
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
ms.date: 06/29/2017
ms.author: anoopkv
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: e5c01de19917235c34c035415df86291b9152bf0
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

---

# <a name="manage-a-scale-out-process-server"></a>Gestire un server di elaborazione con scalabilità orizzontale

Il server di elaborazione con scalabilità orizzontale funge da coordinatore per il trasferimento dei dati tra i servizi di Site Recovery e l'infrastruttura locale in uso. Questo articolo descrive come impostare, configurare e gestire i server di elaborazione con scalabilità orizzontale.

## <a name="prerequisites"></a>Prerequisiti
La tabella seguente elenca i requisiti minimi hardware, software e di rete richiesti per impostare un server di elaborazione con scalabilità orizzontale.

> [!NOTE]
> La [pianificazione della capacità](site-recovery-capacity-planner.md) è un passaggio importante per assicurarsi di distribuire un server di elaborazione con scalabilità orizzontale con una configurazione adatta ai requisiti di carico. Altre informazioni sulle [caratteristiche di scalabilità per un server di elaborazione con scalabilità orizzontale](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>Download del software per il server di elaborazione con scalabilità orizzontale
1. Accedere al portale di Azure e passare all'insieme di credenziali dei servizi di ripristino.
2. Passare a **Infrastruttura di Site Recovery** > **Server di configurazione**, in For VMware & Physical Machines (Computer VMware e fisici).
3. Selezionare il server di configurazione per eseguire il drill-down della relativa pagina delle informazioni.
4. Fare clic sul pulsante **+ Server di elaborazione**.
5. Nella pagina **Aggiungi server di elaborazione** selezionare l'opzione **Distribuire un server di elaborazione con scalabilità orizzontale in locale** dall'elenco a discesa **Scegliere dove distribuire il server di elaborazione**.

  ![Pagina Aggiungi server](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. Fare clic sul collegamento per **scarica l'installazione unificata di Microsoft Azure Site Recovery** per ottenere la versione più recente per l'installazione del server di elaborazione con scalabilità orizzontale.

  > [!WARNING]
  La versione del server di elaborazione con scalabilità orizzontale deve essere uguale o inferiore rispetto alla versione del server di configurazione in esecuzione nell'ambiente. Un modo semplice per garantire la compatibilità tra le versioni è l'uso degli stessi bit del programma di installazione usato per installare/aggiornare il server di configurazione.

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>Installazione e registrazione di un server di elaborazione con scalabilità orizzontale dall'interfaccia utente grafica
Se è necessario ridimensionare la distribuzione oltre 200 computer di origine oppure la varianza totale giornaliera è superiore a 2 TB, sono necessari server di elaborazione aggiuntivi per gestire il volume di traffico.

Vedere [Dimensioni consigliate per il server di elaborazione](#size-recommendations-for-the-process-server) e quindi seguire queste istruzioni per configurare il server di elaborazione. Dopo aver configurato il server è possibile eseguire la migrazione dei computer di origine per usarlo.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>Installazione e registrazione di un server di elaborazione con scalabilità orizzontale dalla riga di comando

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>Esempio di utilizzo
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>Argomenti della riga di comando del programma di installazione del server di elaborazione con scalabilità orizzontale.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>Creare un file di configurazione delle impostazioni del proxy
Il parametro ProxySettingsFilePath usa un file come input. Creare il file usando il formato seguente e passarlo come parametro ProxySettingsFilePath di input.
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>Modifica delle impostazioni proxy per il server di elaborazione con scalabilità orizzontale
1. Accedere al server di elaborazione con scalabilità orizzontale.
2. Aprire una finestra dei comandi di PowerShell per amministratore.
3. Eseguire il comando seguente
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. Passare alla directory **%PROGRAMDATA%\ASR\Agent** ed eseguire il comando seguente
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>Nuova registrazione di un server di elaborazione con scalabilità orizzontale
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* Quindi aprire un prompt dei comandi come amministratore.
* Passare alla directory **%PROGRAMDATA%\ASR\Agent** ed eseguire il comando

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>Aggiornamento di un server di elaborazione con scalabilità orizzontale
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>Rimozione di un server di elaborazione con scalabilità orizzontale
1. Assicurarsi che:
  - Lo stato della connessione del server di configurazione sia visualizzato come **connesso** nel portale di Azure.
  - Il server di elaborazione sia ancora in grado di comunicare con il server di configurazione.
2. Accedere al server di elaborazione come amministratore.
3. Scegliere Pannello di controllo > Programma > Disinstallare programmi
4. Disinstallare i programmi nella sequenza seguente:
  * Server di elaborazione/Server di configurazione di Microsoft Azure Site Recovery
  * Dipendenze del server di configurazione di Microsoft Azure Site Recovery
  * Agente di Servizi di ripristino di Microsoft Azure

Possono essere necessari fino a 15 minuti affinché l'eliminazione del server di elaborazione sia visualizzata nel portale di Azure.

  > [!NOTE]
  Se il server di elaborazione non è in grado di comunicare con il server di configurazione e quindi nel portale lo stato della connessione è Disconnesso, seguire questa procedura rimuoverlo dal server di configurazione.

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>Annullamento della registrazione di un server di elaborazione con scalabilità orizzontale disconnesso da un server di configurazione

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>Requisiti per il ridimensionamento di un server di elaborazione con scalabilità orizzontale

| **Server di elaborazione aggiuntivo** | **Dimensione disco cache** | **Frequenza di modifica dei dati** | **Computer protetti** |
| --- | --- | --- | --- |
|4 vCPU (2 socket * 2 core a 2,5 GHz), 8 GB di memoria |300 GB |250 GB o inferiore |Replicare un massimo di 85 computer. |
|8 vCPU (2 socket * 4 core a 2,5 GHz), 12 GB di memoria |600 GB |Da 250 GB a 1 TB |Replicare tra 85 e 150 computer. |
|12 vCPU (2 socket * 6 core a 2,5 GHz), 24 GB di memoria |1 TB |Da 1 TB a 2 TB |Replicare tra 150 e 225 computer. |

