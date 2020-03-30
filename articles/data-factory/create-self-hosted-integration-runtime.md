---
title: Creare un runtime di integrazione self-hosted
description: Informazioni su come creare un runtime di integrazione self-hosted in Azure Data Factory, che consente alle data factory di accedere agli archivi dati in una rete privata.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 03/10/2020
ms.openlocfilehash: 6302a7d6ffe7218d339121ec98a624f8e98356f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065584"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Creare e configurare un runtime di integrazione self-hosted

Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per distribuire le funzionalità di integrazione di dati in ambienti di rete diversi. Per informazioni dettagliate sul runtime di integrazione, vedere [Runtime di integrazione in Azure Data Factory](concepts-integration-runtime.md).

Un runtime di integrazione self-hosted può eseguire attività di copia tra un archivio dati cloud e un archivio dati in una rete privata. Può anche inviare attività di trasformazione su risorse di calcolo in una rete locale o in una rete virtuale di Azure.It also can dispatch transform activities against compute resources in an on-premises network or an Azure virtual network. L'installazione di un runtime di integrazione self-hosted richiede una macchina locale o una macchina virtuale all'interno di una rete privata.  

In questo articolo viene descritto come creare e configurare un componente di accesso a motore self-hosted.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Configurazione di un runtime di integrazione self-hosted

Per creare e configurare un runtime di integrazione indipendente, utilizzare le procedure seguenti.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Creare un getto di rigenerazione indipendente tramite Azure PowerShellCreate a self-hosted IR via Azure PowerShell

