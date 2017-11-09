---
title: Distribuire il Kit di sviluppo dello Stack di Azure | Documenti Microsoft
description: Informazioni su come preparare il Kit di sviluppo dello Stack di Azure ed eseguire lo script di PowerShell per distribuirla.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/17/2017
ms.author: erikje
ms.openlocfilehash: b67cabf0ecdb48f137bfcfbce95eee568a1c298d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Distribuire il Kit di sviluppo di Azure Stack

*Si applica a: Azure Stack Development Kit*

Per distribuire il [Kit di sviluppo di Azure Stack](azure-stack-poc.md), è necessario completare i passaggi seguenti:

1. [Scaricare il pacchetto di distribuzione](https://azure.microsoft.com/overview/azure-stack/try/?v=try) per ottenere il Cloudbuilder.vhdx.
2. [Preparare il cloudbuilder.vhdx](#prepare-the-development-kit-host) eseguendo lo script asdk installer.ps1 per configurare il computer in cui si desidera installare il kit di sviluppo (l'host di kit di sviluppo). Dopo questo passaggio, verrà avviato l'host del kit di sviluppo di Cloudbuilder.vhdx.
3. [Distribuire il kit di sviluppo](#deploy-the-development-kit) nell'host di kit di sviluppo.

> [!NOTE]
> Per ottenere risultati ottimali, anche se si desidera utilizzare un ambiente disconnesso dello Stack di Azure, è consigliabile distribuire mentre si è connessi a internet. In questo modo, è possibile attivare la versione di valutazione di Windows Server 2016 in fase di distribuzione.
> 
> 

## <a name="download-and-extract-the-development-kit"></a>Scaricare ed estrarre il kit di sviluppo
1. Prima di iniziare il download, assicurarsi che il computer soddisfi i prerequisiti seguenti:

   * Il computer deve disporre di almeno 60 GB di spazio libero su disco.
   * [.NET framework 4.6 (o versione successiva)](https://aka.ms/r6mkiy) deve essere installato.

2. [Passare alla pagina di introduzione](https://azure.microsoft.com/overview/azure-stack/try/?v=try), fornire i dettagli e fare clic su **Invia**.
3. In **scaricare il software**, fare clic su **Kit di sviluppo di Azure Stack**.
4. Eseguire il file AzureStackDownloader.exe scaricato.
5. Nel **Azure Stack Development Kit Downloader** finestra, seguire i passaggi da 1 a 5.
6. Al termine del download, fare clic su **eseguire** per avviare il MicrosoftAzureStackPOC.exe.
7. Rivedere le informazioni della procedura guidata Self-Extractor schermata Contratto di licenza e quindi fare clic su **Avanti**.
8. Rivedere le informazioni della procedura guidata Self-Extractor schermata informativa sulla Privacy e quindi fare clic su **Avanti**.
9. Selezionare la destinazione per i file estratti, fare clic su **Avanti**.
   * Il valore predefinito è: <drive letter>:\<cartella corrente > \Microsoft Azure Stack
10. Rivedere le informazioni della procedura guidata Self-Extractor schermata di percorso di destinazione e quindi fare clic su **estrarre** per estrarre il CloudBuilder.vhdx (circa 25 GB) e i file ThirdPartyLicenses.rtf. Questo processo richiede parecchio tempo.

> [!NOTE]
> Dopo aver estratto i file, è possibile eliminare i file exe e bin per recuperare spazio sul computer. In alternativa, è possibile spostare questi file in un altro percorso in modo che, se è necessario ridistribuire, è necessario scaricare di nuovo i file.
> 
> 

## <a name="prepare-the-development-kit-host"></a>Preparare l'host del kit di sviluppo
1. Assicurarsi che è possibile fisicamente la connessione all'host del kit di sviluppo, o avere l'accesso da console fisica (ad esempio KVM). È necessario l'accesso dopo il riavvio l'host del kit di sviluppo nel passaggio 13 riportato di seguito.
2. Verificare che l'host del kit di sviluppo soddisfi il [requisiti minimi](azure-stack-deploy.md). È possibile utilizzare il [controllo distribuzione per Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) per verificare i requisiti.
3. Accedere come amministratore locale all'host del kit di sviluppo.
4. Copiare o spostare il file CloudBuilder.vhdx alla radice dell'unità C:\ (C:\CloudBuilder.vhdx).
5. Eseguire lo script seguente per scaricare il file di programma di installazione di kit di sviluppo (asdk installer.ps1) nella cartella c:\AzureStack_Installer nell'host di kit di sviluppo.
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. Aprire una console di PowerShell con privilegi elevata > eseguire lo script C:\AzureStack_Installer\asdk-installer.ps1 > fare clic su **Preparazione ambiente**.
7. Nel **vhdx Cloudbuilder selezionare** pagina del programma di installazione, individuare e selezionare il file cloudbuilder.vhdx scaricato nei passaggi precedenti.
8. Facoltativo: Controllare la **aggiungere driver** casella per specificare una cartella contenente i driver aggiuntivi che desidera che nell'host.
9. Nel **impostazioni facoltative** pagina, specificare l'account amministratore locale per l'host del kit di sviluppo. Se non si forniscono le credenziali, è necessario l'accesso KVM all'host durante il processo di installazione riportata di seguito.
10. Anche nel **impostazioni facoltative** pagina, è possibile impostare quanto segue:
    - **Computername**: questa opzione imposta il nome per l'host del kit di sviluppo. Il nome deve essere conforme ai requisiti di FQDN e deve essere 15 caratteri o lunghezza. Il valore predefinito è un nome casuale generato da Windows.
    - **Fuso orario**: imposta il fuso orario per l'host del kit di sviluppo. Il valore predefinito è (UTC-8:00) Pacifico (Stati Uniti e Canada).
    - **Configurazione con IP statico**: imposta la distribuzione da utilizzare un indirizzo IP statico. In caso contrario, quando il programma di installazione riavvia nel cloudbuilder.vhx, le interfacce di rete vengono configurate con DHCP.
11. Fare clic su **Avanti**.
12. Se si sceglie una configurazione IP statico nel passaggio precedente, è necessario ora:
    - Selezionare una scheda di rete. Verificare che sia possibile connettersi all'adapter prima di scegliere **Avanti**.
    - Assicurarsi che il **indirizzo IP**, **Gateway**, e **DNS** valori siano corretti e quindi fare clic su **Avanti**.
13. Fare clic su **Avanti** per avviare il processo di preparazione.
14. Durante la preparazione indica **completato**, fare clic su **Avanti**.
15. Fare clic su **riavviare ora** per l'avvio di cloudbuilder.vhdx e continuare il processo di distribuzione.

## <a name="deploy-the-development-kit"></a>Distribuire il kit di sviluppo
1. Accedere come amministratore locale per l'host del kit di sviluppo. Utilizzare le credenziali specificate nei passaggi precedenti.

    > [!IMPORTANT]
    > Per le distribuzioni di Azure Active Directory, Stack di Azure richiede l'accesso a Internet, direttamente o tramite un proxy trasparente. La distribuzione supporta esattamente una scheda di rete per la rete. Se si dispone di più schede di rete, assicurarsi che solo uno è attivato (e tutti gli altri sono disabilitati) prima di eseguire lo script di distribuzione nella sezione successiva.
    
2. Aprire una console di PowerShell con privilegi elevata > eseguire lo script \AzureStack_Installer\asdk-installer.ps1 (che può essere in un'unità diversa nel Cloudbuilder.vhdx) > fare clic su **installare**.
3. Nel **tipo** , quindi selezionare **Cloud Azure** o **ADFS**.
    - **Cloud di Azure**: Azure Active Directory è il provider di identità. Utilizzare questo parametro per specificare una directory specifica in cui l'account AAD disponga delle autorizzazioni di amministratore globale. Nome completo di un tenant di Directory di AAD. Ad esempio,. c o m. 
    - **ADFS**: il timbro predefinito servizio di Directory è il provider di identità, l'account predefinito per l'accesso è azurestackadmin@azurestack.local, e la password da utilizzare è quello specificato come parte dell'installazione.
4. In **password dell'amministratore locale**nella **Password** casella, digitare la password di amministratore locale (che deve corrispondere la password di amministratore locale configurato corrente) e quindi fare clic su **Avanti**.
5. Selezionare una scheda di rete da utilizzare per il kit di sviluppo e quindi fare clic su **Avanti**.
6. Selezionare una configurazione di rete statica per la macchina virtuale BGPNAT01 o DHCP.
    - **DHCP** (impostazione predefinita): la macchina virtuale Ottiene la configurazione della rete IP dal server DHCP.
    - **Statico**: usare questa opzione solo se DHCP non è possibile assegnare un indirizzo IP valido per lo Stack di Azure accedere a Internet. Con la lunghezza di subnetmask (ad esempio, 10.0.0.5/24), è necessario specificare un indirizzo IP statico.
7. Facoltativamente, impostare i valori seguenti:
    - **ID VLAN**: imposta l'ID VLAN. Utilizzare questa opzione solo se l'host e AzS BGPNAT01 necessario configurare l'ID VLAN per accedere alla rete fisica (e Internet). 
    - **Server d'inoltro DNS**: un server DNS viene creato come parte della distribuzione di Azure Stack. Per consentire ai computer all'interno della soluzione per la risoluzione dei nomi di fuori l'indicatore, fornire i server di infrastruttura DNS esistente. Il server DNS in timbro inoltra le richieste di risoluzione nome sconosciuto al server.
    - **Server di riferimento ora**: questa richiesta campo imposta il tempo server e deve essere un indirizzo IP. Per trovare l'indirizzo IP di un server, visitare [pool.ntp.org](http:\\pool.ntp.org) o time.windows.com ping. 
8. Fare clic su **Avanti**. 
9. Nel **verifica proprietà scheda di interfaccia di rete** pagina, verrà visualizzato un indicatore di stato. 
    - Se l'indicazione è **non è possibile scaricare un aggiornamento**, seguire le istruzioni nella pagina.
    - Quando verrà visualizzato il numero **completato**, fare clic su **Avanti**.
10. In **riepilogo** pagina, fare clic su **Distribuisci**.
11. Se si utilizza una distribuzione di Azure Active Directory, verrà chiesto di immettere le credenziali dell'account di amministratore globale di Azure Active Directory.
12. Il processo di distribuzione può richiedere alcune ore, durante il quale il sistema viene riavviato automaticamente una volta.
   
   > [!IMPORTANT]
   > Se si desidera monitorare lo stato di distribuzione, accedere come azurestack\AzureStackAdmin. Se si accede come amministratore locale dopo che è connesso il computer al dominio, non verrà visualizzata l'avanzamento della distribuzione. Non rieseguire la distribuzione, invece Accedi come azurestack\AzureStackAdmin verificare che sia in esecuzione.
   > 
   > 
   
    Quando la distribuzione ha esito positivo, nella console di PowerShell vengono visualizzati: **completa: azione "Distribuzione"**.
   
Se la distribuzione non riesce, è possibile utilizzare il seguente script di PowerShell eseguire di nuovo dalla stessa finestra di PowerShell con privilegi elevata:

```powershell
cd c:\CloudDeployment\Setup
.\InstallAzureStackPOC.ps1 -Rerun
```

Questo script verrà riavviato la distribuzione dall'ultima operazione che ha avuto esito positivo.

In alternativa, è possibile [ridistribuire](azure-stack-redeploy.md) da zero.


## <a name="reset-the-password-expiration-to-180-days"></a>Reimpostare la scadenza della password a 180 giorni

Per assicurarsi che la password per l'host del kit di sviluppo senza scadenza troppo presto, seguire questi passaggi dopo aver distribuito:

Per modificare i criteri di scadenza della password da Powershell:
1. Dalla finestra di Powershell, eseguire il comando. Set-ADDefaultDomainPasswordPolicy - MaxPasswordAge 180.00:00:00-azurestack.local identità

Per modificare manualmente i criteri di scadenza delle password:
1. Nell'host del kit di sviluppo, aprire **Gestione criteri di gruppo** e passare a **Gestione criteri di gruppo** – **foresta: azurestack.local** – **domini** – **azurestack.local**.
2. Fare clic destro **criterio dominio predefinito** e fare clic su **modifica**.
3. In Editor Gestione criteri di gruppo, passare a **configurazione Computer** : **criteri** : **impostazioni di Windows** – **le impostazioni di sicurezza**– **Criteri di account** – **criteri Password**.
4. Nel riquadro destro fare doppio clic su **validità massima password**.
5. Nel **validità massima password proprietà** nella finestra di dialogo Modifica il **Password scadrà tra** valore 180, quindi fare clic su **OK**.


## <a name="next-steps"></a>Passaggi successivi

[Installare PowerShell](azure-stack-powershell-configure-quickstart.md)

[Registro dello Stack di Azure con la sottoscrizione di Azure](azure-stack-register.md)

[Connettersi ad Azure Stack](azure-stack-connect-azure-stack.md)

