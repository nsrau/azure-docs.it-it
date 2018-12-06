---
title: Servizio App in Azure Stack update note sulla versione 1 | Microsoft Docs
description: Informazioni sulle caratteristiche di aggiornamento per il servizio App in Azure Stack, i problemi noti e dove scaricare l'aggiornamento.
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
ms.reviewer: sethm
ms.openlocfilehash: 632cf506477bdc6f35c66a473963168f81e22351
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52971896"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Servizio App in Azure Stack update note sulla versione 1

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Queste note sulla versione descrivono i miglioramenti e correzioni in servizio App di Azure in Azure Stack Update 1 e problemi noti. Problemi noti sono suddivisi in problemi correlati direttamente per la distribuzione, il processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]
> Applicare l'aggiornamento 1802 per il sistema integrato Azure Stack o distribuire il kit di sviluppo di Azure Stack più recente prima della distribuzione del servizio App di Azure.
>
>

## <a name="build-reference"></a>Riferimento alla compilazione

Il servizio App in Azure Stack Update 1 numero di build è **69.0.13698.9**

### <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Le nuove distribuzioni del servizio App di Azure in Azure Stack è ora necessario un [certificato con caratteri jolly tre soggetto](azure-stack-app-service-before-you-get-started.md#get-certificates) grazie ai miglioramenti nel modo in cui l'accesso SSO per Kudu è ora gestito nel servizio App di Azure. Il nuovo soggetto  **\*. sso.appservice.\< area geografica\>.\< DomainName\>.\< estensione\>**
>
>

Vedere le [documentazione prima di iniziare a](azure-stack-app-service-before-you-get-started.md) prima di iniziare la distribuzione.

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni

Servizio App di Azure in Azure Stack Update 1 include le correzioni e i miglioramenti seguenti:

- **Elevata disponibilità del servizio App di Azure** -carichi di lavoro di aggiornamento abilitato la 1802 di Stack di Azure per la distribuzione in domini di errore. Pertanto l'infrastruttura del servizio App è in grado di essere a tolleranza di errore, come verrà distribuito nei domini di errore. Per impostazione predefinita tutte le nuove distribuzioni del servizio App di Azure dispone di questa funzionalità tuttavia per le distribuzioni completate precedenti alla 1802 di Azure Stack viene applicato l'aggiornamento si intende il [documentazione di dominio di errore di servizio App](azure-stack-app-service-fault-domain-update.md)

