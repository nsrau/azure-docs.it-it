---
title: Servizio app di note sulla versione 1 di aggiornamento dello Stack di Azure | Documenti Microsoft
description: Informazioni sulle caratteristiche di aggiornamento per il servizio App nello Stack di Azure, i problemi noti e dove scaricare l'aggiornamento.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 538d31f5b50ee22c06ba22c78e1aa92281a3b212
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Servizio app di note sulla versione 1 di aggiornamento dello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Queste note sulla versione vengono descritti i miglioramenti e correzioni in Azure App Service in Azure Stack Update 1 e i problemi noti. Problemi noti sono suddivisi in problemi direttamente correlati alla distribuzione, processo di aggiornamento e i problemi con la compilazione (post-installazione).

> [!IMPORTANT]
> Applicare l'aggiornamento 1802 al sistema Azure Stack integrato o distribuire il kit di sviluppo dello Stack di Azure più recente prima di distribuire il servizio App di Azure.
>
>

## <a name="build-reference"></a>Compilazione di riferimento

Il servizio App sul numero di build Azure Stack Update 1 è **69.0.13698.9**

### <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Nuove distribuzioni di Azure App Service nello Stack di Azure è ora necessario un [certificato con caratteri jolly tre soggetto](azure-stack-app-service-before-you-get-started.md#get-certificates) in seguito ai miglioramenti nel modo in cui SSO per Kudu viene gestito nel servizio App di Azure.  È il nuovo argomento * * *. sso.appservice.<region>. <domainname>.<extension>**
>
>

Consultare il [documentazione prima di iniziare a](azure-stack-app-service-before-you-get-started.md) prima di iniziare la distribuzione.

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni

Servizio App di Azure in Azure Stack Update 1 include gli aggiornamenti e i miglioramenti seguenti:

- **Elevata disponibilità del servizio App di Azure** -i carichi di lavoro di The Azure Stack 1802 update è attivato per essere distribuite tra più domini di errore.  Pertanto infrastruttura del servizio App è in grado di essere a tolleranza di errore in quanto verrà distribuito nei domini di errore.  Per impostazione predefinita tutte le nuove distribuzioni di servizio App di Azure avrà questa funzionalità tuttavia per le distribuzioni completate, prima di Azure Stack 1802 aggiornamento viene applicato consultare il [documentazione di dominio di errore di servizio App](azure-stack-app-service-fault-domain-update.md)

