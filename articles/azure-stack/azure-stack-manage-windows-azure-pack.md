---
title: Gestire le macchine virtuali di Windows Azure Pack dallo Stack di Azure | Documenti Microsoft
description: Informazioni su come gestire le macchine virtuali di Windows Azure Pack (WAP) dal portale per gli utenti nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: walterov
manager: byronr
editor: 
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: walterov
ms.openlocfilehash: b07a18055d149e20cd605a892063eccecf3df8a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Gestire le macchine virtuali di Windows Azure Pack dallo Stack di Azure

*Si applica a: Azure Stack Development Kit*

Nel Kit di sviluppo dello Stack di Azure, è possibile abilitare l'accesso dal portale per gli utenti dello Stack di Azure alle macchine virtuali in esecuzione in Windows Azure Pack dei tenant. Gli utenti possono utilizzare il portale di Azure Stack per gestire le macchine virtuali IaaS esistenti e le reti virtuali. Queste risorse vengono rese disponibili in Windows Azure Pack tramite i componenti di Service Provider Foundation (SPF) e Virtual Machine Manager (VMM) sottostanti. In particolare, gli utenti possono:

* Selezionare le risorse
* Esaminare i valori di configurazione
* Arrestare o avviare una macchina virtuale
* Connettersi a una macchina virtuale tramite Remote Desktop Protocol (RDP)
* Creare e gestire i checkpoint
* Eliminare le macchine virtuali e reti virtuali

Questa funzionalità viene fornita per il connettore di Windows Azure Pack per lo Stack di Azure (anteprima). In questo articolo viene illustrato come configurare il connettore per un ambiente di Azure Stack a nodo singolo.

Per questa versione di anteprima del connettore, tenere presente quanto segue:
* Utilizzare il connettore di Windows Azure Pack solo in ambienti di test (per lo Stack di Azure e Windows Azure Pack) e non nelle distribuzioni di produzione.
* È necessario disporre di Windows Azure Pack aggiornamento CUMULATIVO 9.1 o versione successiva e per System Center e VMM UR 9 o versione successiva.
* I componenti VMM e SPF possono essere System Center 2012 R2 o System Center 2016.
* In entrambi Stack di Azure e in Windows Azure Pack, è necessario eseguire passaggi di configurazione.
* Le istruzioni valide in ambienti non - Cloud Platform System CPS.
* Per esaminare i problemi noti, vedere [la risoluzione dei problemi di Microsoft Azure Stack](azure-stack-troubleshooting.md).


## <a name="architecture"></a>Architecture
Il diagramma seguente mostra i componenti principali di Windows Azure Pack Connector.

![I componenti di Windows Azure Pack connettore](media/azure-stack-manage-wap/image1.png)

Tenere presente i seguenti dettagli:
* Il portale di Azure Stack utente accede a informazioni della risorsa da entrambi i cloud (Stack di Azure e Windows Azure Pack).
* L'utente deve disporre di un account valido in entrambi gli ambienti.
* Il portale utenti Azure Stack deve avere accesso alla rete per i componenti in esecuzione in Windows Azure Pack.
* Nel **WAP** sezione del diagramma, è possibile visualizzare i nuovi moduli di Windows Azure Pack connettore (estensione WAP e connettore) ed esistente Windows Azure Pack API Tenant con componenti SPF e VMM.


## <a name="identity-management"></a>Gestione delle identità
L'API Tenant di Windows Azure Pack devono considerare attendibile il Azure Stack servizio token di sicurezza (STS).

Quando un utente esegue un'azione tramite il portale di Azure Stack che fa riferimento alle risorse di Windows Azure Pack, il portale utilizza l'API Tenant di Windows Azure Pack. Pertanto, il token di autenticazione utente fornito deve provenire da un servizio token di sicurezza. Vedere il diagramma seguente:

![Diagramma di autenticazione di Windows Azure Pack connettore](media/azure-stack-manage-wap/image2.png)

Nell'ambiente di kit di sviluppo, Windows Azure Pack e Stack di Azure sono i provider di identità indipendenti. Gli utenti che accedono a entrambi gli ambienti dal portale di Azure Stack devono avere lo stesso nome di un nome principale (UPN) in entrambi i provider di identità. Ad esempio, l'account  *azurestackadmin@azurestack.local*  devono essere presenti anche nel servizio token di sicurezza per Windows Azure Pack. In ADFS non è configurato per supportare le relazioni di trust in uscita, si stabilirà trust dai componenti di Windows Azure Pack (API Tenant) per l'istanza di Azure Stack di ADFS.