1. È possibile usare Azure PowerShell per questa attività. Esempio:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Scaricare](https://www.microsoft.com/download/details.aspx?id=39717) e installare il runtime di integrazione self-hosted in un computer locale.

3. Recuperare la chiave di autenticazione e registrare il runtime di integrazione self-hosted con la chiave. Di seguito è illustrato un esempio di PowerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Creare un flusso di lavoro indipendente tramite l'interfaccia utente di Azure Data FactoryCreate a self-hosted IR via Azure Data Factory UI

Usare la procedura seguente per creare un metodo di recupero i /o più indipendente usando l'interfaccia utente di Azure Data Factory.Use the following steps to create a self-hosted IR using Azure Data Factory UI.

1. Nella pagina **Iniziamo** dell'interfaccia utente di Azure Data Factory selezionare la scheda **Autore** nel riquadro all'estrema sinistra.

   ![Pulsante Autore della home page](media/doc-common-process/get-started-page-author-button.png)

1. Selezionare Connessioni nella parte inferiore del riquadro all'estrema sinistra e selezionare Runtime di integrazione nella finestra Connessioni.Select **Connections** at the bottom of the left pane, and select **Integration runtimes** in the **Connections** window. Selezionare **Nuovo**.

   ![Creare un runtime di integrazione](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. Nella finestra **Impostazione runtime di integrazione** selezionare Esegui attività di spostamento e invio dei **dati a calcoli esterni**e **selezionare Continua**.

1. Immettere un nome per il file IR e selezionare **Crea**.

1. Selezionare il collegamento sotto **l'opzione 1** per aprire la configurazione rapida nel computer. In alternativa, seguire i passaggi descritti in **Opzione 2** per impostare manualmente. Le seguenti istruzioni si basano sulla configurazione manuale:

   ![Impostazione runtime di integrazione](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Copiare e incollare la chiave di autenticazione. Selezionare **Scarica e installa runtime di integrazione**.

    1. Scaricare il runtime di integrazione self-hosted in un computer Windows locale. Eseguire il programma di installazione.

    1. Nella pagina **Registra runtime di integrazione (self-hosted)** incollare la chiave salvata in precedenza e selezionare **Registra**.
    
       ![Registrare il runtime di integrazione](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Nella pagina **Nuovo nodo di Integration Runtime (self-hosted)** fare clic su **Fine**.

1. Dopo che il runtime di integrazione self-hosted è stato registrato correttamente, viene visualizzata la finestra seguente:After the self-hosted integration runtime is registered successfully, you see the following window:

    ![Registrazione completata](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Configurare un provider di disponibilità di un provider di disponibilità automatico in una macchina virtuale di Azure tramite un modello di Azure Resource ManagerSet up a self-hosted IR on an Azure VM via an Azure Resource Manager template

È possibile automatizzare la configurazione del metodo di gestione delle applicazioni a motore self-hosted in una macchina virtuale di Azure usando il [modello Crea iR self-host.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime) Il modello offre un modo semplice per avere un metodo di gestione delle applicazioni self-hosted completamente funzionale all'interno di una rete virtuale di Azure.The template provides an easy way to have a fully functional self-hosted IR inside an Azure virtual network. Il sistema di disponibilità elevata dispone di funzionalità di disponibilità elevata e scalabilità, purché si imposti il numero di nodi su 2 o su un valore superiore.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Configurare un componente di accesso aggiuntivo autoospitato esistente tramite PowerShell localeSet up an existing self-hosted IR via local PowerShell

È possibile utilizzare una riga di comando per impostare o gestire un componente di gestione autonomo esistente. Questo utilizzo può aiutare in particolare ad automatizzare l'installazione e la registrazione di nodi a distribuzione automatica.

Dmgcmd.exe è incluso nel programma di installazione self-hosted. In genere si trova nella cartella C:. Questa applicazione supporta vari parametri e può essere richiamata tramite una riga di comando utilizzando script batch per l'automazione.

Utilizzare l'applicazione come segue:

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

Di seguito sono riportati i dettagli dei parametri e delle proprietà dell'applicazione: 

| Proprietà                                                    | Descrizione                                                  | Obbligatoria |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegistraNuovoNodo** "`<AuthenticationKey>`"                     | Registrare un nodo di runtime di integrazione self-hosted con la chiave di autenticazione specificata. | No       |
| **RegistraNuovoNodo** "`<AuthenticationKey>`" "`<NodeName>`"      | Registrare un nodo di runtime di integrazione self-hosted con la chiave di autenticazione e il nome del nodo specificati. | No       |
| **EnableRemoteAccess (Abilitare L'accesso remoto)** "`<port>`" ["`<thumbprint>`"]            | Abilitare l'accesso remoto nel nodo corrente per configurare un cluster a disponibilità elevata. In alternativa, abilitare l'impostazione delle credenziali direttamente sul provider di informazioni self-hosted senza passare attraverso Azure Data Factory.Or enable setting credentials directly against the self-hosted IR without going through Azure Data Factory. Eseguire quest'ultima operazione utilizzando il cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** da un computer remoto nella stessa rete. | No       |
| **EnableRemoteAccessInContainer** "`<port>`" ["`<thumbprint>`"] | Abilitare l'accesso remoto al nodo corrente quando il nodo viene eseguito in un contenitore. | No       |
| **DisableRemoteAccess (DisabilitaAccessoRemoto)**                                         | Disabilitare l'accesso remoto al nodo corrente. L'accesso remoto è necessario per la configurazione multinodo. Il cmdlet **PowerShell New-AzDataFactoryV2LinkedServiceEncryptedCredential** funziona comunque anche quando l'accesso remoto è disabilitato. Questo comportamento è vero finché il cmdlet viene eseguito sullo stesso computer del nodo ARi self-hosted. | No       |
| **Chiave** "`<AuthenticationKey>`"                                 | Sovrascrivere o aggiornare la chiave di autenticazione precedente. Fate attenzione con questa azione. Il nodo a svelamento ospitato precedente può passare alla modalità offline se la chiave è di un nuovo runtime di integrazione. | No       |
| **GenerateBackupFile (File)** "`<filePath>`" "`<password>`"            | Generare un file di backup per il nodo corrente. Il file di backup include la chiave del nodo e le credenziali dell'archivio dati. | No       |
| **ImportBackupFile (File Di Importazione)** "`<filePath>`" "`<password>`"              | Ripristinare il nodo da un file di backup.                          | No       |
| **Riavvia**                                                     | Riavviare il servizio host runtime di integrazione self-hosted.   | No       |
| **Inizia**                                                       | Avviare il servizio host di runtime di integrazione self-hosted.     | No       |
| **Arresta**                                                        | Arrestare il servizio host di runtime di integrazione self-hosted.        | No       |
| **StartUpgradeService (StartUpgradeService)**                                         | Avviare il servizio di aggiornamento del runtime di integrazione self-hosted.       | No       |
| **StopUpgradeService**                                          | Arrestare il servizio di aggiornamento del runtime di integrazione self-hosted.        | No       |
| **TurnOnAutoUpdate (aggiornamento di TurnOnAuto)**                                            | Attivare l'aggiornamento automatico del runtime di integrazione self-hosted.        | No       |
| **TurnOffAutoUpdate (Aggiornamento automatico di TurnOff)**                                           | Disattivare l'aggiornamento automatico del runtime di integrazione self-hosted.       | No       |
| **SwitchServiceAccount (Account Servizio SwitchService)** "`<domain\user>`" ["`<password>`"]           | Impostare DIAHostService per l'esecuzione come nuovo account. Utilizzare la password vuota "" per gli account di sistema e gli account virtuali. | No       |


## <a name="command-flow-and-data-flow"></a>Flusso dei comandi e flusso di dati

Quando si spostano i dati tra l'ambiente locale e il cloud, l'attività usa un runtime di integrazione self-hosted per trasferire i dati tra un'origine dati locale e il cloud.

Ecco un riepilogo generale dei passaggi del flusso di dati per la copia con un componente di gestione di accesso autonomo:Here is a high-level summary of the data-flow steps for copying with a self-hosted IR:

![Panoramica generale del flusso di dati](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Uno sviluppatore di dati crea un runtime di integrazione self-hosted all'interno di una data factory di Azure usando un cmdlet di PowerShell.A data developer creates a self-hosted integration runtime within an Azure data factory by using a PowerShell cmdlet. Attualmente, il portale di Azure non supporta questa funzionalità.
1. Lo sviluppatore di dati crea un servizio collegato per un archivio dati locale. Lo sviluppatore esegue questa operazione specificando l'istanza del runtime di integrazione self-hosted che il servizio deve utilizzare per connettersi agli archivi dati.
1. Il nodo del runtime di integrazione self-hosted crittografa le credenziali con Data Protection API (DPAPI) e le salva in locale. Se più nodi vengono impostati per la disponibilità elevata, le credenziali vengono ulteriormente sincronizzate negli altri nodi. Ogni nodo crittografa le credenziali con DPAPI e le archivia in locale. La sincronizzazione delle credenziali è trasparente allo sviluppatore di dati e viene gestita dal runtime di integrazione self-hosted.
1. Azure Data Factory comunica con il runtime di integrazione self-hosted per pianificare e gestire i processi. La comunicazione avviene tramite un canale di controllo che usa una connessione di [inoltro bus](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) di servizio di Azure condivisa. Quando è necessario eseguire un processo di attività, Data Factory accoda la richiesta insieme a tutte le informazioni sulle credenziali. Lo fa nel caso in cui le credenziali non siano già archiviate nel runtime di integrazione self-hosted. Il runtime di integrazione self-hosted avvia il processo dopo il polling della coda.
1. Il runtime di integrazione self-hosted copia i dati tra un archivio locale e l'archiviazione cloud. La direzione della copia dipende dalla configurazione dell'attività di copia nella pipeline di dati. Per questo passaggio, il runtime di integrazione self-hosted comunica direttamente con i servizi di archiviazione basati su cloud come l'archiviazione BLOB di Azure tramite un canale HTTPS sicuro.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Considerazioni sull'uso del runtime di integrazione self-hosted

- È possibile usare un singolo runtime di integrazione self-hosted per più origini dati locali. È anche possibile condividerlo con un'altra data factory all'interno dello stesso tenant di Azure Active Directory (Azure AD). Per altre informazioni, vedere [Condivisione di un runtime di integrazione self-hosted](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory).
- È possibile installare una sola istanza di un runtime di integrazione self-hosted in qualsiasi singolo computer. Se si dispone di due data factory che devono accedere alle origini dati locali, utilizzare la funzionalità di [condivisione dei](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) ri-attività per condividere il flusso di integrazione self-hosted oppure installare il flusso di integrazione self-hosted in due computer locali, uno per ogni data factory.  
- Non è necessario che il runtime di integrazione self-hosted si trovara nello stesso computer dell'origine dati. Tuttavia, la presenza del runtime di integrazione self-hosted vicino all'origine dati riduce il tempo necessario al runtime di integrazione self-hosted per la connessione all'origine dati. È consigliabile installare il runtime di integrazione self-hosted in un computer diverso da quello che ospita l'origine dati locale. Quando il runtime di integrazione self-hosted e l'origine dati si trovano in computer diversi, il runtime di integrazione self-hosted non è in concorrenza con l'origine dati per le risorse.
- È possibile che più runtime di integrazione self-hosted siano presenti in computer diversi che si connettono alla stessa origine dati locale. Ad esempio, se si dispone di due runtime di integrazione self-hosted che servono due data factory, la stessa origine dati locale può essere registrata in entrambe le factory di dati.
- Se nel computer è già installato un gateway per la gestione di uno scenario di Power BI, installare un runtime di integrazione self-hosted separato per Data Factory in un altro computer.
- Usare un runtime di integrazione self-hosted per supportare l'integrazione dei dati all'interno di una rete virtuale di Azure.Use a self-hosted integration runtime to support data integration within an Azure virtual network.
- Considerare l'origine dati come un'origine dati locale protetta da firewall anche quando si usa Azure ExpressRoute. Usare il runtime di integrazione self-hosted per connettere il servizio all'origine dati.
- Usare il runtime di integrazione self-hosted anche se l'archivio dati si trova nel cloud in una macchina virtuale Azure Infrastructure as a Service (IaaS).
- Le attività potrebbero non riuscire in un runtime di integrazione self-hosted installato in un server Windows per il quale è abilitata la crittografia FIPS compatibile. Per risolvere questo problema, disabilitare la crittografia FIPS nel server. Per disabilitare la crittografia FIPS compatibile con FIPS, modificare il valore `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`della seguente sottochiave del Registro di sistema da 1 (abilitato) a 0 (disabilitato): .

## <a name="prerequisites"></a>Prerequisiti

- Le versioni supportate di Windows sono:
  + Windows 7 Service Pack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   L'installazione del runtime di integrazione self-hosted in un controller di dominio non è supportata.
- È necessario .NET Framework 4.6.1 o versione successiva. Se si installa il runtime di integrazione self-hosted in un computer Windows 7, installare .NET Framework 4.6.1 o versione successiva. Per informazioni dettagliate, vedere [Requisiti di sistema di .NET Framework](/dotnet/framework/get-started/system-requirements) .
- La configurazione minima consigliata per il computer runtime di integrazione self-hosted è un processore da 2 GHz con 4 core, 8 GB di RAM e 80 GB di spazio disponibile sul disco rigido.
- Se il computer host esegue l'ibernazione, il runtime di integrazione self-hosted non risponde alle richieste di dati. Configurare una combinazione appropriata per il risparmio di energia nel computer prima di installare il runtime di integrazione self-hosted. Se il computer è configurato per l'ibernazione, il programma di installazione del runtime di integrazione self-hosted richiede un messaggio.
- Per installare e configurare correttamente il runtime di integrazione self-hosted, è necessario essere un amministratore del computer.
- Le esecuzioni di attività di copia avvengono con una frequenza specifica. L'utilizzo del processore e della RAM sulla macchina segue lo stesso modello con i tempi di picco e di inattività. L'utilizzo delle risorse dipende anche in larga misura dalla quantità di dati spostati. Quando sono in corso più processi di copia, l'utilizzo delle risorse aumenta durante i periodi di picco.
- Le attività potrebbero non riuscire durante l'estrazione di dati nei formati Parquet, ORC o Avro. Per altre informazioni sul formato Parquet, vedere [Formato parquet in Azure Data Factory.For](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)more on Parquet, see Parquet format in Azure Data Factory . La creazione di file viene eseguita nel computer di integrazione self-hosted. Per funzionare come previsto, la creazione di file richiede i prerequisiti seguenti:
    - [Ridistribuibile](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) in Visual C Pacchetto (x64)
    - Java Runtime (JRE) versione 8 da un provider JRE come [Adopt OpenJDK](https://adoptopenjdk.net/). Assicurarsi `JAVA_HOME` che la variabile di ambiente sia impostata.

## <a name="installation-best-practices"></a>Procedure consigliate per l'installazione

È possibile installare il runtime di integrazione self-hosted scaricando un pacchetto di installazione di Identità gestite [dall'Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Vedere l'articolo Spostare i [dati tra l'ambiente locale e il cloud](tutorial-hybrid-copy-powershell.md) per istruzioni dettagliate.

- Configurare una plan per il risparmio di energia nel computer host per il runtime di integrazione self-hosted in modo che il computer non ibernazioni. Se il computer host entra in stato di ibernazione, il runtime di integrazione self-hosted passa in modalità offline.
- Eseguire regolarmente il backup delle credenziali associate al runtime di integrazione self-hosted.
- Per automatizzare le operazioni di configurazione iR self-hosted, fare riferimento a [Configurare un iR self-hosted esistente tramite PowerShell](#setting-up-a-self-hosted-integration-runtime).  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Installare e registrare un componente di ri-azione indipendente dall'Area download Microsoft

1. Accedere alla [pagina di download di Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
1. Selezionare **Scarica**, selezionare la versione a 64 bit e scegliere **Avanti**. La versione a 32 bit non è supportata.
1. Eseguire direttamente il file identità gestita o salvarlo sul disco rigido ed eseguirlo.
1. Nella finestra **di benvenuto,** selezionare una lingua e scegliere **Avanti**.
1. Accettare le condizioni di licenza software Microsoft e quindi selezionare **Avanti**.
1. Selezionare la **cartella** in cui installare il runtime di integrazione self-hosted e quindi selezionare **Avanti**.
1. Nella pagina **Pronto per l'installazione** selezionare **Installa**.
1. Selezionare **Fine** per completare l'installazione.
1. Ottenere la chiave di autenticazione tramite PowerShell.Get the authentication key by using PowerShell. Di seguito viene indicato un esempio di PowerShell per recuperare la chiave di autenticazione:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. Nella finestra **Registra runtime di integrazione (self-hosted)** di Microsoft Integration Runtime Configuration Manager in esecuzione nel computer, eseguire le operazioni seguenti:

    1. Incollare la chiave di autenticazione nell'area di testo.

    1. Facoltativamente, selezionare **Mostra chiave di autenticazione** per visualizzare il testo della chiave.

    1. Selezionare **Registra**.

## <a name="high-availability-and-scalability"></a>Disponibilità elevata e scalabilità

È possibile associare un runtime di integrazione self-hosted a più macchine locali o macchine virtuali in Azure.You can associate a self-hosted integration runtime with multiple on-premises machines or virtual machines in Azure. Questi computer sono chiamati nodi. È possibile avere fino a quattro nodi associati a un runtime di integrazione self-hosted. I vantaggi della presenza di più nodi in computer locali in cui è installato un gateway per un gateway logico sono:

* Maggiore disponibilità del runtime di integrazione self-hosted in modo che non sia più il singolo punto di errore nella soluzione Big Data o nell'integrazione dei dati cloud con Data Factory. Questa disponibilità consente di garantire la continuità quando si utilizzano fino a quattro nodi.
* Miglioramento delle prestazioni e della velocità effettiva durante lo spostamento dati tra archivi dati locali e cloud. Ottenere altre informazioni sui [confronti delle prestazioni](copy-activity-performance.md).

È possibile associare più nodi installando il software runtime di integrazione self-hosted [dall'Area download](https://www.microsoft.com/download/details.aspx?id=39717). Quindi, registrarlo utilizzando una delle chiavi di autenticazione ottenute dal cmdlet **New-AzDataFactoryV2IntegrationRuntimeKey** , come descritto [nell'esercitazione](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Non è necessario creare un nuovo runtime di integrazione self-hosted per associare ogni nodo. È possibile installare il runtime di integrazione self-hosted in un altro computer e registrarlo con la stessa chiave di autenticazione.

> [!NOTE]
> Prima di aggiungere un altro nodo per la disponibilità elevata e la scalabilità, assicurarsi che l'opzione **Accesso remoto a Intranet** sia abilitata nel primo nodo. A tale scopo, selezionare Microsoft **Integration Runtime Configuration Manager** > **Impostazioni** > **Accesso remoto alla rete Intranet**.

### <a name="scale-considerations"></a>Considerazioni sulla scalabilità

#### <a name="scale-out"></a>Scalabilità orizzontale

Quando l'utilizzo del processore è elevato e la memoria disponibile è insufficiente per il componente di gioco interno self-hosted, aggiungere un nuovo nodo per aumentare il carico tra i computer. Se le attività non riescono perché scadono o il nodo a svelamento automatico è offline, è utile se si aggiunge un nodo al gateway.

#### <a name="scale-up"></a>Aumentare le prestazioni

Quando il processore e la RAM disponibile non sono ben utilizzati, ma l'esecuzione di processi simultanei raggiunge i limiti di un nodo, aumentare aumentando il numero di processi simultanei che un nodo può eseguire. È anche possibile aumentare le dimensioni quando si verifica il timeout delle attività perché il runtime di crisi dei prodotti irattività self-hosted è sovraccarico. Come illustrato nell'immagine seguente, è possibile aumentare la capacità massima di un nodo:  

![Aumentare il numero di processi simultanei che possono essere eseguiti in un nodo](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Requisiti del certificato TLS/SSL

Di seguito sono riportati i requisiti per il certificato TLS/SSL utilizzato per proteggere la comunicazione tra i nodi di runtime di integrazione:

- Deve essere un certificato X509 v3 pubblicamente attendibile. È consigliabile utilizzare i certificati emessi da un'autorità di certificazione (CA) del partner pubblico.
- Ogni nodo del runtime di integrazione deve considerare attendibile questo certificato.
- Non è consigliabile utilizzare i certificati SAN (Subject Alternative Name) perché viene utilizzato solo l'ultimo elemento SAN. Tutti gli altri elementi SAN vengono ignorati. Ad esempio, se si dispone di un certificato SAN i cui RETI SAN sono **node1.domain.contoso.com** e **node2.domain.contoso.com**, è possibile utilizzare il certificato solo in un computer il cui nome di dominio completo (FQDN) è **node2.domain.contoso.com**.
- Il certificato può utilizzare qualsiasi dimensione di chiave supportata da Windows Server 2012 R2 per i certificati TLS/SSL.
- I certificati che utilizzano chiavi CNG non sono supportati.  

> [!NOTE]
> Questo certificato viene utilizzato:
>
> - Per crittografare le porte in un nodo a iR auto-ospitato.
> - Per la comunicazione da nodo a nodo per la sincronizzazione dello stato, che include la sincronizzazione delle credenziali dei servizi collegati tra i nodi.
> - Quando un cmdlet di PowerShell viene utilizzato per le impostazioni delle credenziali del servizio collegato dall'interno di una rete locale.
>
> Si consiglia di utilizzare questo certificato se l'ambiente di rete privata non è sicuro o se si desidera proteggere la comunicazione tra i nodi all'interno della rete privata.
>
> Lo spostamento dei dati in transito da un ir-intermediamento autonomo ad altri archivi dati avviene sempre all'interno di un canale crittografato, indipendentemente dal fatto che il certificato sia impostato o meno.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Creare un runtime di integrazione self-hosted condiviso in Azure Data FactoryCreate a shared self-hosted integration runtime in Azure Data Factory

È possibile usare nuovamente un'infrastruttura del runtime di integrazione self-hosted esistente già configurata in una data factory. Questo riutilizzo consente di creare un runtime di integrazione self-hosted collegato in una data factory diversa facendo riferimento a un runtime di integrazione self-hosted condiviso esistente.

Per un'introduzione e una dimostrazione di questa funzionalità, guardare il seguente video di 12 minuti:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **IR condiviso**: un provider di servizi di cassette postali originale che viene eseguito in un'infrastruttura fisica.  
- **IR collegato**: un iR che fa riferimento a un altro IR condiviso. Il rinisso collegato è un iR logico e utilizza l'infrastruttura di un altro iR indipendente.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Metodi per condividere un runtime di integrazione self-hosted

Per condividere un runtime di integrazione self-hosted con più data factory, vedere Creare un runtime di [integrazione self-hosted condiviso](create-shared-self-hosted-integration-runtime-powershell.md) per i dettagli.

### <a name="monitoring"></a>Monitoraggio

#### <a name="shared-ir"></a>IR condiviso

![Selezioni per trovare un runtime di integrazione condiviso](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Monitorare un runtime di integrazione condivisoMonitor a shared integration runtime](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>IR collegato

![Selezioni per trovare un runtime di integrazione collegato](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Monitorare un runtime di integrazione collegataMonitor a linked integration runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitazioni note della condivisione del runtime di integrazione self-hosted

* La data factory in cui viene creato un componente di riorazione collegato deve avere [un'identità gestita.](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) Per impostazione predefinita, le data factory create nel portale di Azure o nei cmdlet di PowerShell hanno un'identità gestita creata in modo implicito. Tuttavia, quando una data factory viene creata tramite un modello o un SDK di Azure Resource Manager, è necessario impostare la proprietà **Identity** in modo esplicito. Questa impostazione garantisce che Resource Manager crei una data factory contenente un'identità gestita.

* Data Factory .NET SDK che supporta questa funzionalità deve essere versione 1.1.0 o successiva.

* Per concedere l'autorizzazione, è necessario il ruolo Proprietario o proprietario ereditato nella data factory in cui è presente il provider di servizi di gestione delle risorse multimediali.

* La funzionalità di condivisione funziona solo per le data factory all'interno dello stesso tenant di Azure AD.

* Per [gli utenti guest](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)di Azure AD, la funzionalità di ricerca nell'interfaccia utente, che elenca tutte le data factory tramite una parola chiave di ricerca, non [funziona.](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits) Tuttavia, finché l'utente guest è il proprietario della data factory, è possibile condividere il file IR senza la funzionalità di ricerca. Per l'identità gestita della data factory che deve condividere il flusso di proprietà, immettere tale identità gestita nella casella **Assegna autorizzazione** e selezionare **Aggiungi** nell'interfaccia utente di Data Factory.

  > [!NOTE]
  > Questa funzione è disponibile solo in Data Factory V2.

## <a name="notification-area-icons-and-notifications"></a>Icone e notifiche dell'area di notifica

Se si sposta il cursore sull'icona o sul messaggio nell'area di notifica, è possibile visualizzare i dettagli sullo stato del runtime di integrazione ospitato.

![Notifiche nell'area di notifica](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>Porte e firewall

Ci sono due firewall da considerare:

- Il *firewall aziendale* in esecuzione sul router centrale dell'organizzazione
- Il firewall di *Windows* configurato come daemon nel computer locale in cui è installato il runtime di integrazione self-hosted

![I firewall](media/create-self-hosted-integration-runtime/firewall.png)

A livello di firewall aziendale è necessario configurare le porte in uscita e i domini seguenti:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

A livello di windows firewall o computer, queste porte in uscita sono normalmente abilitate. In caso contrario, è possibile configurare i domini e le porte in un computer di runtime di integrazione self-hosted.

> [!NOTE]
> In base all'origine e ai sink, potrebbe essere necessario consentire ulteriori domini e porte in uscita nel firewall aziendale o nel firewall di Windows.
>
> Per alcuni database cloud, ad esempio Il database SQL di Azure e Il data lake di Azure, potrebbe essere necessario consentire gli indirizzi IP dei computer runtime di integrazione autonomi nella configurazione del firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiare i dati da un'origine a un sink

Assicurarsi di abilitare correttamente le regole del firewall nel firewall aziendale, nel firewall di Windows del computer runtime di integrazione self-hosted e nell'archivio dati stesso. L'abilitazione di queste regole consente al runtime di integrazione self-hosted di connettersi correttamente sia all'origine che al sink. Abilitare le regole per ogni archivio dati interessato dall'operazione di copia.

Ad esempio, per copiare da un archivio dati locale a un sink del database SQL o a un sink di Data Warehouse SQL di Azure, eseguire la procedura seguente:For example, to copy from an on-premises data store to an SQL Database sink or an Azure SQL Data Warehouse sink, take the following steps:

1. Consentire la comunicazione TCP in uscita sulla porta 1433 sia per il firewall di Windows che per il firewall aziendale.
1. Configurare le impostazioni del firewall del database SQL per aggiungere l'indirizzo IP del computer runtime di integrazione self-hosted all'elenco degli indirizzi IP consentiti.

> [!NOTE]
> Se il firewall non consente la porta in uscita 1433, il runtime di integrazione self-hosted non può accedere direttamente al database SQL. In questo caso, è possibile utilizzare una [copia in fasi](copy-activity-performance.md) nel database SQL e nel data warehouse SQL. In questo scenario, è necessario solo HTTPS (porta 443) per lo spostamento dei dati.

## <a name="proxy-server-considerations"></a>Considerazioni sui server proxy

Se nell'ambiente di rete aziendale è presente un server proxy per accedere a Internet, configurare il runtime di integrazione self-hosted per usare le impostazioni proxy appropriate. È possibile impostare il proxy durante la fase di registrazione iniziale.

![Specificare il proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Se configurato, il runtime di integrazione self-hosted utilizza il server proxy per connettersi all'origine e alla destinazione del servizio cloud (che utilizzano il protocollo HTTP o HTTPS). Questo è il motivo per cui si seleziona **Cambia collegamento** durante la configurazione iniziale.

![Impostare il proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Sono disponibili tre opzioni di configurazione:

- **Non usare proxy:** il runtime di integrazione self-hosted non utilizza in modo esplicito alcun proxy per connettersi ai servizi cloud.
- Usa proxy di **sistema:** il runtime di integrazione self-hosted utilizza l'impostazione proxy configurata in diahost.exe.config e diawp.exe.config. Se questi file non specificano alcuna configurazione proxy, il runtime di integrazione self-hosted si connette direttamente al servizio cloud senza passare attraverso un proxy.
- **Usa proxy personalizzato**: Configurare l'impostazione proxy HTTP da utilizzare per il runtime di integrazione self-hosted, anziché utilizzare le configurazioni in diahost.exe.config e diawp.exe.config. **I** valori Address e **Port** sono obbligatori. **I** valori Nome utente e **Password** sono facoltativi, a seconda dell'impostazione di autenticazione del proxy. Tutte le impostazioni vengono crittografate con Windows DPAPI nel runtime di integrazione self-hosted e archiviate localmente nel computer.

Il servizio host runtime di integrazione viene riavviato automaticamente dopo aver salvato le impostazioni proxy aggiornate.

Dopo aver registrato il runtime di integrazione self-hosted, se si desidera visualizzare o aggiornare le impostazioni proxy, usare Gestione configurazione Microsoft Integration Runtime.

1. Aprire **Gestione configurazione di Microsoft Integration Runtime**.
1. Selezionare la scheda **Impostazioni.**
1. In **Proxy HTTP**selezionare il collegamento **Cambia** per aprire la finestra di dialogo Imposta **proxy HTTP.**
1. Fare clic su **Avanti**. Viene quindi visualizzato un avviso che richiede l'autorizzazione per salvare l'impostazione proxy e riavviare il servizio host del runtime di integrazione.

È possibile utilizzare lo strumento di gestione della configurazione per visualizzare e aggiornare il proxy HTTP.

![Visualizzare e aggiornare il proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Se si configura un server proxy con l'autenticazione NTLM, il servizio host del runtime di integrazione viene eseguito con l'account di dominio. Se in seguito si modifica la password per l'account di dominio, ricordarsi di aggiornare le impostazioni di configurazione per il servizio e riavviare il servizio. A causa di questo requisito, si consiglia di accedere al server proxy utilizzando un account di dominio dedicato che non richiede l'aggiornamento frequente della password.

### <a name="configure-proxy-server-settings"></a>Configurare le impostazioni del server proxy

Se si seleziona l'opzione **Usa proxy** di sistema per il proxy HTTP, il runtime di integrazione self-hosted utilizza le impostazioni proxy in diahost.exe.config e diawp.exe.config. Quando questi file non specificano alcun proxy, il runtime di integrazione self-hosted si connette direttamente al servizio cloud senza passare attraverso un proxy. La procedura seguente contiene le istruzioni per aggiornare il file diahost.exe.config:

1. In Esplora file, creare come backup del file originale il file di backup del file originale in Esplora file.
1. Aprire Blocco note in esecuzione come amministratore.
1. Nel Blocco note, aprire il file di testo C:.
1. Individuare il tag system.net predefinito come illustrato nel codice seguente:Find the default **system.net** tag as shown in the following code:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    È quindi possibile aggiungere i dettagli del server proxy, come illustrato nell'esempio seguente:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Il tag proxy consente proprietà aggiuntive `scriptLocation`per specificare le impostazioni necessarie come . Vedere [ \<\> elemento proxy (impostazioni di rete)](https://msdn.microsoft.com/library/sa91de1e.aspx) per la sintassi.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. Salvare il file di configurazione nella posizione originale. Riavviare quindi il servizio host del runtime di integrazione self-hosted, che rileva le modifiche.

   Per riavviare il servizio, utilizzare l'applet dei servizi dal Pannello di controllo. In alternativa, in Gestione configurazione di Integration Runtime selezionare il pulsante **Arresta servizio** e quindi selezionare **Avvia servizio**.

   Se il servizio non si avvia, è probabile che nel file di configurazione dell'applicazione modificato sia stata aggiunta una sintassi di tag XML non corretta.

> [!IMPORTANT]
> Non dimenticare di aggiornare entrambi i file diahost.exe.config e diawp.exe.config.

È inoltre necessario assicurarsi che Microsoft Azure sia incluso nell'elenco Consenti dell'azienda. È possibile scaricare l'elenco di indirizzi IP di Azure validi [dall'Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Possibili sintomi per problemi relativi al firewall e al server proxy

Se vengono visualizzati messaggi di errore simili ai seguenti, il motivo probabile è la configurazione non corretta del firewall o del server proxy. Tale configurazione impedisce al runtime di integrazione self-hosted di connettersi a Data Factory per autenticarsi. Per verificare che la configurazione del firewall e del server proxy sia corretta, vedere la sezione precedente.

* Quando si tenta di registrare il runtime di integrazione self-hosted, viene visualizzato il seguente messaggio di errore: "Impossibile registrare il nodo di runtime di integrazione! Verificare che la chiave di autenticazione sia valida e che il servizio host del servizio di integrazione sia in esecuzione in questo computer."
* Quando si apre Gestione configurazione di Integration Runtime, lo stato del gateway visualizzato può essere **Disconnesso** o **Connessione**. Quando si visualizzano i registri eventi di Windows, > in Registri applicazioni e servizi del **Visualizzatore** > **eventi**Microsoft Integration**Runtime**vengono visualizzati messaggi di errore simili ai seguente:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>Abilitare l'accesso remoto da una rete IntranetEnable remote access from an intranet

Se si usa PowerShell per crittografare le credenziali da un computer in rete diverso da quello in cui è stato installato il runtime di integrazione self-hosted, è possibile abilitare l'opzione **Accesso remoto da Intranet.** Se si esegue PowerShell per crittografare le credenziali nel computer in cui è stato installato il runtime di integrazione self-hosted, non è possibile abilitare **Accesso remoto da Intranet**.

Abilitare **Accesso remoto da Intranet** prima di aggiungere un altro nodo per la disponibilità elevata e la scalabilità.  

Quando si esegue la versione 3.3 o successiva del runtime di integrazione self-hosted, per impostazione predefinita il programma di installazione del runtime di integrazione self-hosted disabilita **L'accesso remoto da Intranet** nel computer di runtime di integrazione self-hosted.

Quando si utilizza un firewall di un partner o di altri, è possibile aprire manualmente la porta 8060 o la porta configurata dall'utente. Se si verifica un problema con il firewall durante la configurazione del runtime di integrazione self-hosted, utilizzare il comando seguente per installare il runtime di integrazione self-hosted senza configurare il firewall:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Se si sceglie di non aprire la porta 8060 nel computer di runtime di integrazione self-hosted, utilizzare meccanismi diversi dall'applicazione Impostazione credenziali per configurare le credenziali dell'archivio dati. Ad esempio, è possibile utilizzare il cmdlet **PowerShell New-AzDataFactoryV2LinkedServiceEncryptCredential.**

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni dettagliate, vedere [Esercitazione: Copiare dati locali](tutorial-hybrid-copy-powershell.md)nel cloud.
