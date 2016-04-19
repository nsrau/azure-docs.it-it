<properties
   pageTitle="Integrazione continua per l'infrastruttura di servizi | Microsoft Azure"
   description="Panoramica di come configurare l'integrazione continua per un'applicazione Service Fabric tramite Visual Studio Team Services (VSTS)."
   services="service-fabric"
   documentationCenter="na"
   authors="cawams"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="03/29/2016"
   ms.author="cawa" />

# Configurare l'integrazione continua per un'applicazione Service Fabric con Visual Studio Team Services

Questo articolo descrive la procedura per configurare l'integrazione continua per un'applicazione di Azure Service Fabric usando Visual Studio Team Services (VSTS) per compilarla, crearne il pacchetto e distribuirla automaticamente. Si noti che queste istruzioni ricreano ogni volta il cluster da zero.

Questo documento descrive la procedura corrente e può essere modificato nel tempo.

## Prerequisiti

Per iniziare, configurare il progetto in Visual Studio Team Services:

1. Se non è già stato fatto, creare un account di Team Services usando il proprio [account Microsoft](http://www.microsoft.com/account).

2. Creare un nuovo progetto in Team Services con l'account Microsoft.

3. Effettuare il push dell'origine per l'app dell'infrastruttura di servizi nuova o esistente a questo progetto.

Per altre informazioni sull'uso dei progetti Team Services, vedere [Connettersi a Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

## Configurare l'entità servizio

### Configurare l'autenticazione per l'automazione

Prima di configurare il computer di compilazione, è necessario creare un'[entità servizio](../resource-group-create-service-principal-portal.md) di cui si avvarrà l'agente di compilazione per l'autenticazione in Azure. È anche necessario creare un certificato e caricarlo nell'insieme di credenziali delle chiavi di Azure, perché l'insieme di credenziali delle chiavi non supporta l'autenticazione dell'entità servizio. È possibile eseguire questa procedura da qualsiasi computer. Il computer di sviluppo è un'ottima scelta.

### Installare Azure PowerShell e accedere

1.  Installare PowerShellGet.

    a. Se si esegue Windows 10 con gli aggiornamenti più recenti, è possibile ignorare questo passaggio. PowerShellGet è già installato.

    b. In caso contrario, installare [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729), che include PowerShellGet.

2.	Installare e aggiornare il modulo AzureRM. Se è installata una versione precedente di Azure PowerShell, rimuoverla.

    a. Fare clic con il pulsante destro del mouse sul pulsante Start e quindi scegliere **Installazione applicazioni**.

    b. Cercare "Azure PowerShell" e disinstallarlo.

    c. Aprire un prompt dei comandi di PowerShell.

    d. Installare il modulo AzureRM con il comando `Install-Module AzureRM`.

    e. Aggiornare il modulo AzureRM con il comando `Update-AzureRM`.

3.	Disabilitare o abilitare la raccolta di dati di Azure.

    I cmdlet di Azure chiederanno di acconsentire o rifiutare esplicitamente la raccolta di dati finché non si effettua una scelta. Questi prompt bloccheranno l'automazione in attesa dell'input dell'utente. Per eliminare questi prompt effettuare in anticipo una scelta eseguendo uno dei comandi seguenti:

    - Enable-AzureRmDataCollection

    - Disable-AzureRmDataCollection

4.	Accedere ad Azure PowerShell:

    a. Eseguire il comando `Login-AzureRmAccount`.

    b. Nella finestra di dialogo visualizzata immettere le credenziali di Azure.

    c. Eseguire il comando `Get-AzureRmSubscription`.

    d. Trovare la sottoscrizione da usare.

    e. Eseguire il comando `Select-AzureRmSubscription -SubscriptionId <ID for your subscription>`.

### Creare un’entità servizio

1. Seguire [queste istruzioni](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) per creare un'entità servizio e un endpoint di servizio per il progetto.

2. Annotare i valori stampati alla fine dell'output dello script. Saranno necessari per configurare la definizione di compilazione.

### Creare un certificato e caricarlo in un nuovo insieme di credenziali delle chiavi

>[AZURE.NOTE] Questo script di esempio genera un certificato autofirmato. Questa operazione, tuttavia, non è sicura ed è accettabile solo a scopo di sperimentazione. Attenersi alle linee guida aziendali per ottenere un certificato legittimo. Queste istruzioni usano anche un singolo certificato per il server e il client. In produzione è consigliabile usare certificati server e client separati.

1. Scaricare ed estrarre [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) in una cartella del computer in uso.

2. Da un prompt amministratore di PowerShell passare alla directory `<extracted zip>/Manual`.

3. Eseguire lo script `CreateAndUpload-Certificate.ps1` di PowerShell con i parametri seguenti:

| Parametro | Valore |
| --- | --- |
| KeyVaultLocation | Qualsiasi valore. Questo parametro deve corrispondere al percorso in cui si prevede di creare il cluster. |
| CertificateSecretName | Qualsiasi valore. |
| CertificateDnsName | Deve corrispondere al nome DNS del cluster. Esempio: `mycluster.westus.azure.cloudapp.net` |
| SecureCertificatePassword | Qualsiasi valore. Questo parametro viene usato quando si importa il certificato nel computer di compilazione. |
| KeyVaultResourceGroupName | Qualsiasi valore. Non usare però il nome del gruppo di risorse che si prevede di usare per il cluster. |
| KeyVaultName | Qualsiasi valore. |
| PfxFileOutputPath| Qualsiasi valore. Questo file viene usato per importare il certificato nel computer di compilazione. |

Al termine dell'esecuzione, lo script restituisce i tre valori seguenti. Prendere nota di questi valori, perché verrano usati come variabili di compilazione.

 - `ServiceFabricCertificateThumbprint`
 - `ServiceFabricKeyVaultId`
 - `ServiceFabricCertificateSecretId`

## Configurare il computer di compilazione

### Installare Visual Studio 2015

Se è già stato effettuato il provisioning di un computer o se si prevede di fornire il proprio, installare [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) nel computer selezionato.

Se non si ha ancora un computer, è possibile effettuare rapidamente il provisioning di una macchina virtuale (VM) di Azure con Visual Studio 2015 preinstallato. A tale scopo, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare il comando **Nuovo** nell'angolo superiore sinistro della schermata.

3. Selezionare **Marketplace**.

4. Cercare **Visual Studio 2015**.

5. Selezionare **Calcolo** -> **Macchina virtuale** -> **Da raccolta**.

6. Selezionare l'immagine **Visual Studio Enterprise 2015 Update 1 con Azure SDK 2.8 in Windows Server 2012 R2**.

    >[AZURE.NOTE] Azure SDK non è un componente obbligatorio, ma attualmente non sono disponibili immagini con la sola installazione di Visual Studio 2015.

7.	Seguire le istruzioni nella finestra di dialogo per creare la VM.

### Installare Service Fabric SDK

Installare [Service Fabric SDK](https://azure.microsoft.com/campaigns/service-fabric/) in questo computer.

### Installare Azure PowerShell

Per installare Azure PowerShell, seguire la procedura nella sezione precedente "Installare Azure PowerShell e accedere". Ignorare il passaggio "Accedere ad Azure PowerShell".

### Registrare i moduli di Azure PowerShell con l'account Servizio di rete

>[AZURE.NOTE] Eseguire questa operazione *prima* di avviare l'agente di compilazione. In caso contrario, la nuova variabile di ambiente non verrà selezionata.

1. Premere il tasto WINDOWS + R, digitare **regedit** e premere INVIO.

2. Fare clic con il pulsante destro del mouse sul nodo `HKEY_Users\.Default\Environment` e quindi scegliere **Nuovo** > **Valore stringa espandibile**.

3. Immettere `PSModulePath` come nome e `%PROGRAMFILES%\WindowsPowerShell\Modules` come valore. Sostituire `%PROGRAMFILES%` con il valore della variabile di ambiente `PROGRAMFILES`.

### Importare il certificato di automazione

1.	Impostare il certificato nel computer di compilazione. A tale scopo, effettuare l'operazione seguente:

    a. Copiare il file PFX creato dallo script CreateAndUpload-Certificate.ps1 nel computer di compilazione.

    b. Aprire un prompt amministratore di PowerShell ed eseguire i comandi seguenti usando la password passata a `CreateAndUpload-Certificate.ps1` in precedenza.

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```

2.	Eseguire il gestore di certificati:

    a. Aprire il Pannello di controllo in Windows. Fare clic con il pulsante destro del mouse sul pulsante Start e quindi scegliere **Pannello di controllo**.

    b. Cercare **certificato**.

    c. Selezionare **Strumenti di amministrazione** > **Gestisci i certificati computer**.

3.	Concedere all'account Servizio di rete l'autorizzazione per usare il certificato di automazione:

    a. In **Certificati - Computer locale** espandere **Personale** e selezionare **Certificati**.

    b. Trovare il certificato nell'elenco.

    c. Fare clic con il pulsante destro del mouse sul certificato e quindi scegliere **Tutte le attività** > **Gestisci chiavi private**.

    d. Scegliere il pulsante **Aggiungi**, quindi immettere **Servizio locale** e selezionare **Controlla nomi**.

    e. Fare clic su **OK** e chiudere il gestore di certificati.

    ![Screenshot dei passaggi per concedere le autorizzazioni dell'account Servizio locale](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

4.  Copiare il certificato nella cartella `Trusted People`.

    a. Il certificato è stato importato in **Personale/Certificati**, ma è necessario aggiungerlo a **Persone attendibili**. Fare clic con il pulsante destro del mouse sul certificato, quindi scegliere **Copia**. Fare quindi clic con il pulsante destro del mouse su **Persone attendibili** e scegliere **Incolla**.

### Registrare l'agente di compilazione

1.	Scaricare agent.zip. A tale scopo, effettuare l'operazione seguente:

    a. Accedere al progetto team, ad esempio **https://[your-VSTS-account-name].visualstudio.com**.

    b. Selezionare l'icona a forma di ingranaggio nell'angolo in alto a destra della schermata.

    c. Selezionare la scheda **Pool di agenti**.

    d. Selezionare **Scarica agente** per scaricare il file agent.zip.

    >[AZURE.NOTE] Se il download non viene avviato, controllare il Blocco popup.

    e. Copiare agent.zip nel computer di compilazione creato in precedenza.

    f. Decomprimere agent.zip in `C:\agent` o in qualsiasi posizione con un percorso breve nel computer di compilazione.

    >[AZURE.NOTE] Se si prevede di usare servizi Web ASP.NET 5, è consigliabile scegliere il nome più breve possibile per questa cartella, per evitare che si verifichino errori **PathTooLongExceptions** durante la distribuzione. Dopo il rilascio di ASP.NET Core, l'impatto di questo problema verrà ridotto.

2.	Da un prompt dei comandi amministratore eseguire `C:\agent\ConfigureAgent.cmd`. Lo script richiede i parametri seguenti:

|Parametro|Valore|
|---|---|
|Agent Name|Accettare il valore predefinito, `Agent-[machine name]`.|
|TFS Url|Immettere l'URL del progetto team, ad esempio `https://[your-VSTS-account-name].visualstudio.com`.|
|Agent Pool|Immettere il nome del pool di agenti. Se non è stato creato un pool di agenti, accettare il valore predefinito.|
|Work folder|Accettare il valore predefinito. Questa è la cartella il cui l'agente di compilazione eseguirà effettivamente la compilazione dell'applicazione. Se si prevede di usare servizi Web ASP.NET 5, è consigliabile scegliere il nome più breve possibile per questa cartella, per evitare che si verifichino errori PathTooLongExceptions durante la distribuzione.|
|Install as Windows Service?|Il valore predefinito è N. Modificarlo in **Y**.|
|User account to run the service|Accettare il valore predefinito, `NT AUTHORITY\NetworkService`.|
|Password per `NT AUTHORITY\Network Service`|L'account Servizio di rete non ha alcuna password, ma rifiuterà le password vuote. Immettere una stringa non vuota per la password. I valori immessi verranno ignorati.|
|Un-configure existing agent?|Accettare il valore predefinito,**N**.|

3.  Quando vengono chieste le credenziali, immettere le credenziali dell'account Microsoft con diritti per il progetto team.

4.  Assicurarsi che l'agente di compilazione sia stato registrato e configurarne le funzionalità. A tale scopo, effettuare l'operazione seguente:

    a. Tornare al Web browser (`https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`) e aggiornare la pagina.

    b. Selezionare il pool di agenti selezionato prima durante l'esecuzione di ConfigureAgent.ps1.

    c. Verificare che l'agente di compilazione sia visualizzato nell'elenco e sia contrassegnato con uno stato verde. Se lo stato è rosso, l'agente di compilazione non riesce a connettersi a Team Services.

    ![Screenshot che illustra lo stato dell'agente di compilazione](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)

    d. Selezionare l'agente di compilazione, quindi la scheda **Funzionalità**.

    e. Aggiungere una funzionalità denominata **azureps** con qualsiasi valore. In questo modo si indica a VSTS che nel computer è installato Azure PowerShell, necessario per l'uso di alcune attività di compilazione fornite da VSTS.


## Configurare la definizione di compilazione

>[AZURE.NOTE] La definizione di compilazione creata a partire da queste istruzioni non supporta più compilazioni simultanee, anche se in computer diversi. Questo perché ogni compilazione si contenderebbe lo stesso gruppo di risorse/cluster. Per eseguire più agenti di compilazione, sarà necessario modificare le istruzioni o gli script seguenti per evitare interferenze.

### Aggiungere gli script di integrazione continua al controllo del codice sorgente per l'applicazione

1.	Estrarre [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) in una cartella di questo computer. Copiare il contenuto di `Powershell\Automation` in una cartella qualsiasi nel controllo del codice sorgente.

2.	Archiviare i file risultanti.

### Creare la definizione di compilazione

1.	Creare una definizione di compilazione vuota. A tale scopo, effettuare l'operazione seguente:

    a. Aprire il progetto in Visual Studio Team Services.

    b. Selezionare la scheda **Compilazione**.

    c. Selezionare il segno **+** verde per creare una nuova definizione di compilazione.

    d. Selezionare **Vuoto** e quindi fare clic su **Avanti**.

    e. Verificare che siano selezionati il repository e il ramo corretti.

    f. Selezionare la coda di agenti in cui è stato registrato l'agente di compilazione e quindi selezionare la casella di controllo **Integrazione continua**.

2.	Nella scheda **Variabili** creare le variabili seguenti con questi valori.

    |Variabile|Valore|Segreto|Consenti in tempo coda|
    |---|---|---|---|
    |BuildConfiguration|Release||X|
    |BuildPlatform|x64|||
    |ServicePrincipalPassword|Password usata durante la creazione dell'entità servizio.|X||
    |ServicePrincipalId|Dall'output dello script usato per creare l'entità servizio.|||
    |ServicePrincipalTenantId|Dall'output dello script usato per creare l'entità servizio.|||
    |ServicePrincipalSubscriptionId|Dall'output dello script usato per creare l'entità servizio.|||
    |ServiceFabricCertificateThumbprint|Dall'output di CreateAndUpload-Certificate.ps1|||
    |ServiceFabricKeyVaultId|Dall'output di CreateAndUpload-Certificate.ps1|||
    |ServiceFabricCertificateSecretId|Dall'output di CreateAndUpload-Certificate.ps1|||
    |ServiceFabricClusterName|Deve corrispondere al nome DNS del certificato.|||
    |ServiceFabricClusterResourceGroupName|Qualsiasi nome.|||
    |ServiceFabricClusterLocation|Deve corrispondere al percorso dell'insieme di credenziali delle chiavi.|||
    |ServiceFabricClusterAdminPassword|8-123 caratteri, con almeno 3 dei tipi di carattere seguenti: maiuscoli, minuscoli, numerici, speciali.|X||
    |ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
    |ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` L'endpoint connessione nel profilo di pubblicazione sarà ignorato. Verrà invece usato l'endpoint connessione del cluster temporaneo.|||
    |ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
    |ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` Deve essere la cartella che contiene il file SFPROJ.||||

3.  Salvare la definizione di compilazione e assegnare un nome. È possibile modificare questo nome in seguito.

### Aggiungere un'istruzione di tipo "Ripristina pacchetti NuGet"

1. Nella scheda **Compilazione** scegliere il comando **Aggiungi istruzione di compilazione**.

2. Scegliere **Pacchetto** > **Programma di installazione NuGet**.

3. Scegliere l'icona a forma di penna accanto al nome dell'istruzione di compilazione e rinominarla in **Ripristina pacchetti NuGet**.

4. Scegliere il pulsante **…** accanto al campo **Soluzione** e quindi selezionare il file SLN.

5. Salvare la definizione di compilazione.

### Aggiungere un passaggio "Compilazione"

1.	Nella scheda **Compilazione** selezionare il comando **Aggiungi istruzione di compilazione**.

2.	Selezionare **Compila** > **MSBuild**.

3.	Selezionare l'icona a forma di penna accanto al nome dell'istruzione di compilazione e quindi rinominarla in **Compilazione**.

4.	Scegliere il pulsante **…** accanto al campo **Soluzione** e quindi selezionare il file SLN.

5.	Immettere `$(BuildPlatform)` per **Piattaforma**.

6.	Immettere `$(BuildConfiguration)` per **Configurazione**.

7.	Selezionare la casella di controllo **Ripristina pacchetti NuGet**, se non è già selezionata.

8.	Salvare la definizione di compilazione.

### Aggiungere un passaggio "Pacchetto"

1.	Nella scheda **Compilazione** selezionare il comando **Aggiungi istruzione di compilazione**.

2.	Selezionare **Compila** > **MSBuild**.

3.	Selezionare l'icona a forma di matita accanto al nome dell'istruzione di compilazione e quindi rinominarla in **Pacchetto**.

4.	Scegliere il pulsante **…** accanto al campo **Soluzione** e quindi selezionare il file SFPROJ del progetto di applicazione.

5.	Immettere `$(BuildPlatform)` per **Piattaforma**.

6.	Immettere `$(BuildConfiguration)` per **Configurazione**.

7.	Immettere `/t:Package` per **Argomenti MSBuild**.

8.	Deselezionare la casella di controllo **Ripristina pacchetti NuGet**, se non è già deselezionata.

9.	Salvare la definizione di compilazione.

### Aggiungere il passaggio "Rimuovere il gruppo di risorse cluster"

Se una compilazione precedente non si è pulita automaticamente al termine dell'esecuzione, ad esempio perché è stata annullata prima di poter eseguire la pulizia, è possibile che un gruppo di risorse esistente entri in conflitto con quello nuovo. Per evitare conflitti, pulire gli eventuali gruppi di risorse rimasti e le risorse associate prima di crearne uno nuovo.

1.	Nella scheda **Compilazione** selezionare il comando **Aggiungi istruzione di compilazione**.

2.	Selezionare **Utilità** > **PowerShell**.

3.	Selezionare l'icona a forma di matita accanto al nome dell'istruzione di compilazione e quindi rinominarla in **Rimuovere il gruppo di risorse cluster**.

4.	Selezionare il comando **…** accanto a **Nome file dello script**. Passare al percorso in cui sono stati estratti gli script di automazione e quindi selezionare **Remove-ClusterResourceGroup.ps1**.

5.	Per **Argomenti** immettere `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`.

6.	Salvare la definizione di compilazione.

### Aggiungere il passaggio "Effettuare il provisioning e distribuire in un cluster sicuro"

1.	Nella scheda **Compilazione** selezionare il comando **Aggiungi istruzione di compilazione**.

2.	Selezionare **Utilità** > **PowerShell**.

3.	Selezionare l'icona a forma di matita accanto al nome dell'istruzione di compilazione e quindi rinominarla in **Effettuare il provisioning e distribuire in un cluster sicuro**.

4.	Scegliere il pulsante **…** accanto a **Nome file dello script**. Passare al percorso in cui sono stati estratti gli script di automazione e quindi selezionare **ProvisionAndDeploy-SecureCluster.ps1**.

5.	Per **Argomenti** immettere `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`.

6.	Salvare la definizione di compilazione.

### Aggiungere il passaggio "Rimuovere il gruppo di risorse cluster"

A questo punto il cluster temporaneo è completato e occorre quindi pulirlo. In caso contrario continueranno a essere addebitati i costi corrispondenti. Questo passaggio rimuove il gruppo di risorse, che rimuove il cluster e tutte le altre risorse nel gruppo.

>[AZURE.NOTE] Esiste una differenza tra questo passaggio e il passaggio "Rimuovere il gruppo di risorse cluster" precedente: in questo caso deve essere selezionata l'opzione **Esegui sempre**.

1.	Nella scheda **Compilazione** selezionare il comando **Aggiungi istruzione di compilazione**.

2.	Selezionare **Utilità** > **PowerShell**.

3.	Selezionare l'icona a forma di matita accanto al nome dell'istruzione di compilazione e quindi rinominarla in **Rimuovere il gruppo di risorse cluster**.

4.	Scegliere il pulsante **…** accanto a **Nome file dello script**. Passare al percorso in cui sono stati estratti gli script di automazione e quindi selezionare **RemoveClusterResourceGroup.ps1**.

5.	Per **Argomenti** immettere `-ServicePrincipalPassword "$(ServicePrincipalPassword)`".

6.	In **Opzioni di controllo** selezionare la casella di controllo **Esegui sempre**.

7.	Salvare la definizione di compilazione.

### Prova

Selezionare **Accoda compilazione** per avviare una compilazione. Le compilazioni vengono attivate anche al momento del push o dell'archiviazione.


## Soluzioni alternative

Le istruzioni precedenti creano un nuovo cluster per ogni compilazione e lo rimuovono al termine dell'operazione. Se invece si preferisce che ogni compilazione esegua l'aggiornamento di un'applicazione, in un cluster esistente, seguire questa procedura:

1.	Creare manualmente un cluster di test tramite il portale di Azure o Azure PowerShell. Per questa operazione è possibile fare riferimento allo script `ProvisionAndDeploy-SecureCluster.ps1`.

2.	Configurare il profilo di pubblicazione per supportare l'aggiornamento dell'applicazione seguendo [queste istruzioni](service-fabric-visualstudio-configure-upgrade.md).

3.	Sostituire l'istruzione **Effettuare il provisioning e distribuire al cluster sicuro** con un'istruzione che chiama direttamente Deploy-FabricApplication.ps1 e la passa al profilo di pubblicazione.

4.	Rimuovere entrambe le istruzioni di compilazione **Rimuovere il gruppo di risorse cluster** dalla definizione di compilazione.

## Passaggi successivi

Per sapere di più sull'integrazione continua con applicazioni di Service Fabric, leggere gli articoli seguenti:

 - [Home page di documentazione sulla compilazione](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
 - [Articolo sulla distribuzione di un agente di compilazione](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
 - [Articolo sulla creazione e configurazione di una definizione di compilazione](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0406_2016-->