## <a name="prerequisites"></a>Prerequisiti

### <a name="download-the-windows-azure-pack-connector"></a>Scaricare il connettore di Windows Azure Pack
Nel [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), scaricare il file .exe ed estrarre i file nel computer locale. In un secondo momento, copiare il contenuto in un computer in cui può accedere l'ambiente di Windows Azure Pack.

### <a name="deployment-option-requirement"></a>Requisiti di opzione di distribuzione
Per l'integrazione con Windows Azure Pack, è possibile distribuire Azure Stack utilizzando l'opzione di ADFS o Azure Active Directory.

### <a name="connectivity-requirements"></a>Requisiti di connettività
1. Dal computer in cui si accedere al portale utenti Azure Stack, assicurarsi che il portale tenant di Windows Azure Pack per accedere tramite il web browser.
2. La macchina virtuale AzS WASP01 nello Stack di Azure deve essere in grado di connettersi al computer del portale tenant Windows Azure Pack. Utilizzare Ping.exe per verificare la connettività di rete. 
3.  È necessario disporre di certificati validi per i nuovi servizi del connettore. Questi certificati SSL devono essere emesso da un'autorità di certificazione (CA). Non è possibile utilizzare certificati autofirmati. I certificati SSL devono essere considerato attendibile da Azure Stack (in particolare la macchina virtuale AzS WASP01) e qualsiasi altro computer che il tenant può utilizzare per accedere al portale utenti Azure Stack.
 
    >[!NOTE]
    Poiché AzS WASP01 viene eseguito Windows Server con l'opzione di installazione Server Core, è possibile utilizzare uno strumento da riga di comando, ad esempio Certutil.ext per importare il certificato. Ad esempio, è possibile copiare il file con estensione CER in c:\temp su AzS WASP01 e quindi eseguire il comando **certutil - addstore "CA", "c:\temp\certname.cer"**.

4.  Per stabilire la connettività RDP per le macchine virtuali tenant Windows Azure Pack tramite il portale di Azure Stack, l'ambiente di Windows Azure Pack deve consentire il traffico di Desktop remoto per le macchine virtuali tenant.
5.  Per la connettività tra macchine virtuali tenant di Azure Stack e le macchine virtuali tenant Windows Azure Pack, gli indirizzi IP esterni devono essere instradabili tra due ambienti. Questo tipo di connettività può anche includere l'associazione di un server DNS per risolvere i nomi di macchina virtuale tra gli ambienti.

### <a name="iis-requirements"></a>Requisiti di IIS
Il computer che ospita il portale tenant di Windows Azure Pack (che può essere un host fisico, una macchina virtuale o più macchine virtuali) deve avere installato l'estensione IIS di riscrittura degli URL. Se non è già installato, è possibile scaricarlo da [qui](https://www.iis.net/downloads/microsoft/url-rewrite). Se più macchine virtuali di ospitare il portale tenant, è possibile installare l'estensione in ogni macchina virtuale.

## <a name="configure-azure-stack"></a>Configurare Azure Stack
Prima di configurare il connettore di Windows Azure Pack, è necessario abilitare la modalità multi-cloud nel portale utenti Azure Stack. Questa modalità consente agli utenti di selezionare quali cloud di accedere alle risorse.

Per abilitare la modalità multi-cloud, è necessario eseguire lo script Add-AzurePackConnector.ps1 dopo la distribuzione di Azure Stack. Nella tabella seguente vengono descritti i parametri di script.


|  Parametro | DESCRIZIONE | Esempio |   
| -------- | ------------- | ------- |  
| AzurePackClouds | URI dei connettori Windows Azure Pack. Gli URI devono corrispondere ai portali tenant Windows Azure Pack. | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (Per impostazione predefinita, il valore della porta è 40005). |  
| AzureStackCloudName | Etichetta per rappresentare il cloud di Azure Stack locale.| "AzureStack" |
| DisableMultiCloud | Un'opzione per disabilitare la modalità multi-cloud.| N/D |
| | |

È possibile eseguire lo script Add-AzurePackConnector.ps1 immediatamente dopo la distribuzione, o versione successiva. Per eseguire lo script immediatamente dopo la distribuzione, utilizzare la stessa sessione di Windows PowerShell in cui è stata completata la distribuzione dello Stack di Azure. In caso contrario, è possibile aprire una nuova sessione di Windows PowerShell come amministratore (con segno come account azurestackadmin).

