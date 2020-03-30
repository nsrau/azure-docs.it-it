---
title: Configurare un'appliance di Azure Migrate con uno scriptSet up an Azure Migrate appliance with a script
description: Informazioni su come configurare un'appliance di Azure Migrate con uno script
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337688"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurare un'appliance con uno script

Questo articolo descrive come configurare [l'appliance di Azure Migrate](deploy-appliance.md) usando uno script del programma di installazione di PowerShell.This article describes how to set up the Azure Migrate appliance using a PowerShell installer script.

Lo script fornisce:
- Un'alternativa alla configurazione dell'appliance tramite un modello OVA, per la valutazione e la migrazione senza agenti delle macchine virtuali VMware.
- Un'alternativa alla configurazione dell'appliance usando un modello di disco rigido virtuale, per la valutazione e la migrazione delle macchine virtuali Hyper-V.
- Per la valutazione dei server fisici (o delle macchine virtuali di cui si desidera eseguire la migrazione come server fisici), lo script è l'unico metodo per configurare l'appliance.

## <a name="prerequisites"></a>Prerequisiti

Lo script configura l'appliance di Azure Migrate in un computer fisico o in una macchina virtuale esistente.

- Il computer che fungerà da appliance deve eseguire Windows Server 2016, con 32 GB di memoria, otto vCPU, circa 80 GB di spazio di archiviazione su disco e un commutatore virtuale esterno. Richiede un indirizzo IP statico o dinamico e l'accesso a Internet.
- Prima di distribuire l'appliance, esaminare i requisiti dettagliati dell'appliance per [le macchine virtuali VMware,](migrate-appliance.md#appliance---vmware)le [macchine virtuali Hyper-V](migrate-appliance.md#appliance---hyper-v)e i [server fisici.](migrate-appliance.md#appliance---physical)
- Non eseguire lo script in un'appliance di Azure Migrate esistente.


## <a name="download-the-script"></a>Scarica lo script

1. Individuare la macchina/VM che fungerà da appliance di Azure Migrate.Locate the machine/VM that will act as the Azure Migrate appliance.
2. Nel computer eseguire le operazioni seguenti:

    - Per utilizzare l'appliance con macchine virtuali VMware o macchine virtuali Hyper-V, [scaricare](https://go.microsoft.com/fwlink/?linkid=2105112) la cartella compressa contenente lo script del programma di installazione e gli MSI.
    - Per usare l'appliance con server fisici, scaricare lo script dal portale di Azure Migrate, come descritto in questa [esercitazione.](tutorial-assess-physical.md#set-up-the-appliance)

## <a name="verify-file-security"></a>Verificare la sicurezza dei file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Verificare che i valori hash generati corrispondano a queste impostazioni (per la versione più recente dell'appliance):

    **Algoritmo** | **Valore hash**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, inclusi il servizio di attivazione di Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo riscrivibile di IIS. [Scopri di più](https://www.microsoft.com/download/details.aspx?id=7435).
- Aggiorna una chiave del Registro di sistema (HKLM), con impostazioni permanenti per Azure Migrate.
- Crea i file di registro e di configurazione come segue:
    - **File di configurazione**: %ProgramData%\Microsoft Azure\Config
    - **File di log**: %ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella sul computer che ospiterà l'appliance.
2. Avviare PowerShell nel computer, con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella contenente il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script AzureMigrateInstaller.ps1 come segue:Run the script **AzureMigrateInstaller.ps1** as follows:

    - Per VMware: 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - Per Hyper-V:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - Per i server fisici:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. Dopo che lo script viene eseguito correttamente, avvia l'applicazione Web dell'appliance in modo che sia possibile configurare l'appliance. Se si verificano problemi, è possibile visualizzare i log di script in C<em>AzureMigrateScenarioInstaller_:</em>

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato l'appliance utilizzando lo script, seguire queste istruzioni:

- Configurare l'appliance per [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Configurare l'appliance per [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Impostare l'appliance per i [server fisici.](how-to-set-up-appliance-physical.md)