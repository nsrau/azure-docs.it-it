---
title: Preparare il computer host di Azure Stack Development Kit (ASDK) | Documenti Microsoft
description: Viene descritto come preparare il computer host di Azure Stack Development Kit (ASDK) per l'installazione ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 5de25f574cb876701ffce74f1dca8c4bb9764157
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="prepare-the-asdk-host-computer"></a>Preparare il computer host ASDK
Prima di installare il ASDK nel computer host, è necessario preparare l'ambiente ASDK per l'installazione. Quando il computer host kit di sviluppo è stato preparato, verrà avviato dal disco rigido CloudBuilder.vhdx macchina virtuale per iniziare la distribuzione ASDK.

## <a name="prepare-the-development-kit-host-computer"></a>Preparare il computer host kit di sviluppo
Prima di installare il ASDK nel computer host, è necessario preparare l'ambiente del computer host ASDK.
1. Accedere come amministratore locale nel computer host kit di sviluppo.
2. Assicurarsi che il file CloudBuilder.vhdx siano stato spostato alla radice dell'unità C:\ (C:\CloudBuilder.vhdx).
3. Eseguire lo script seguente per scaricare il file di programma di installazione di kit di sviluppo (asdk installer.ps1) dal [archivio degli strumenti di Azure Stack GitHub](https://github.com/Azure/AzureStack-Tools) per il **C:\AzureStack_Installer** cartella il computer host kit di sviluppo:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Enforce usage of TLSv1.2 to download from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. In una console di PowerShell con privilegi elevata, avviare il **C:\AzureStack_Installer\asdk-installer.ps1** script e quindi fare clic su **Preparazione ambiente**.

    ![](media/asdk-prepare-host/1.PNG) 

5. Nel **vhdx selezionare Cloudbuilder** pagina del programma di installazione, individuare e selezionare il **cloudbuilder.vhdx** file che è stato scaricato ed estratto [i passaggi precedenti](asdk-download.md). In questa pagina, è anche possibile, facoltativamente, abilitare il **aggiungere i driver** casella di controllo se è necessario aggiungere ulteriori driver al computer host kit di sviluppo. Fare clic su **Avanti**.  

    ![](media/asdk-prepare-host/2.PNG)

6. Nel **impostazioni facoltative** pagina, fornire all'amministratore locale informazioni account per il computer host kit di sviluppo e quindi fare clic su **successivo**. È anche possibile fornire valori per le impostazioni facoltative seguenti:
  - **Computername**: questa opzione imposta il nome per l'host del kit di sviluppo. Il nome deve essere conforme ai requisiti di FQDN e deve essere 15 caratteri o lunghezza. Il valore predefinito è un nome casuale generato da Windows.
  - **Fuso orario**: imposta il fuso orario per l'host del kit di sviluppo. Il valore predefinito è (UTC-8:00) Pacifico (Stati Uniti e Canada).
  - **Configurazione con IP statico**: imposta la distribuzione da utilizzare un indirizzo IP statico. In caso contrario, quando il programma di installazione riavvia nel cloudbuilder.vhx, le interfacce di rete vengono configurate con DHCP.

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > Se non si forniscono le credenziali di amministratore locale in questo passaggio, è necessario il diretta o l'accesso KVM all'host dopo il riavvio del computer come parte dell'impostazione il kit di sviluppo.

7. Se si sceglie una configurazione IP statico nel passaggio precedente, è necessario ora:
    - Selezionare una scheda di rete. Verificare che sia possibile connettersi all'adapter prima di scegliere **Avanti**.
    - Assicurarsi che il **indirizzo IP**, **Gateway**, e **DNS** valori siano corretti e quindi fare clic su **Avanti**.
13. Fare clic su **Avanti** per avviare il processo di preparazione.
14. Durante la preparazione indica **completato**, fare clic su **Avanti**.
15. Fare clic su **riavviare ora** per avviare il computer host kit sviluppo il cloudbuilder.vhdx e [continuare il processo di distribuzione](asdk-install.md).

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>Passaggi successivi
[Installare il ASDK](asdk-install.md)