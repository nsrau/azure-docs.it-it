---
title: Preparare il computer host Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Viene descritto come preparare il computer host Azure Stack Development Kit (ASDK) per l'installazione ASDK.
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
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 36012c023025b8304dfaf9cc63997f600ef6cbe8
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962724"
---
# <a name="prepare-the-asdk-host-computer"></a>Preparare il computer host ASDK
Prima di installare il ASDK nel computer host, è necessario preparare l'host ASDK per l'installazione. Quando il computer host kit di sviluppo è stato preparato, eseguirà l'avvio dal disco rigido CloudBuilder.vhdx macchina virtuale per iniziare la distribuzione ASDK.

## <a name="prepare-the-development-kit-host-computer"></a>Preparare il computer host kit di sviluppo
Prima di installare il ASDK nel computer host, è necessario preparare l'ambiente del computer host ASDK.
1. Accedere come amministratore locale nel computer host kit di sviluppo.
2. Assicurarsi che il file CloudBuilder.vhdx è stato spostato nella radice dell'unità C:\ (C:\CloudBuilder.vhdx).
3. Eseguire lo script seguente per scaricare il file di programma di installazione di development kit (asdk installer.ps1) dal [Stack repository GitHub Azure tools](https://github.com/Azure/AzureStack-Tools) per il **C:\AzureStack_Installer** cartella il computer host kit di sviluppo:

   > [!IMPORTANT]
   > Assicurarsi di scaricare il file asdk installer.ps1 ogni volta che si installa il ASDK. Vengono apportate frequenti modifiche allo script e usare la versione più recente per ogni distribuzione ASDK. Le versioni precedenti dello script potrebbero non funzionare con la versione più recente.

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

4. In una console di PowerShell con privilegi elevata, avviare il **C:\AzureStack_Installer\asdk-installer.ps1** lo script e quindi fare clic su **Preparazione ambiente**.

    ![](media/asdk-prepare-host/1.PNG) 

5. Nel **vhdx selezionare Cloudbuilder** pagina del programma di installazione, individuare e selezionare il **cloudbuilder.vhdx** file che è stato scaricato ed estratto nella [i passaggi precedenti](asdk-download.md). In questa pagina, è anche possibile, facoltativamente, abilitare la **aggiungere i driver** casella di controllo se è necessario aggiungere ulteriori driver al computer host kit di sviluppo. Fare clic su **Avanti**.  

    ![](media/asdk-prepare-host/2.PNG)

6. Nel **impostazioni facoltative** pagina, fornire all'amministratore locale informazioni account per il computer host kit di sviluppo e quindi fare clic su **successivo**.<br><br>Se non si forniscono le credenziali di amministratore locale in questo passaggio, è necessario l'accesso KVM all'host o direct dopo il riavvio del computer come parte della configurazione il kit di sviluppo.

   ![](media/asdk-prepare-host/3.PNG)

    È anche possibile fornire valori per le impostazioni facoltative seguenti:
    - **Computername**: Questa opzione imposta il nome per l'host del kit di sviluppo. Il nome deve essere conforme ai requisiti di FQDN e deve essere una lunghezza o 15 caratteri. Il valore predefinito è un nome casuale generato da Windows.
    - **Configurazione con IP statico**: Imposta la distribuzione da usare un indirizzo IP statico. In caso contrario, quando il programma di installazione riavvia nel cloudbuilder.vhdx, le interfacce di rete vengono configurate con DHCP. Se si sceglie di usare una configurazione con IP statico, verranno visualizzate opzioni aggiuntive in cui è anche necessario:
      - Selezionare una scheda di rete. Assicurarsi che sia possibile connettersi all'adapter prima di fare clic **successivo**.
      - Assicurarsi che l'oggetto visualizzato **indirizzo IP**, **Gateway**, e **DNS** valori siano corretti e quindi fare clic su **Avanti**.
13. Fare clic su **successivo** per avviare il processo di preparazione.
14. Durante la preparazione indica **Completed**, fare clic su **successivo**.

    ![](media/asdk-prepare-host/4.PNG)

15. Fare clic su **Riavvia ora** per avviare il computer host kit di sviluppo di cloudbuilder.vhdx e [continuare il processo di distribuzione](asdk-install.md).

    ![](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>Passaggi successivi
[Installare il ASDK](asdk-install.md)