- **Distribuire nella rete virtuale esistente** -i clienti possono ora distribuire il servizio App in Azure Stack all'interno di una rete virtuale esistente. Distribuzione in una rete virtuale esistente consente ai clienti di connettersi al Server SQL e File Server, necessarie per servizio App di Azure su porte private. Durante la distribuzione, i clienti possono scegliere di distribuire in una rete virtuale esistente, tuttavia [deve creare le subnet per l'utilizzo dal servizio App](azure-stack-app-service-before-you-get-started.md#virtual-network) prima della distribuzione.

- Gli aggiornamenti **Tenant del servizio App, amministratore, i portali di funzioni e gli strumenti di Kudu**. Coerente con la versione di SDK portale di Azure Stack.

- **Gli aggiornamenti per gli strumenti e Framework di applicazione seguenti**:
    - Aggiunti **.NET Core 2.0** supportano
    - Aggiunti **Node. js** versioni:
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
    - Aggiunti **NPM** versioni:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Aggiunti **PHP** aggiornamenti:
        - 5.6.32
        - 7.0.26 (x86 e x64)
        - 7.1.12 (x86 e x64)
    - Aggiornata **Git per Windows** a v 2.14.1
    - Aggiornata **Mercurial** a v4.5.0

  - Aggiunta del supporto per **solo HTTPS** funzionalità all'interno di funzionalità dominio personalizzato nel portale Tenant del servizio App. 

  - Aggiunta convalida della connessione di archiviazione nella casella di selezione di un'archiviazione personalizzata per le funzioni di Azure 

#### <a name="fixes"></a>Correzioni

- Quando si crea un pacchetto di distribuzione non in linea, i clienti non riceveranno più un accesso negato messaggio di errore quando si apre la cartella dal programma di installazione del servizio App

- Risolvere i problemi quando si utilizza la funzionalità di domini personalizzati nel portale Tenant del servizio App.

- Evitare che i clienti che usano i nomi degli amministratori riservato durante l'installazione

- Distribuzione del servizio App con abilitata **aggiunti a un dominio** file server di

- Miglioramenti apportati al recupero della radice di Azure Stack in uno script di certificato e ora convalidare il certificato radice nel programma di installazione del servizio App.

- Stato non corretto fisso restituito ad Azure Resource Manager quando una sottoscrizione viene eliminata che le risorse contenute nello spazio dei nomi Microsoft. Web.

### <a name="known-issues-with-the-deployment-process"></a>Problemi noti con il processo di distribuzione

- Errori di convalida del certificato

Alcuni clienti hanno riscontrato problemi quando si forniscono i certificati per il programma di installazione del servizio App durante la distribuzione in un sistema integrato, grazie a una convalida troppo restrittiva nel programma di installazione. È stato rilasciato nuovamente il programma di installazione del servizio App, i clienti dovrebbero [scaricare il programma di installazione aggiornato](https://aka.ms/appsvconmasinstaller). Se si continua a verificarsi problemi di convalida dei certificati con il programma di installazione aggiornato, contattare il supporto tecnico.

- Problema durante il recupero certificato radice di Azure Stack da integrated system.

A causa di un errore nella finestra di Get-AzureStackRootCert.ps1 i clienti a non riuscire a recuperare il certificato radice di Azure Stack durante l'esecuzione dello script in un computer che non è installato il certificato radice. Lo script anche a questo punto è stato rilasciato, risolvere il problema e i clienti di richiesta [scaricare gli script helper aggiornata](https://aka.ms/appsvconmashelpers). Se si continua a verificarsi problemi di recupero di certificati con lo script aggiornato, contattare il supporto tecnico.

### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

- Sono presenti problemi noti per l'aggiornamento del servizio App di Azure in Azure Stack Update 1.

### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

- Scambio di slot non funziona

Lo scambio di slot del sito viene suddiviso in questa versione. Per ripristinare la funzionalità, completare questi passaggi:

1. Modificare ControllersNSG Network Security Group to **Consenti** le connessioni desktop remoto alle istanze di controller del servizio App. Sostituire AppService.local con il nome del gruppo di risorse in che è stato distribuito il servizio App.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

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

2. Selezionare il **CN0-VM** in macchine virtuali nel portale di amministrazione di Azure Stack e **fare clic su Connetti** per aprire una sessione desktop remoto con l'istanza del controller. Usare le credenziali specificate durante la distribuzione del servizio App.
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
5. Ripristinare ControllersNSG Network Security Group to **Deny** le connessioni desktop remoto alle istanze di controller del servizio App. Sostituire AppService.local con il nome del gruppo di risorse in che è stato distribuito il servizio App.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

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

6. I ruoli di lavoro sono in grado di raggiungere il server di file quando il servizio App viene distribuito in una rete virtuale esistente e il file server è disponibile nella rete privata solo.

Se si sceglie di distribuire in una rete virtuale esistente e un indirizzo IP interno per la connessione al file server, è necessario aggiungere una regola di sicurezza in uscita, consentendo il traffico tra la subnet del ruolo di lavoro e il file server SMB. A tale scopo, passare a WorkersNsg nel portale di amministrazione e aggiungere una regola di sicurezza in uscita con le proprietà seguenti:

- Origine: tutti
- Intervallo di porte di origine: *
- Destinazione: Gli indirizzi IP
- Intervallo di indirizzi IP di destinazione: intervallo di indirizzi IP per il file server
- Intervallo di porte di destinazione: 445
- Protocollo: TCP
- Azione: Consenti
- Priorità: 700
- Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemi noti per gli amministratori Cloud funziona nel servizio App di Azure in Azure Stack

Vedere la documentazione nel [note sulla versione 1802 di Azure Stack](azure-stack-update-1802.md)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica del servizio App di Azure, vedere [servizio App di Azure nella panoramica di Azure Stack](azure-stack-app-service-overview.md).
- Per altre informazioni su come preparare la distribuzione del servizio App in Azure Stack, vedere [prima di iniziare con il servizio App in Azure Stack](azure-stack-app-service-before-you-get-started.md).
