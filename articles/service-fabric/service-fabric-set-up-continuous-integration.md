<properties
   pageTitle="Integrazione continua per l'infrastruttura di servizi | Microsoft Azure"
   description="Panoramica di come configurare l'integrazione continua per un'applicazione dell'infrastruttura di servizi tramite Visual Studio Online (VSO)."
   services="service-fabric"
   documentationCenter="na"
   authors="cawa"
   manager="paulyuk"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/16/2015"
   ms.author="cawa" />

# Configurare l'integrazione continua per un'applicazione dell'infrastruttura di servizi con Visual Studio Online (VSO)

Questo articolo descrive la configurazione dell'integrazione continua per un'applicazione dell'infrastruttura di servizi tramite Visual Studio Online (VSO) per compilarla, crearne il pacchetto e distribuirla automaticamente. Questo documento riflette l'esperienza corrente ed è soggetto a modifiche man mano che procede l'attività di sviluppo. Inoltre, queste istruzioni ricreano ogni volta il cluster da zero.

## Prerequisiti

Per iniziare, configurare il progetto in Visual Studio Online.

1. Se non è già stato fatto, creare un account VSO usando il proprio [account Microsoft](http://www.microsoft.com/account).
2. Creare un nuovo progetto in VSO con l'account Microsoft.
3. Effettuare il push dell'origine per l'app dell'infrastruttura di servizi nuova o esistente a questo progetto.

Per altre informazioni sull'utilizzo dei progetti VSO, vedere [Connettersi a Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

## Passaggi di configurazione

### Configurare l'autenticazione per l'automazione

Prima di poter configurare il computer di compilazione, è necessario creare una "entità servizio" che viene usata dall'automazione per l'autenticazione ad Azure. Occorre creare anche un certificato e caricarlo in un insieme di credenziali delle chiavi, perché l'insieme di credenziali delle chiavi di Azure non supporta l'autenticazione all'entità servizio. È possibile eseguire questa procedura da qualsiasi computer. Il computer di sviluppo è un'ottima scelta.

### Installare Azure PowerShell e accedere

1.	Installare Azure PowerShell.
    - Installare PowerShellGet. A questo scopo, installare [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729), che include PowerShellGet.
    >[AZURE.NOTE]Se si esegue Windows 10 con gli aggiornamenti più recenti, è possibile ignorare questo passaggio.

1.	Installare e aggiornare il modulo AzureRM.
    1.  Se è installata una versione precedente di Azure PowerShell, rimuoverla. Fare clic con il pulsante destro del mouse sul pulsante Start e scegliere "Installazione applicazioni". Cercare "Azure PowerShell" e disinstallarlo.
    1.  Avviare un prompt dei comandi di PowerShell.
    1.	Installare il modulo "AzureRM" con il comando `Install-Module AzureRM`.
    1.	Aggiornare il modulo "AzureRM" con il comando `Update-AzureRM`.
1.	Disabilitare o abilitare la raccolta di dati di Azure.

    I cmdlet di Azure chiederanno di acconsentire o rifiutare esplicitamente la raccolta di dati finché non si effettua una scelta. Questi prompt bloccheranno l'automazione in attesa dell'input dell'utente. Per eliminare questi prompt effettuando in anticipo una scelta, eseguire uno dei comandi seguenti:

    - Enable-AzureRmDataCollection
    - Disable-AzureRmDataCollection

1.	Accedere ad Azure PowerShell.
    1. Avviare un prompt amministratore di PowerShell ed eseguire in comando `Login-AzureRMAccount`.
    1. Nella finestra di dialogo visualizzata immettere le credenziali di Azure.
    1. Eseguire il comando `Get-AzureRmSubscription`.
    1. Trovare la sottoscrizione che si vuole usare e quindi eseguire il comando `Select-AzureRmSubscription -SubscriptionId <id for your subscription>`."

### Creare un'entità servizio

1.	Estrarre [ServiceFabricCIManualScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703773) in una cartella di questo computer.
1.	In un prompt dei comandi amministratore di PowerShell passare alla directory in cui è stato estratto ServiceFabricCIManualScripts.zip.
1.	Scegliere una password per l'entità servizio usando il comando seguente. Ricordare questa password, perché verrà usata come variabile di compilazione.

    ```
    $password = Read-Host -AsSecureString
    ```
1.	Eseguire lo script di PowerShell Create-ServicePrincipal.ps1 con i parametri seguenti:

    |Parametro|Valore|
    |---|---|
    |ServicePrincipalDisplayName|Un nome qualsiasi.|
    |ServicePrincipalHomePage|Un URI qualsiasi. Non è necessario che esista effettivamente.|
    |ServicePrincipalIdentifierUri|Qualsiasi URI univoco. Non è necessario che esista effettivamente.|
    |ServicePrincipalSecurePassword|$password|

    Al termine dell'esecuzione, lo script restituisce i tre valori seguenti. Prendere nota dei valori, perché verranno usati come variabili di compilazione.

    - ServicePrincipalId
    - ServicePrincipalTenantId
    - ServicePrincipalSubscriptionId

### Creare un certificato e caricarlo in un nuovo insieme di credenziali delle chiavi

1.	In un prompt amministratore di PowerShell passare alla directory in cui è stato estratto ServiceFabricCIManualScripts.zip.
1.	Eseguire lo script di PowerShell CreateAndUpload-Certificate.ps1 con i parametri seguenti:

    |Parametro|Valore|
    |---|---|
    |ServiceFabricKeyVaultLocation|Qualsiasi valore. Deve corrispondere al percorso in cui si prevede di creare il cluster.|
    |ServiceFabricCertificateSecretName|Qualsiasi valore.|
    |ServiceFabricSecureCertificatePassword|Qualsiasi valore. Viene usato quando si importa il certificato nel computer di compilazione.|
    |ServiceFabricKeyVaultResourceGroupName|Qualsiasi valore. Non usare però il nome del gruppo di risorse che si prevede di usare per il cluster.|
    |ServiceFabricKeyVaultName|Qualsiasi valore.|
    |ServiceFabricPfxFileOutputPath|Qualsiasi valore. Questo file viene usato per importare il certificato nel compilare di compilazione.|

    Al termine dell'esecuzione, lo script restituisce i tre valori seguenti. Prendere nota di questi valori, perché verrano usati come variabili di compilazione.

    - ServiceFabricCertificateThumbprint
    - ServiceFabricKeyVaultId
    - ServiceFabricCertificateSecretId


## Configurare il computer di compilazione

Notare che la definizione di compilazione creata da queste istruzioni non supporta le compilazioni parallele, anche se in computer separati. Questo è dovuto al fatto che ogni compilazione si contenderà lo stesso gruppo di risorse/cluster. Per eseguire più agenti di compilazione, sarà necessario modificare le istruzioni o gli script seguenti per evitare interferenze.

### Installare Visual Studio 2015

1.	Se è già stato effettuato il provisioning di un computer, o se si prevede di fornire il proprio, installare [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) in quel computer.
2.	Se non si ha ancora un computer, è possibile effettuare rapidamente il provisioning di una macchina virtuale (VM) di Azure con Visual Studio 2015 preinstallato. A tale scopo, seguire questa procedura:
    1.	Accedere al [portale di gestione di Azure](http://portal.azure.com).
    1.	Scegliere il comando **Nuovo** nell'angolo superiore sinistro della schermata.
    1.	Scegliere **Marketplace**.
    1.	Cercare **Visual Studio 2015**.
    1.	Selezionare **Calcolo** -> **Macchina virtuale** -> **Da raccolta**.
    1.	Scegliere l'immagine **Visual Studio Enterprise 2015 con Azure SDK 2.7 in Windows Server 2012 R2**.

        >[AZURE.NOTE]Azure SDK non è un componente obbligatorio, ma non sono disponibili immagini con installato solo Visual Studio 2015.

    1.	Seguire le istruzioni visualizzate nella finestra di dialogo per creare la VM. È consigliabile scegliere una VM serie D per ottenere prestazioni del disco e della CPU ottimali.

### Installare Service Fabric SDK

Installare [Service Fabric SDK](https://azure.microsoft.com/zh-CN/campaigns/service-fabric/).

### Registrare il repository NuGet di Service Fabric SDK

1.	Creare la directory `%SYSTEMDRIVE%\Windows\ServiceProfiles\LocalService\AppData\Roaming\NuGet` nel computer di compilazione, se non esiste già.
2.	Se in questa directory è già presente un file NuGet.config, aprirlo e aggiungere l'elemento seguente come figlio di `<packageSources>`.

    ```
    <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />`
    ```

3.	Se NuGet.config non è già presente, crearlo con il contenuto seguente. Sostituire`<path to service fabric SDK>` con il percorso di Service Fabric SDK nel computer di compilazione.
>[AZURE.NOTE]Per impostazione predefinita, `<path to service fabric SDK>` è `%ProgramFiles%\Microsoft SDKs\Service Fabric`.

    NuGet.config:

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <packageSources>
        <!-- Add this repository to the list of available repositories -->
        <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />
      </packageSources>
    </configuration>
    ```

### Installare Azure PowerShell

Per installare Azure PowerShell, seguire la procedura nella sezione precedente **Installare Azure PowerShell e accedere**. Ignorare il sottoparagrafo **Accedere ad Azure PowerShell**.

### Registrare i moduli di Azure PowerShell con l'account Servizio locale

1. Premere WIN+R, digitare **regedit** e premere INVIO.
2. Fare clic con il pulsante destro del mouse sul nodo `HKEY_Users\.Default\Environment` e selezionare **Nuovo > Valore stringa espandibile**.
3. Immettere `PSModulePath` come nome e `%PROGRAMFILES%\WindowsPowerShell\Modules` come valore.

>[AZURE.NOTE]Eseguire questa operazione *prima* di avviare l'agente di compilazione, altrimenti non selezionerà la nuova variabile di ambiente.

### Impostare il certificato di Automazione

1.	Impostare il certificato nel computer di compilazione. A tale scopo, seguire questa procedura:
    1. Copiare il file PFX creato dallo script CreateAndUpload-Certificate.ps1 nel computer di compilazione.
    1. Aprire un account amministratore di PowerShell ed eseguire i comandi seguenti con la password passata a GenerateCertificate.ps1 in precedenza.

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```  

1.	Eseguire il gestore di certificati.
    1. Aprire il Pannello di controllo di Windows. Fare clic con il pulsante destro del mouse sul pulsante Start e scegliere **Pannello di controllo**.
    1. Cercare **certificato**.
    1. Scegliere **Strumenti di amministrazione** > **Gestisci i certificati computer**.


1.	Concedere all'account Servizio locale l'autorizzazione per usare il certificato di Automazione.
    1.	In **Certificati - Computer locale** espandere **Personale** e scegliere **Certificati**.
    1.	Trovare il certificato nell'elenco.
    1.	Fare clic con il pulsante destro del mouse sul certificato e quindi scegliere **Tutte le attività** > **Gestisci chiavi private**.
    1.	Fare clic sul pulsante **Aggiungi**, quindi immettere **Servizio locale** e scegliere **Controlla nomi**.
    1.	Fare clic su **OK** e chiudere il gestore di certificati.

![](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### Registrare l'agente di compilazione

1.	Scaricare agent.zip. A tale scopo, seguire questa procedura:
    1.	Accedere al progetto team, ad esempio ****https://[your-VSO-account-name].visualstudio.com**.
1.	Fare clic sull'icona a forma di "ingranaggio" nell'angolo in alto a destra della schermata.
    1.	Nel Pannello di controllo scegliere la scheda **Pool di agenti**.
    1.	Scegliere **Scarica agente** per scaricare il file agent.zip.
    1.	Copiare agent.zip nel computer di compilazione creato in precedenza.
    1.	Decomprimere agent.zip in `C:\agent`, o qualsiasi posizione con un percorso breve, nel compilare di compilazione.

        >[AZURE.NOTE]Se si prevede di compilare servizi Web ASP.NET 5, è consigliabile scegliere il nome più breve possibile per questa cartella, per evitare che si verifichino errori **PathTooLongExceptions** durante la distribuzione.

1.	A un prompt dei comandi amministratore di PowerShell, eseguire `C:\agent\ConfigureAgent.ps1`. Lo script richiede i parametri seguenti:

    |Parametro|Valore|
    |---|---|
    |Agent Name|Accettare il valore predefinito, `Agent-[machine name]`.
    |TFS Url|Immettere l'URL del progetto team, ad esempio `https://[your-VSO-account-name].visualstudio.com`.
    |Agent Pool|Immettere il nome del pool di agenti. Se non è stato creato un pool di agenti, accettare il valore predefinito.|
    |Work folder|Accettare il valore predefinito. Questa è la cartella il cui l'agente di compilazione eseguirà effettivamente la compilazione dell'applicazione. Nota: se si prevede di compilare servizi Web ASP.NET 5, è consigliabile scegliere il nome più breve possibile per questa cartella, per evitare che si verifichino errori PathTooLongExceptions durante la distribuzione.|
    |Install as Windows Service?|Il valore predefinito è N, modificarlo in Y.|
    |User account to run the service|Accettare il valore predefinito, `NT AUTHORITY\LocalService)`.|
    |Un-configure existing agent?|Accettare il valore predefinito,**N**.|

1. Verrà richiesto di immettere le credenziali. Immettere le credenziali dell'account Microsoft con diritti per il progetto team.
1. Verificare che l'agente di compilazione sia registrato. A tale scopo, seguire questa procedura:

    1. Tornare al Web browser, dovrebbe essere visualizzata la pagina `https://[your-VSO-account-name].visualstudio.com/_admin/_AgentPool`, quindi aggiornarla.
    1. Scegliere il pool di agenti selezionato prima durante l'esecuzione di ConfigureAgent.ps1.
    1. Verificare che l'agente di compilazione sia visualizzato nell'elenco e sia contrassegnato con uno stato verde. Se lo stato è rosso, l'agente di compilazione non riesce a connettersi a VSO.

![](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## Configurare la definizione di compilazione

### Aggiungere gli script di integrazione continua al controllo del codice sorgente per l'applicazione in uso.

1.	Estrarre [ServiceFabricCIAutomationScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703775) in una cartella qualsiasi nel controllo del codice sorgente.
1.	Archiviare i file risultanti.

### Creare la definizione di compilazione

1.	Creare una definizione di compilazione vuota. A tale scopo, seguire questa procedura:
    1.	Aprire il progetto in Visual Studio Online.
    1.	Scegliere la scheda **Compilazione**.
    1.	Scegliere il segno **+** verde per creare una nuova definizione di compilazione.
    1.	Scegliere **Vuota** e quindi fare clic su **Avanti**.
    1.  Verificare che siano selezionati il repository e il ramo corretti.
    1.  Selezionare la coda di agenti in cui è stato registrato l'agente di compilazione e selezionare la casella di controllo **Integrazione continua**.
1.	Nella scheda **Variabili** creare le variabili seguenti con questi valori.

    |Variabile|Valore|Segreto|Consenti in tempo coda|
    |---|---|---|---|
    |BuildConfiguration|Release||X|
    |BuildPlatform|x64|||
    |ServicePrincipalPassword|Password passata a CreateServicePrincipal.ps1|X||
    |ServicePrincipalId|Dall'output di CreateServicePrincipal.ps1|||
    |ServicePrincipalTenantId|Dall'output di CreateServicePrincipal.ps1|||
    |ServicePrincipalSubscriptionId|Dall'output di CreateServicePrincipal.ps1|||
    |ServiceFabricCertificateThumbprint|Dall'output di GenerateCertificate.ps1|||
    |ServiceFabricKeyVaultId|Dall'output di GenerateCertificate.ps1|||
    |ServiceFabricCertificateSecretId|Dall'output di GenerateCertificate.ps1|||
    |ServiceFabricClusterResourceGroupName|Qualsiasi nome.|||
    |ServiceFabricClusterName|Qualsiasi nome.|||
    |ServiceFabricClusterLocation|Qualsiasi nome corrispondente al percorso dell'insieme di credenziali delle chiavi.|||
    |ServiceFabricClusterAdminPassword|Qualsiasi nome.|X||
    |ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
    |ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` Nota: l'endpoint connessione nel profilo di pubblicazione sarà ignorato. Verrà invece usato l'endpoint connessione del cluster temporaneo.|||
    |ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
    |ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` Deve essere la cartella che contiene il file SFPROJ.||||

1.	Nella scheda **Trigger** selezionare le opzioni **Integrazione continua** e **Modifiche batch**.
1.	Nella scheda **Generale** scegliere la coda in cui sono stati registrati gli agenti di compilazione.
1.	Salvare la definizione di compilazione e assegnare un nome. È possibile modificare questo nome in seguito.

### Aggiungere un'istruzione "Compilazione"

1.	Nella scheda **Compilazione** scegliere il comando **Aggiungi istruzione di compilazione**.
2.	Scegliere **Compila** > **MSBuild**.
3.	Scegliere l'icona a forma di penna accanto al nome dell'istruzione di compilazione e rinominarla in **Compilazione**.
4.	Scegliere il pulsante **…** accanto al campo **Soluzione** e quindi selezionare il file SLN.
5.	Immettere `$(BuildPlatform)` per **Piattaforma**.
6.	Immettere `$(BuildConfiguration)` per **Configurazione**.
7.	Selezionare la casella di controllo **Ripristina pacchetti NuGet**, se non è già selezionata.
8.	Salvare la definizione di compilazione.

### Aggiungere un'istruzione "Pacchetto"

1.	Nella scheda **Compilazione** scegliere il comando **Aggiungi istruzione di compilazione**.
2.	Scegliere **Compila** > **MSBuild**.
3.	Scegliere l'icona a forma di matita accanto al nome dell'istruzione di compilazione e rinominarla in **Pacchetto**.
4.	Scegliere il pulsante **…** accanto al campo **Soluzione** e quindi selezionare il file SFPROJ del progetto di applicazione.
5.	Immettere `$(BuildPlatform)` per **Piattaforma**.
6.	Immettere `$(BuildConfiguration)` per **Configurazione**.
7.	Immettere `/t:Package` per **Argomenti MSBuild**.
8.	Deselezionare la casella di controllo **Ripristina pacchetti NuGet**, se non è già deselezionata.
9.	Salvare la definizione di compilazione.

### Aggiungere un'istruzione "Rimuovere il gruppo di risorse cluster"

Se una compilazione precedente non si è pulita automaticamente al termine dell'esecuzione, ad esempio perché è stata annullata prima di poter eseguire la pulizia, è possibile che un gruppo di risorse esistente entri in conflitto con quello nuovo. Per evitare conflitti, pulire gli eventuali gruppi di risorse rimasti, e le risorse associate, prima di crearne uno nuovo.

1.	Nella scheda **Compilazione** scegliere il comando **Aggiungi istruzione di compilazione**.
2.	Scegliere **Utilità** > **PowerShell**.
3.	Scegliere l'icona a forma di matita accanto al nome dell'istruzione di compilazione e quindi rinominarla in **Rimuovere il gruppo di risorse cluster**.
4.	Scegliere il comando **…** accanto a **Nome file dello script**. Passare al percorso in cui sono stati estratti gli script di automazione e quindi scegliere **Remove-ClusterResourceGroup.ps1**.
5.	Per **Argomenti** immettere `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`.
6.	Salvare la definizione di compilazione.

### Aggiungere un'istruzione "Effettuare il provisioning e distribuire al cluster sicuro"

1.	Nella scheda **Compilazione** scegliere il comando **Aggiungi istruzione di compilazione**.
2.	Scegliere **Utilità** > **PowerShell**.
3.	Scegliere l'icona a forma di matita accanto al nome dell'istruzione di compilazione e quindi rinominarla in **Effettuare il provisioning e distribuire al cluster sicuro**.
4.	Scegliere il pulsante **…** accanto a **Nome file dello script**. Passare al percorso in cui sono stati estratti gli script di automazione e quindi scegliere **ProvisionAndDeploy-SecureCluster.ps1**.
5.	Per "Argomenti" immettere `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`.
6.	Salvare la definizione di compilazione.

### Aggiungere un'istruzione "Rimuovere il gruppo di risorse cluster"

A questo punto il cluster temporaneo è completato e occorre quindi pulirlo. In caso contrario continueranno a essere addebitati i costi corrispondenti. Questo passaggio rimuove il gruppo di risorse, che rimuove il cluster e tutte le altre risorse nel gruppo.

1.	Nella scheda **Compilazione** scegliere il comando **Aggiungi istruzione di compilazione**.
1.	Scegliere **Utilità** > **PowerShell**.
1.	Scegliere l'icona a forma di matita accanto al nome dell'istruzione di compilazione e quindi rinominarla in **Rimuovere il gruppo di risorse cluster**.
1.	Scegliere il pulsante **…** accanto a **Nome file dello script**. Passare al percorso in cui sono stati estratti gli script di automazione e quindi scegliere **RemoveClusterResourceGroup.ps1**.
1.	Per "Argomenti" immettere `-ServicePrincipalPassword "$(ServicePrincipalPassword)`.
1.	In **Opzioni di controllo** selezionare la casella di controllo **Esegui sempre**.
1.	Salvare la definizione di compilazione.

### Prova

Scegliere il comando **Accoda compilazione** per avviare una compilazione. Le compilazioni vengono attivate anche all'archiviazione.


## Soluzioni alternative

Le istruzioni precedenti creano un nuovo cluster per ogni compilazione e lo rimuovono al termine dell'operazione. Se invece si preferisce che ogni compilazione esegua l'aggiornamento di un'applicazione, in un cluster esistente, seguire questa procedura.

1.	Creare manualmente un cluster di test tramite il portale di gestione di Azure o Azure PowerShell. Come riferimento, è possibile vedere lo script "ProvisionAndDeploy-SecureCluster.ps1".
1.	Configurare il profilo di pubblicazione per supportare l'aggiornamento dell'applicazione seguendo [queste istruzioni](service-fabric-visualstudio-configure-upgrade.md).

1.	Sostituire l'istruzione **Effettuare il provisioning e distribuire al cluster sicuro** con un'istruzione che chiama direttamente Deploy-FabricApplication.ps1 e la passa al profilo di pubblicazione.
1.	Rimuovere entrambe le istruzioni di compilazione **Rimuovere il gruppo di risorse cluster** dalla definizione di compilazione.

## Passaggi successivi

Per altre informazioni sull'integrazione continua con le applicazioni dell'infrastruttura di servizi, vedere gli articoli seguenti. - [Home page di documentazione sulla compilazione](https://msdn.microsoft.com/Library/vs/alm/Build/overview) - [Distribuire un agente di compilazione](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows) - [Creare e configurare una definizione di compilazione](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=Nov15_HO4-->