1. Eseguire lo script Add-AzurePackConnector.ps1 utilizzando i comandi seguenti (con valori specifici per l'ambiente). Si noti che lo script Add-AzurePackConnector consente di aggiungere più di un endpoint di connettore di Windows Azure Pack.
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> Si verifica un problema nella generazione corrente in cui al termine, lo script Add-AzurePackConnector rimane in un ciclo di polling per un lungo periodo di tempo (alcuni minuti) finché non termina. Dopo aver visualizzato il messaggio **VERBOSE: passaggio 'Configura connettore di Azure Pack' stato: 'Success'**, è possibile arrestare lo script o attendere finché termina automaticamente. Rende una differenza non perché la configurazione già ha avuto esito positivo.

2. Prendere nota dei file di output generati da questo script, uno per ogni ambiente di Windows Azure Pack specificato. I file si trovano in: \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput. Questi file contengono le informazioni necessarie per configurare gli ambienti Windows Azure Pack di destinazione. Si passa questo file come parametro a uno script in un secondo momento in queste istruzioni. Questo file contiene le informazioni seguenti:

    * **AzurePackConnectorEndpoint**: contiene l'endpoint al servizio del connettore di Windows Azure Pack.
    * **AuthenticationIdentityProviderPartner**: contiene la coppia di valore seguenti:
        * L'API Tenant di Windows Azure Pack deve avere fiducia per accettare chiamate dall'estensione del portale di Azure Stack certificato di firma di Token di autenticazione.

        * "Realm" associata al certificato di firma. Ad esempio: https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/.

3.  Passare alla cartella che contiene i file di output (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) e copiare i file nel computer locale. I file avrà un aspetto simili al seguente: AzurePack-06-27-15-50.txt.

4.  Test della configurazione.

    a. Aprire il browser e accedere al portale utenti Azure Stack (https://portal.local.azurestack.external/).
    
    b. Dopo l'accesso come un tenant e il caricamento del portale, si noterà errori relativi a non è in grado di recuperare le sottoscrizioni o le estensioni dal cloud di Azure Pack. Fare clic su **OK** per chiudere questi messaggi. (Questi messaggi di errore scomparirà dopo aver configurato Windows Azure Pack.)

    c. Si noti il **Cloud** elenco a discesa nell'angolo superiore sinistro del portale.

    ![Il selettore di cloud nell'interfaccia utente di Azure Stack](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>Configurare Windows Azure Pack
Per questo connettore sola versione di anteprima, Windows Azure Pack richiede la configurazione manuale.

>[!IMPORTANT]
Per questa versione di anteprima, usare il connettore di Windows Azure Pack solo in ambienti di test e non nelle distribuzioni di produzione.

1.  Installare i file MSI connettore nella macchina virtuale del portale tenant Windows Azure Pack e installare i certificati. (Se si dispone di più macchine virtuali del portale tenant, è necessario completare questo passaggio in ogni macchina virtuale.)

    a. In Esplora File, copiare il **WAPConnector** cartella (ciò che è stato scaricato in precedenza) in una cartella denominata **c:\temp** nella macchina virtuale del portale tenant.

    b. Aprire una Console o RDP connessione alla macchina virtuale del portale tenant.

    c. Passare alla directory **c:\temp\wapconnector\setup\scripts**ed eseguire il **installazione Connector.ps1** script per installare tre file MSI. Non sono richiesti parametri.

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. Passare alla directory **c:\inetpub** e verificare che siano installati i tre nuovi siti:

       * MgmtSvc-Connector

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    e. Dalla stessa **c:\temp\wapconnector\setup\scripts** cartella, eseguire il **Configura Certificates.ps1** script per installare i certificati. Per impostazione predefinita, utilizza lo stesso certificato che è disponibile per il sito portale Tenant in Windows Azure Pack. Verificare che si tratta di un certificato valido (attendibile per la macchina virtuale di Azure Stack AzS-WASP01 e tutti i computer client che accede al portale di Azure Stack). In caso contrario, la comunicazione non funzionerà. (In alternativa, è possibile passare in modo esplicito un'identificazione personale del certificato come parametro con parametro - identificazione personale.)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. Per completare la configurazione di questi tre servizi, eseguire il **Configura WapConnector.ps1** script per aggiornare i parametri del file Web. config.

    |  Parametro | DESCRIZIONE | Esempio |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | Il portale tenant Windows Azure Pack FQDN. | tenant.contoso.com | 
    | TenantAPIFQDN | Windows Azure Pack Tenant API nome completo. | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | Il portale utenti Azure Stack FQDN. | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. Se si dispone di più macchine virtuali del portale tenant, ripetere il passaggio 1 per ognuna di queste macchine virtuali.

2. Installare il nuovo file MSI API Tenant in ogni macchina virtuale API Tenant di Windows Azure Pack.

    a. Se un servizio di bilanciamento del carico è in uso, si desidera contrassegnare come non in linea la macchina virtuale.

    b. In Esplora File, copiare il **WAPConnector** cartella in una cartella denominata **c:\temp** in ogni computer con l'API Tenant.

    c. Copiare il file AzurePackConnectorOutput.txt salvato in precedenza, per **c:\temp\WAPConnector**.

    d. Aprire una connessione Console o RDP alla macchina virtuale API Tenant è stato copiato i file.
    
    e. Passare alla directory **c:\temp\wapconnector\setup\scripts**ed eseguire **aggiornamento TenantAPI.ps1**. Questa nuova versione dell'API Tenant WAP contiene una modifica per abilitare una relazione di trust, non solo con il servizio token di sicurezza corrente, ma anche con l'istanza di AD FS nello Stack di Azure.

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  Ripetere il passaggio 2 in altre macchine virtuali in esecuzione l'API Tenant.
3. Da **sola** delle macchine virtuali API Tenant, eseguire lo script di configurazione TrustAzureStack.ps1 per aggiungere una relazione di trust tra l'API Tenant e l'istanza di AD FS nello Stack di Azure. È necessario utilizzare un account con accesso sysadmin nel database di Store. Questo script ha i seguenti parametri:

    | Parametro | DESCRIZIONE | Esempio |
    | --------- | ------------| ------- |
   | SqlServer | Il nome del Server SQL che contiene il database di Store. Questo parametro è obbligatorio. | SQLServer | 
   | DataFile | Il file di output generati durante la configurazione della modalità multi-cloud Azure Stack in precedenza. Questo parametro è obbligatorio. | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | Indica che lo script deve richiedere in modo interattivo per le credenziali di autenticazione di SQL Server da utilizzare quando ci si connette all'istanza di SQL Server. Le credenziali specificate devono avere autorizzazioni sufficienti per disinstallare i database, schemi ed eliminare gli account di accesso utente. Se non è specificato, lo script si presuppone che il contesto utente corrente ha accesso. | Non è necessario alcun valore. |
   |  |  |

    Se non si conosce il Server SQL da utilizzare, è possibile individuarla. Connettersi al computer l'API Tenant, usare il comando Unprotect MgmtSvc per rimuovere la protezione il file Web. config API Tenant e cercare il nome del server nella stringa di connessione. Ricordare di eseguire Protect-MgmtSvc nuovamente per proteggere il file Web. config API Tenant.

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>Esempio
Nell'esempio seguente viene illustrata una distribuzione completa di Windows Azure Pack connettore in una configurazione di Azure Stack a nodo singolo e due installazioni Express per Windows Azure Pack. (Ogni installazione di Express è in un singolo computer, con i nomi di esempio *wapcomputer1* e*wapcomputer2*.)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
Scaricare il file .exe il [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), decomprimerlo e copiare la cartella WAPConnector un **c:\temp** cartella nel computer di Windows Azure Pack. Copiare i file che sono stati generati come output nello script precedente (in \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) per il **c:\temp\WAPConnector** cartella. (I file verranno ha un aspetto simile al seguente: AzurePack-06-27-15-50.txt.) Quindi, eseguire lo script seguente, una volta per ogni istanza di Windows Azure Pack:

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi
1.  Verificare la connettività di rete tra Stack di Azure e Windows Azure Pack. Tale connettività deve essere compreso tra computer tenant che accede al portale di Azure Stack e la macchina virtuale del portale del tenant Windows Azure Pack in cui sono in esecuzione i servizi del nuovo connettore.
2.  Assicurarsi che tutti i nomi di dominio completi siano corretti.
3.  Verificare che i certificati SSL usati con i nuovi servizi del connettore sono considerati attendibili dallo Stack di Azure (in particolare la macchina virtuale AzS WASP01) e qualsiasi altro computer tenant può utilizzare per accedere al portale utenti Azure Stack.
4. Per i problemi noti, vedere [la risoluzione dei problemi di Microsoft Azure Stack](azure-stack-troubleshooting.md).


## <a name="next-steps"></a>Passaggi successivi
[Tramite i portali di amministratore e utente nello Stack di Azure](azure-stack-manage-portals.md)