- **Distribuire nella rete virtuale esistente** -i clienti possono ora distribuire App servizio nello Stack di Azure all'interno di una rete virtuale esistente.  Distribuzione in una rete virtuale esistente consente ai clienti per la connessione al Server SQL e File Server, necessari per il servizio App di Azure, tramite le porte private.  Durante la distribuzione, i clienti possono scegliere di distribuire in una rete virtuale esistente, tuttavia [necessario creare subnet per l'utilizzo dal servizio App](azure-stack-app-service-before-you-get-started.md#virtual-network) prima della distribuzione.

- Gli aggiornamenti a **Tenant di servizio App, amministratore, i portali di funzioni e gli strumenti Kudu**.  Coerentemente con versione del SDK portale di Azure dello Stack.

- **Gli aggiornamenti per gli strumenti e Framework di applicazioni seguenti**:
    - Aggiunto **.Net Core 2.0** supportano
    - Aggiunto **Node. js** versioni:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Aggiunto **NPM** versioni:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Aggiunto **PHP** aggiornamenti:
        - 5.6.32
        - 7.0.26 (x86 e x64)
        - 7.1.12 (x86 e x64)
    - Aggiornato **Git per Windows** a v 2.14.1
    - Aggiornato **Mercurial** a v4.5.0

  - Aggiunta del supporto per **solo HTTPS** funzionalità all'interno di funzionalità dominio personalizzato nel portale Tenant di servizio App. 

  - Convalida aggiuntiva della connessione di archiviazione nel selettore di archiviazione personalizzati per le funzioni di Azure 

#### <a name="fixes"></a>Correzioni

- Quando si crea un pacchetto di distribuzione non in linea, i clienti non riceveranno più un accesso negato quando si apre la cartella dal programma di installazione di servizio App

- Risolvere i problemi quando si utilizza la funzionalità di domini personalizzati nel portale Tenant di servizio App.

- Evitare che i clienti che usano i nomi degli amministratori riservato durante l'installazione

- Abilitata la distribuzione di servizio App con **appartenenti a un dominio** file server di

- Miglioramenti apportati al recupero della radice dello Stack di Azure nello script e l'ora convalidare il certificato radice nel programma di installazione del servizio App.

- Stato errato fisso restituito in Azure Resource Manager quando una sottoscrizione viene eliminato tale risorse contenute nello spazio dei nomi Microsoft.

### <a name="known-issues-with-the-deployment-process"></a>Problemi noti con il processo di distribuzione

- Errori di convalida del certificato

Alcuni clienti hanno riscontrato problemi quando si forniscono i certificati per il programma di installazione di servizio App, quando si distribuisce in un sistema integrato, a causa di convalida eccessivamente restrittivo nel programma di installazione.  Il programma di installazione di servizio App è stata rilasciata, i clienti devono [scaricare il programma di installazione aggiornato](https://aka.ms/appsvconmasinstaller).  Se si continua a verificarsi problemi di convalida dei certificati con il programma di installazione aggiornato, contattare il supporto tecnico.

- Errore durante il recupero di certificati radice dello Stack di Azure dal sistema integrato.

A causa di un errore in Get-AzureStackRootCert.ps1 ai clienti di esito negativo recuperare il certificato radice dello Stack di Azure quando si esegue lo script in un computer che non dispone di installare il certificato radice.  Lo script è inoltre stato nuovamente rilasciato, risolvere il problema e i clienti di richiesta [scaricare gli script di supporto aggiornato](https://aka.ms/appsvconmashelpers).  Se si continua a verificarsi problemi recuperando il certificato radice con lo script aggiornato, contattare il supporto tecnico.

### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

- Sono presenti problemi noti per l'aggiornamento del servizio App di Azure in Azure Stack Update 1.

### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

- Scambio di slot non funziona

Scambio di slot del sito viene suddiviso in questa versione.  Per ripristinare la funzionalità, completare questi passaggi:

1. Modificare ControllersNSG Network Security Group to **Consenti** le connessioni desktop remoto alle istanze del servizio App controller.  Sostituire con il nome del gruppo di risorse in che è stato distribuito il servizio App AppService.local.

    ```powershell
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

2. Individuare il **CN0-VM** in macchine virtuali nel portale di Azure Stack amministratore e **fare clic su Connetti** per aprire una sessione desktop remoto con l'istanza del controller.  Utilizzare le credenziali specificate durante la distribuzione del servizio App.
3. Avviare **PowerShell come amministratore** ed eseguire lo script seguente

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Chiudere la sessione desktop remoto.
5. Ripristinare ControllersNSG Network Security Group to **Deny** le connessioni desktop remoto alle istanze del servizio App controller.  Sostituire con il nome del gruppo di risorse in che è stato distribuito il servizio App AppService.local.

    ```powershell

        Login-AzureRMAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemi noti per gli amministratori Cloud operativo Azure App Service nello Stack di Azure

Consultare la documentazione nel [note sulla versione di Azure Stack 1802](azure-stack-update-1802.md)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di servizio App di Azure, vedere [Azure App Service nella panoramica di Azure Stack](azure-stack-app-service-overview.md).
- Per ulteriori informazioni su come preparare la distribuzione di servizio App nello Stack di Azure, vedere [prima di iniziare a con il servizio App in Azure Stack](azure-stack-app-service-before-you-get-started.md).
