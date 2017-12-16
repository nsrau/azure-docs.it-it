---
title: Distribuire il Kit di sviluppo dello Stack di Azure | Documenti Microsoft
description: Informazioni su come preparare il Kit di sviluppo dello Stack di Azure ed eseguire lo script di PowerShell per distribuirla.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2017
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 470a45aea253e1e238983527427b600117e413fe
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Distribuire il Kit di sviluppo di Azure Stack

*Si applica a: Azure Stack Development Kit*

Per distribuire il [Kit di sviluppo di Azure Stack](azure-stack-poc.md), è necessario completare i passaggi seguenti:

1. [Scaricare il pacchetto di distribuzione](https://azure.microsoft.com/overview/azure-stack/try/?v=try) per ottenere il Cloudbuilder.vhdx.
2. Preparare cloudbuilder.vhdx per configurare il computer in cui si desidera installare il kit di sviluppo (l'host di kit di sviluppo). Dopo questo passaggio, verrà avviato l'host del kit di sviluppo di Cloudbuilder.vhdx.
3. Distribuire il kit di sviluppo nell'host di kit di sviluppo.

> [!NOTE]
> Per ottenere risultati ottimali, anche se si desidera utilizzare un ambiente disconnesso dello Stack di Azure, è consigliabile distribuire mentre si è connessi a internet. In questo modo, è possibile attivare la versione di valutazione di Windows Server 2016 inclusa con l'installazione del kit di sviluppo in fase di distribuzione.

## <a name="download-and-extract-the-development-kit"></a>Scaricare ed estrarre il kit di sviluppo
1. Prima di iniziare il download, assicurarsi che il computer soddisfi i prerequisiti seguenti:

  - Il computer deve disporre almeno 60 GB di spazio su disco disponibile in quattro separati, identiche unità disco rigido locali inoltre per il disco del sistema operativo.
  - [.NET framework 4.6 (o versione successiva)](https://aka.ms/r6mkiy) deve essere installato.

2. [Passare alla pagina di introduzione](https://azure.microsoft.com/overview/azure-stack/try/?v=try) in cui può scaricare il Kit di sviluppo dello Stack di Azure, fornire i dettagli e quindi fare clic su **Invia**.
3. Scaricare ed eseguire il [controllo distribuzione per il Kit di sviluppo di Azure Stack](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) script del controllo prerequisiti. Questo script autonomo viene sottoposto a controlli dei prerequisiti eseguiti dal programma di installazione per il Kit di sviluppo dello Stack di Azure. Fornisce un modo per verificare che vengano soddisfatti i requisiti hardware e software, prima di scaricare il pacchetto più grande per il Kit di sviluppo dello Stack di Azure.
4. In **scaricare il software**, fare clic su **Kit di sviluppo di Azure Stack**.

  > [!NOTE]
  > Il download ASDK (AzureStackDevelopmentKit.exe) è di 10GB approximiately da solo. Se si sceglie di scaricare anche il file ISO di Windows Server 2016 evaluation versione, le dimensioni di download aumentano a circa 17GB. È possibile utilizzare tale file ISO per creare e aggiungere un'immagine di macchina virtuale di Windows Server 2016 a Azure Marketplace dello Stack al termine dell'installazione ASDK. Si noti che questa immagine di valutazione di Windows Server 2016 può essere utilizzata solo con la ASDK ed è soggetta alle condizioni di licenza ASDK.

5. Al termine del download, fare clic su **eseguire** per avviare il Self-extractor ASDK (AzureStackDevelopmentKit.exe).
6. Leggere e accettare il contratto di licenza visualizzati il **contratto di licenza** pagina della procedura guidata di Self-Extractor e quindi fare clic su **Avanti**.
7. Esaminare le informazioni di istruzione sulla privacy visualizzate nella **nota importante** pagina della procedura guidata di Self-Extractor e quindi fare clic su **Avanti**.
8. Selezionare il percorso di file di installazione di Azure Stack da estrarre attivato il **Seleziona posizione di destinazione** pagina della procedura guidata di Self-Extractor e quindi fare clic su **Avanti**. Il percorso predefinito è *cartella corrente*\Azure Kit di sviluppo dello Stack. 
9. Esaminare il riepilogo sul percorso di destinazione di **pronto per l'estrazione** pagina della procedura guidata di Self-Extractor e quindi fare clic su **estrarre** per estrarre CloudBuilder.vhdx (circa 25 GB) e File ThirdPartyLicenses.rtf. Questo processo richiede parecchio tempo.
10. Copiare o spostare il file CloudBuilder.vhdx alla radice dell'unità C:\ (C:\CloudBuilder.vhdx) nel computer host ASDK.

> [!NOTE]
> Dopo aver estratto i file, è possibile eliminare il. File EXE e. File della cartella BIN per recuperare spazio su disco rigido. In alternativa, è possibile eseguire il backup di questi file in modo che non è necessario scaricare di nuovo i file se è necessario ridistribuire il ASDK.

## <a name="deploy-the-asdk-using-a-guided-experience"></a>Distribuire il ASDK utilizzando un'esperienza interattiva
Il ASDK può essere distribuito utilizzando un'interfaccia utente grafica (GUI) fornita da scaricare ed eseguire lo script di PowerShell asdk installer.ps1.

> [!NOTE]
> L'interfaccia utente di installazione per il Kit di sviluppo dello Stack di Azure è uno script di origine aperto basato su WCF e PowerShell.

### <a name="prepare-the-development-kit-host-using-a-guided-user-experience"></a>Preparare l'host del kit di sviluppo utilizzando un'esperienza interattiva
Prima di installare il ASDK nel computer host, è necessario preparare l'ambiente di ASDK.
1. Accedere come amministratore locale nel computer host ASDK.
2. Assicurarsi che il file CloudBuilder.vhdx siano stato spostato alla radice dell'unità C:\ (C:\CloudBuilder.vhdx).
3. Eseguire lo script seguente per scaricare il file di programma di installazione di kit di sviluppo (asdk installer.ps1) dal [archivio degli strumenti di Azure Stack GitHub](https://github.com/Azure/AzureStack-Tools) per il **C:\AzureStack_Installer** cartella il computer host kit di sviluppo:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. In una console di PowerShell con privilegi elevata, avviare il **C:\AzureStack_Installer\asdk-installer.ps1** script e quindi fare clic su **Preparazione ambiente**.
5. Nel **selezionare Cloudbuilder vhdx** pagina del programma di installazione, individuare e selezionare il **cloudbuilder.vhdx** file scaricati ed estratti nei passaggi precedenti. In questa pagina è possibile anche, facoltativamente, abilitare il **aggiungere driver** casella di controllo se è necessario aggiungere ulteriori driver al computer host del kit di sviluppo.  
6. Nel **impostazioni facoltative** pagina, specificare l'account amministratore locale per il computer host kit di sviluppo. 

  > [!IMPORTANT]
  > Se non si forniscono le credenziali, è necessario il diretta o l'accesso KVM all'host dopo il riavvio del computer come parte dell'impostazione il kit di sviluppo.

7. È anche possibile specificare queste impostazioni facoltative nel **impostazioni facoltative** pagina:
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


### <a name="deploy-the-development-kit-using-a-guided-experience"></a>Distribuire il kit di sviluppo utilizzando un'esperienza interattiva
Dopo aver preparato il computer host ASDK, il ASDK può essere distribuito nell'immagine CloudBuilder.vhdx usando la procedura seguente. 
1. Dopo che il computer host viene avviato correttamente nell'immagine CloudBuilder.vhdx, accedere con le credenziali di amministratore specificate nei passaggi precedenti. 
2. Aprire una console di PowerShell con privilegi elevata ed eseguire il **\AzureStack_Installer\asdk-installer.ps1** script (che potrebbero essere in un'unità diversa nell'immagine CloudBuilder.vhdx). Fare clic su **Installa**.
3. Nel **tipo** casella di riepilogo a discesa, selezionare **Cloud Azure** o **ADFS**.
    - **Cloud di Azure**: consente di configurare Azure Active Directory (Azure AD) come provider di identità. Per utilizzare questa opzione, è necessario una connessione internet, il nome completo di Azure Active Directory tenant di directory sotto forma di *domainname*. c o m o Azure Active Directory verifica le credenziali di amministratore globale e nome di dominio personalizzato per il directory specificata. 
    - **AD FS**: l'indicatore di valore predefinito verrà utilizzato il servizio Directory come provider di identità. L'account predefinito per l'accesso è azurestackadmin@azurestack.local, e la password da utilizzare è quello specificato come parte dell'installazione.
4. In **password dell'amministratore locale**nella **Password** casella, digitare la password di amministratore locale (che deve corrispondere la password di amministratore locale configurato corrente) e quindi fare clic su **Avanti**.
5. Selezionare una scheda di rete da utilizzare per il kit di sviluppo e quindi fare clic su **Avanti**.
6. Selezionare una configurazione di rete statica per la macchina virtuale BGPNAT01 o DHCP.
    - **DHCP** (impostazione predefinita): la macchina virtuale Ottiene la configurazione della rete IP dal server DHCP.
    - **Statico**: usare questa opzione solo se DHCP non è possibile assegnare un indirizzo IP valido per lo Stack di Azure accedere a Internet. Con la lunghezza subnetmask nel formato CIDR (ad esempio, 10.0.0.5/24), è necessario specificare un indirizzo IP statico.
7. Facoltativamente, impostare i valori seguenti:
    - **ID VLAN**: imposta l'ID VLAN. Utilizzare questa opzione solo se l'host e AzS BGPNAT01 necessario configurare l'ID VLAN per accedere alla rete fisica (e internet). 
    - **Server d'inoltro DNS**: un server DNS viene creato come parte della distribuzione di Azure Stack. Per consentire ai computer all'interno della soluzione per la risoluzione dei nomi di fuori l'indicatore, fornire i server di infrastruttura DNS esistente. Il server DNS in timbro inoltra le richieste di risoluzione nome sconosciuto al server.
    - **Server di riferimento ora**: questa richiesta campo imposta il tempo server da utilizzare con il kit di sviluppo. Questo parametro deve essere fornito come un indirizzo IP del server ora valido. I nomi dei server non sono supportati.
  
  > [!TIP]
  > Per trovare l'indirizzo IP di un server, visitare [pool.ntp.org](http:\\pool.ntp.org) o time.windows.com ping. 
  
8. Fare clic su **Avanti**. 
9. Nel **verifica proprietà scheda di interfaccia di rete** pagina, verrà visualizzato un indicatore di stato. 
    - Se l'indicazione è **non è possibile scaricare un aggiornamento**, seguire le istruzioni nella pagina.
    - Quando verrà visualizzato il numero **completato**, fare clic su **Avanti**.
10. In **riepilogo** pagina, fare clic su **Distribuisci**. È inoltre possibile copiare i comandi di installazione di PowerShell che verranno utilizzati per installare il kit di sviluppo.
11. Se si utilizza una distribuzione di Azure AD, verrà chiesto di immettere le credenziali dell'account di amministratore globale di Azure AD alcuni minuti dopo l'avvio dell'installazione.
12. Il processo di distribuzione può richiedere alcune ore, durante il quale il sistema viene riavviato automaticamente una volta. Quando la distribuzione ha esito positivo, nella console di PowerShell vengono visualizzati: **completa: azione "Distribuzione"**. Se la distribuzione non riesce, puoi [ridistribuire](azure-stack-redeploy.md) da zero o utilizzare i seguenti comandi di PowerShell, dalla stessa finestra di PowerShell con privilegi elevata, riavviare la distribuzione dall'ultimo passaggio ha esito positivo:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

   > [!IMPORTANT]
   > Se si desidera monitorare lo stato di distribuzione, accedere come azurestack\AzureStackAdmin quando viene riavviato l'host del kit di sviluppo durante l'installazione. Se si accede come amministratore locale dopo che è connesso il computer al dominio, non verrà visualizzata l'avanzamento della distribuzione. Non rieseguire la distribuzione, invece Accedi come azurestack\AzureStackAdmin verificare che sia in esecuzione.
   

## <a name="deploy-the-asdk-using-powershell"></a>Distribuire il ASDK tramite PowerShell
I passaggi precedenti esplorato il ASDK utilizzando un'esperienza interattiva di distribuzione. In alternativa, è possibile utilizzare PowerShell per distribuire il ASDK nell'host di kit di sviluppo seguendo i passaggi descritti in questa sezione.

### <a name="configure-the-asdk-host-computer-to-boot-from-cloudbuildervhdx"></a>Configurare il computer host ASDK per l'avvio da CloudBuilder.vhdx
Questi comandi configurerà il computer host ASDK per l'avvio dallo scaricati ed estratti Stack Azure virtual harddisk (CloudBuilder.vhdx). Dopo aver completato questi passaggi, riavviare il computer host ASDK.

1. Avviare un prompt dei comandi come amministratore.
2. Eseguire `bcdedit /copy {current} /d "Azure Stack"`
3. Copia (CTRL + C), il valore CLSID restituito, incluse le {obbligatorio} ' s. Questo valore è detto {CLSID} e dovrà essere incollato nel (CTRL + V o destro) negli altri passaggi.
4. Eseguire `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
5. Eseguire `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
6. Eseguire `bcdedit /set {CLSID} detecthal on` 
7. Eseguire `bcdedit /default {CLSID}`
8. Per verificare le impostazioni di avvio, eseguire `bcdedit`. 
9. Verificare che il CloudBuilder.vhdx file è stato spostato nella radice dell'unità C:\ (C:\CloudBuilder.vhdx) e riavviare il computer host kit di sviluppo. Quando viene riavviato l'host ASDK ora deve essere predefinito per l'avvio dalla macchina virtuale CloudBuilder.vhdx. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Preparare l'host del kit di sviluppo con PowerShell 
Dopo che il computer host kit di sviluppo si avvia correttamente nell'immagine CloudBuilder.vhdx, è possibile aprire una console di PowerShell con privilegi elevata ed eseguire i comandi in questa sezione per distribuire il ASDK nell'host di kit di sviluppo.

> [!IMPORTANT] 
> Installazione ASDK supporta esattamente una scheda di interfaccia di rete (NIC) per la rete. Se si dispone di più schede di rete, assicurarsi che solo uno è attivato (e tutti gli altri sono disabilitati) prima di eseguire lo script di distribuzione.

È possibile distribuire Azure Stack con Azure Active Directory o ADFS come provider di identità. Stack di Azure, i provider di risorse e altre applicazioni funzionano con entrambe. Per ulteriori informazioni sugli elementi supportati con AD FS nello Stack di Azure, vedere il [funzionalità e i concetti principali](.\azure-stack-key-features.md) articolo.

> [!TIP]
> Se non si specifica alcun parametro di installazione (vedere i parametri facoltativi InstallAzureStackPOC.ps1 ed esempi riportati di seguito), verrà richiesto per i parametri obbligatori.

### <a name="deploy-azure-stack-using-azure-ad"></a>Distribuire Azure Stack mediante Azure AD 
Per distribuire Azure Stack **mediante Azure AD come provider di identità**, è necessario disporre della connettività internet, direttamente o tramite un proxy trasparente. Eseguire i seguenti comandi di PowerShell per distribuire il kit di sviluppo mediante Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

  Alcuni minuti in installazione ASDK che verrà richiesto per le credenziali di Azure AD. È necessario fornire credenziali di amministratore globale per il tenant di Azure AD. 

### <a name="deploy-azure-stack-using-ad-fs"></a>Distribuire Azure Stack con ADFS 
Per distribuire il kit di sviluppo **con ADFS come provider di identità**, eseguire i comandi di PowerShell seguenti (è sufficiente aggiungere il parametro - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

Nelle distribuzioni di AD FS, l'indicatore di valore predefinito del servizio di Directory viene usato come provider di identità. L'account predefinito per l'accesso è azurestackadmin@azurestack.local, e la password verrà impostata su fornite come parte dei comandi di installazione di PowerShell.

Il processo di distribuzione può richiedere alcune ore, durante il quale il sistema viene riavviato automaticamente una volta. Quando la distribuzione ha esito positivo, nella console di PowerShell vengono visualizzati: **completa: azione "Distribuzione"**. Se la distribuzione non riesce, è possibile provare a eseguire di nuovo lo script usando eseguire nuovamente il parametro-. In alternativa, è possibile [ridistribuire ASDK](.\azure-stack-redeploy.md) da zero.
> [!IMPORTANT]
> Se si desidera monitorare lo stato di distribuzione dopo il riavvio dell'host ASDK, è necessario accedere come AzureStack\AzureStackAdmin. Se si accede come amministratore locale dopo che il computer host viene riavviato (e aggiunti al dominio azurestack.local), non verrà visualizzata l'avanzamento della distribuzione. Non rieseguire la distribuzione, invece Accedi come azurestack verificare che sia in esecuzione.
>

#### <a name="azure-ad-deployment-script-examples"></a>Esempi di script di distribuzione AD Azure
È possibile generare script l'intera distribuzione di Azure AD. Di seguito sono riportati alcuni esempi commentati che includono alcuni parametri facoltativi.

Se l'identità di Azure AD è associato solo **uno** directory di Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Se l'identità di Azure AD è associata a **maggiore di uno** directory di Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Se l'ambiente **non** hanno DHCP è abilitato, è necessario includere i seguenti parametri aggiuntivi per una delle opzioni sopra (utilizzo di esempio forniti): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Parametri facoltativi ASDK InstallAzureStackPOC.ps1
|Parametro|Obbligatorio/Facoltativo|Descrizione|
|-----|-----|-----|
|AdminPassword|Obbligatorio|Imposta l'account amministratore locale e tutti gli altri account utente in tutte le macchine virtuali create come parte della distribuzione kit di sviluppo. Questa password deve corrispondere alla password di amministratore locale corrente nell'host.|
|InfraAzureDirectoryTenantName|Obbligatorio|Imposta la directory del tenant. Utilizzare questo parametro per specificare una directory specifica in cui l'account AAD disponga delle autorizzazioni per la gestione di più directory. Nome di un Tenant di Directory nel formato completo. c o m o Azure Active Directory il nome di dominio personalizzato verificato.|
|Acquisisca|Obbligatorio|Utilizzare questo parametro per specificare un server di tempo specifico. Questo parametro deve essere fornito come un indirizzo IP del server ora valido. I nomi dei server non sono supportati.|
|InfraAzureDirectoryTenantAdminCredential|Facoltativo|Imposta il nome utente di Azure Active Directory e la password. Le credenziali di Azure devono essere ID. Org|
|InfraAzureEnvironment|Facoltativo|Selezionare l'ambiente di Azure con cui si desidera registrare la distribuzione di Azure Stack. Le opzioni includono pubblica Azure, Azure - Cina, Azure - governo.|
|DNSForwarder|Facoltativo|Un server DNS viene creato come parte della distribuzione di Azure Stack. Per consentire ai computer all'interno della soluzione per la risoluzione dei nomi di fuori l'indicatore, fornire i server di infrastruttura DNS esistente. Il server DNS in timbro inoltra le richieste di risoluzione nome sconosciuto al server.|
|NatIPv4Address|Richiesta per il supporto DHCP NAT|Imposta un indirizzo IP statico per definiti BGPNAT01. Usare questo parametro solo se DHCP non riesce ad assegnare un indirizzo IP valido per accedere a Internet.|
|NatIPv4Subnet|Richiesta per il supporto DHCP NAT|Prefisso di Subnet IP utilizzato per DHCP sul supporto NAT. Usare questo parametro solo se DHCP non riesce ad assegnare un indirizzo IP valido per accedere a Internet.|
|PublicVlanId|Facoltativo|Imposta l'ID VLAN. Utilizzare questo parametro solo se l'host e definiti BGPNAT01 necessario configurare l'ID VLAN per accedere alla rete fisica (e Internet). Ad esempio,.\InstallAzureStackPOC.ps1-Verbose - PublicVLan 305|
|Riesegui|Facoltativo|Utilizzare questo flag per rieseguire la distribuzione. Tutti gli input precedenti viene utilizzato. Dati reimmettere forniti in precedenza non sono supportati perché più valori univoci vengono generati e utilizzati per la distribuzione.|

## <a name="activate-the-administrator-and-tenant-portals"></a>Attivare i portali tenant e di amministratore
Dopo le distribuzioni che usano Azure AD, è necessario attivare entrambi i Azure Stack amministratore portali tenant e. Questa attivazione consente che il portale di Azure Stack e Azure Resource Manager le autorizzazioni corrette (elencate nella pagina di consenso) per tutti gli utenti della directory.

- Per il portale dell'amministratore, passare a https://adminportal.local.azurestack.external/guest/signup, leggere le informazioni e quindi fare clic su **Accept**. Dopo avere accettato, è possibile aggiungere gli amministratori del servizio non sono anche amministratori di tenant di directory.

- Per il portale tenant, passare a https://portal.local.azurestack.external/guest/signup, leggere le informazioni e quindi fare clic su **Accept**. Dopo avere accettato, gli utenti nella directory possono accedere al portale tenant. 

> [!NOTE] 
> Se non sono attivati i portali, solo l'amministratore di directory può accedere e utilizzare i portali. Se qualsiasi altro utente accede, essi verrà visualizzato un errore che informa che l'amministratore non ha concesso le autorizzazioni ad altri utenti. Quando l'amministratore in modo nativo appartiene alla directory registrato in Azure Stack, la directory di Azure Stack deve essere aggiunto all'URL di attivazione. Ad esempio, se Azure Stack è registrato per fabrikam.onmicrosoft.com e l'utente amministratore è admin@contoso.com, passare a https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com per attivare il portale. 

## <a name="reset-the-password-expiration-policy"></a>Reimpostare i criteri di scadenza password 
Per assicurarsi che la password per l'host del kit di sviluppo non scada prima la fine del periodo di valutazione, seguire questi passaggi dopo aver distribuito il ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Per modificare i criteri di scadenza della password da Powershell:
In una console di Powershell con privilegi elevata, eseguire il comando:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Per modificare manualmente i criteri di scadenza delle password:
1. Nell'host del kit di sviluppo, aprire **Gestione criteri di gruppo** e passare a **Gestione criteri di gruppo** – **foresta: azurestack.local** – **domini** – **azurestack.local**.
2. Fare clic destro **criterio dominio predefinito** e fare clic su **modifica**.
3. In Editor Gestione criteri di gruppo, passare a **configurazione Computer** : **criteri** : **impostazioni di Windows** – **le impostazioni di sicurezza**– **Criteri di account** – **criteri Password**.
4. Nel riquadro destro fare doppio clic su **validità massima password**.
5. Nel **validità massima password proprietà** nella finestra di dialogo Modifica il **Password scadrà tra** valore 180, quindi fare clic su **OK**.


## <a name="next-steps"></a>Passaggi successivi

[Connettersi ad Azure Stack](azure-stack-connect-azure-stack.md)

[Il programma di installazione di PowerShell per gli ambienti Azure Stack](azure-stack-powershell-configure-quickstart.md)

[Registro dello Stack di Azure con la sottoscrizione di Azure](azure-stack-register.md)



