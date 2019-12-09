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
ms.date: 06/18/2019
ms.openlocfilehash: 0d04ea7d7003f274b252e057b7afced7759bfaae
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928517"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Creare e configurare un runtime di integrazione self-hosted

Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per distribuire le funzionalità di integrazione di dati in ambienti di rete diversi. Per informazioni dettagliate sul runtime di integrazione, vedere [Runtime di integrazione in Azure Data Factory](concepts-integration-runtime.md).

Un runtime di integrazione self-hosted può eseguire attività di copia tra un archivio dati cloud e un archivio dati in una rete privata. Può anche inviare le attività di trasformazione sulle risorse di calcolo in una rete locale o in una rete virtuale di Azure. Per l'installazione di un runtime di integrazione self-hosted è necessario un computer locale o una macchina virtuale all'interno di una rete privata.  

Questo articolo descrive come creare e configurare un runtime di integrazione self-hosted.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Configurazione di un runtime di integrazione self-hosted

Per creare e configurare un runtime di integrazione self-hosted, attenersi alle procedure riportate di seguito.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Creare un runtime di integrazione self-hosted tramite Azure PowerShell

1. Per questa attività è possibile usare Azure PowerShell. Di seguito è fornito un esempio:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Scaricare](https://www.microsoft.com/download/details.aspx?id=39717) e installare il runtime di integrazione self-hosted in un computer locale.

3. Recuperare la chiave di autenticazione e registrare il runtime di integrazione self-hosted con la chiave. Di seguito è illustrato un esempio di PowerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Creare un runtime di integrazione self-hosted tramite Azure Data Factory interfaccia utente

Usare la procedura seguente per creare un runtime di integrazione self-hosted usando Azure Data Factory interfaccia utente.

1. Nella pagina attività **iniziali** dell'interfaccia utente di Azure Data Factory selezionare la scheda **autore** nel riquadro più a sinistra.

   ![Pulsante home page author](media/doc-common-process/get-started-page-author-button.png)

1. Selezionare **connessioni** nella parte inferiore del riquadro a sinistra e selezionare **runtime di integrazione** nella finestra **connessioni** . Selezionare **+ nuovo**.

   ![Creare un runtime di integrazione](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. Nella finestra di **dialogo di installazione di Integration Runtime** selezionare **Esegui attività di spostamento e invio dati in calcoli esterni**e selezionare **continua**.

1. Immettere un nome per il runtime di integrazione e selezionare **Crea**.

1. Selezionare il collegamento nell' **opzione 1** per aprire l'installazione rapida nel computer. In alternativa, attenersi alla procedura descritta nell' **opzione 2** per configurare manualmente. Le istruzioni seguenti si basano sull'installazione manuale:

   ![Impostazione runtime di integrazione](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Copiare e incollare la chiave di autenticazione. Selezionare **scaricare e installare Integration Runtime**.

    1. Scaricare il runtime di integrazione self-hosted in un computer Windows locale. Eseguire il programma di installazione.

    1. Nella pagina **register Integration Runtime (self-hosted)** incollare la chiave salvata in precedenza e selezionare **Register**.
    
       ![Registrare il runtime di integrazione](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Nella pagina **Nuovo nodo di Integration Runtime (self-hosted)** fare clic su **Fine**.

1. Una volta completata la registrazione del runtime di integrazione self-hosted, viene visualizzata la finestra seguente:

    ![Registrazione completata](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Configurare un runtime di integrazione self-hosted in una macchina virtuale di Azure tramite un modello di Azure Resource Manager

È possibile automatizzare la configurazione del runtime di integrazione self-hosted in una macchina virtuale di Azure usando il [modello di creazione host self-service](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Il modello fornisce un modo semplice per avere un runtime di integrazione self-hosted completamente funzionante all'interno di una rete virtuale di Azure. Il runtime di integrazione offre funzionalità a disponibilità elevata e scalabilità, purché il numero di nodi sia impostato su 2 o su un valore superiore.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Configurare un runtime di integrazione self-hosted esistente tramite PowerShell locale

È possibile usare una riga di comando per configurare o gestire un runtime di integrazione self-hosted esistente. Questo utilizzo può essere particolarmente utile per automatizzare l'installazione e la registrazione dei nodi IR indipendenti.

Dmgcmd. exe è incluso nel programma di installazione self-hosted. Si trova in genere nella cartella C:\Programmi\Microsoft Integration Runtime\3.0\Shared\ Questa applicazione supporta diversi parametri e può essere richiamata tramite una riga di comando tramite script batch per l'automazione.

Usare l'applicazione come segue:

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

Ecco i dettagli dei parametri e delle proprietà dell'applicazione: 

| Proprietà                                                    | Description                                                  | Obbligatoria |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegisterNewNode** "`<AuthenticationKey>`"                     | Registrare un nodo del runtime di integrazione self-hosted con la chiave di autenticazione specificata. | No       |
| **RegisterNewNode** "`<AuthenticationKey>`" "`<NodeName>`"      | Registrare un nodo del runtime di integrazione self-hosted con la chiave di autenticazione e il nome del nodo specificati. | No       |
| **EnableRemoteAccess** "`<port>`" ["`<thumbprint>`"]            | Abilitare l'accesso remoto nel nodo corrente per configurare un cluster a disponibilità elevata. In alternativa, abilitare l'impostazione delle credenziali direttamente sul runtime di integrazione Self-Hosted senza passare attraverso Azure Data Factory. Per eseguire quest'ultima operazione, usare il cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** da un computer remoto nella stessa rete. | No       |
| **EnableRemoteAccessInContainer** "`<port>`" ["`<thumbprint>`"] | Abilitare l'accesso remoto al nodo corrente quando il nodo viene eseguito in un contenitore. | No       |
| **DisableRemoteAccess**                                         | Disabilitare l'accesso remoto al nodo corrente. Per la configurazione a più nodi è necessario l'accesso remoto. Il cmdlet **di PowerShell New-AzDataFactoryV2LinkedServiceEncryptedCredential** funziona ancora anche quando l'accesso remoto è disabilitato. Questo comportamento è true purché il cmdlet venga eseguito nello stesso computer del nodo IR self-hosted. | No       |
| **Chiave** di "`<AuthenticationKey>`"                                 | Sovrascrivere o aggiornare la chiave di autenticazione precedente. Prestare attenzione a questa azione. Il nodo del runtime di integrazione self-hosted precedente può passare offline se la chiave è di un nuovo runtime di integrazione. | No       |
| **GenerateBackupFile** "`<filePath>`" "`<password>`"            | Genera un file di backup per il nodo corrente. Il file di backup include la chiave del nodo e le credenziali dell'archivio dati. | No       |
| **ImportBackupFile** "`<filePath>`" "`<password>`"              | Ripristinare il nodo da un file di backup.                          | No       |
| **Riavviare**                                                     | Riavviare il servizio host del runtime di integrazione self-hosted.   | No       |
| **Inizia**                                                       | Avviare il servizio host del runtime di integrazione self-hosted.     | No       |
| **Stop**                                                        | Arrestare il servizio host del runtime di integrazione self-hosted.        | No       |
| **StartUpgradeService**                                         | Avviare il servizio di aggiornamento del runtime di integrazione self-hosted.       | No       |
| **StopUpgradeService**                                          | Arrestare il servizio di aggiornamento del runtime di integrazione self-hosted.        | No       |
| **TurnOnAutoUpdate**                                            | Attivare l'aggiornamento automatico del runtime di integrazione self-hosted.        | No       |
| **TurnOffAutoUpdate**                                           | Disattivare l'aggiornamento automatico del runtime di integrazione self-hosted.       | No       |
| **SwitchServiceAccount** "`<domain\user>`" ["`<password>`"]           | Impostare DIAHostService per l'esecuzione come nuovo account. Utilizzare la password vuota "" per gli account di sistema e gli account virtuali. | No       |


## <a name="command-flow-and-data-flow"></a>Flusso dei comandi e flusso di dati

Quando si spostano i dati tra l'ambiente locale e il cloud, l'attività usa un runtime di integrazione self-hosted per trasferire i dati tra un'origine dati locale e il cloud.

Di seguito è riportato un riepilogo di alto livello dei passaggi del flusso di dati per la copia con un runtime di integrazione self-hosted:

![Panoramica di alto livello del flusso di dati](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Uno sviluppatore di dati crea un runtime di integrazione self-hosted in un data factory di Azure usando un cmdlet di PowerShell. Attualmente, il portale di Azure non supporta questa funzionalità.
1. Lo sviluppatore di dati crea un servizio collegato per un archivio dati locale. Lo sviluppatore specifica l'istanza del runtime di integrazione self-hosted che il servizio deve usare per connettersi agli archivi dati.
1. Il nodo del runtime di integrazione self-hosted crittografa le credenziali con Data Protection API (DPAPI) e le salva in locale. Se più nodi vengono impostati per la disponibilità elevata, le credenziali vengono ulteriormente sincronizzate negli altri nodi. Ogni nodo crittografa le credenziali con DPAPI e le archivia in locale. La sincronizzazione delle credenziali è trasparente allo sviluppatore di dati e viene gestita dal runtime di integrazione self-hosted.
1. Azure Data Factory comunica con il runtime di integrazione self-hosted per pianificare e gestire i processi. La comunicazione avviene tramite un canale di controllo che usa una connessione condivisa di [inoltro del bus di servizio di Azure](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) . Quando è necessario eseguire un processo di attività, Data Factory Accoda la richiesta insieme a tutte le informazioni sulle credenziali. Questa operazione viene eseguita nel caso in cui le credenziali non siano già archiviate nel runtime di integrazione self-hosted. Il runtime di integrazione self-hosted avvia il processo dopo il polling della coda.
1. Il runtime di integrazione self-hosted copia i dati tra un archivio locale e l'archiviazione cloud. La direzione della copia dipende dalla modalità di configurazione dell'attività di copia nella pipeline di dati. Per questo passaggio, il runtime di integrazione self-hosted comunica direttamente con i servizi di archiviazione basati sul cloud, ad esempio archiviazione BLOB di Azure, tramite un canale HTTPS sicuro.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Considerazioni sull'uso del runtime di integrazione self-hosted

- È possibile usare un singolo runtime di integrazione self-hosted per più origini dati locali. È anche possibile condividerlo con un altro data factory nello stesso tenant di Azure Active Directory (Azure AD). Per altre informazioni, vedere [Condivisione di un runtime di integrazione self-hosted](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory).
- È possibile installare una sola istanza di un runtime di integrazione self-hosted in un singolo computer. Se si hanno due data factory che devono accedere alle origini dati locali, usare la [funzionalità di condivisione IR self-hosted](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) per condividere il runtime di integrazione self-hosted o installare il runtime di integrazione self-hosted in due computer locali, uno per ogni data factory.  
- Non è necessario che il runtime di integrazione self-hosted si trovi nello stesso computer dell'origine dati. Tuttavia, la presenza del runtime di integrazione self-hosted vicino all'origine dati riduce il tempo necessario per la connessione del runtime di integrazione self-hosted all'origine dati. Si consiglia di installare il runtime di integrazione self-hosted in un computer diverso da quello che ospita l'origine dati locale. Quando il runtime di integrazione self-hosted e l'origine dati si trovano in computer diversi, il runtime di integrazione self-hosted non compete con l'origine dati per le risorse.
- È possibile che più runtime di integrazione self-hosted siano presenti in computer diversi che si connettono alla stessa origine dati locale. Se, ad esempio, sono presenti due runtime di integrazione self-hosted che servono due data factory, la stessa origine dati locale può essere registrata con entrambe le data factory.
- Se nel computer è già installato un gateway per gestire uno scenario di Power BI, installare un runtime di integrazione self-hosted separato per Data Factory in un altro computer.
- Usare un runtime di integrazione self-hosted per supportare l'integrazione dei dati all'interno di una rete virtuale di Azure.
- Considerare l'origine dati come un'origine dati locale protetta da firewall anche quando si usa Azure ExpressRoute. Usare il runtime di integrazione self-hosted per connettere il servizio all'origine dati.
- Usare il runtime di integrazione self-hosted anche se l'archivio dati si trova nel cloud in una macchina virtuale di infrastruttura distribuita come servizio (IaaS) di Azure.
- Le attività potrebbero non riuscire in un runtime di integrazione self-hosted installato in un server Windows per cui è abilitata la crittografia FIPS compatibile. Per risolvere questo problema, disabilitare la crittografia FIPS nel server. Per disabilitare la crittografia conforme a FIPS, modificare il valore della sottochiave del registro di sistema seguente da 1 (abilitato) a 0 (disabilitato): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Prerequisiti

- Le versioni supportate di Windows sono:
  + Windows 7 Service Pack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   L'installazione del runtime di integrazione self-hosted in un controller di dominio non è supportata.
- È necessario .NET Framework 4.6.1 o versione successiva. Se si installa il runtime di integrazione self-hosted in un computer Windows 7, installare .NET Framework 4.6.1 o versione successiva. Per informazioni dettagliate, vedere [Requisiti di sistema di .NET Framework](/dotnet/framework/get-started/system-requirements) .
- La configurazione minima consigliata per il computer del runtime di integrazione self-hosted è un processore a 2 GHz con quattro core, 8 GB di RAM e 80 GB di spazio disponibile su disco rigido.
- Se il computer host è in stato di ibernazione, il runtime di integrazione self-hosted non risponde alle richieste di dati. Configurare una combinazione appropriata per il risparmio di energia nel computer prima di installare il runtime di integrazione self-hosted. Se il computer è configurato per l'ibernazione, il programma di installazione del runtime di integrazione self-hosted richiede un messaggio.
- Per installare e configurare correttamente il runtime di integrazione self-hosted, è necessario essere un amministratore del computer.
- Le esecuzioni delle attività di copia avvengono con una frequenza specifica. L'utilizzo del processore e della RAM nel computer segue lo stesso modello con tempi di punta e di inattività. L'utilizzo delle risorse dipende anche molto dalla quantità di dati spostati. Quando sono in corso più processi di copia, l'utilizzo delle risorse aumenta durante i periodi di picco.
- Le attività potrebbero non riuscire durante l'estrazione dei dati in formati parquet, ORC o avro. Per altre informazioni su parquet, vedere il [formato parquet in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime). La creazione di file viene eseguita nel computer di integrazione self-hosted. Per funzionare come previsto, per la creazione di file sono necessari i prerequisiti seguenti:
    - [Visual C++ 2010 Redistributable](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Pacchetto (x64)
    - Java Runtime (JRE) versione 8 da un provider JRE, ad esempio [Adopt OpenJDK](https://adoptopenjdk.net/). Verificare che sia impostata la variabile di ambiente `JAVA_HOME`.

## <a name="installation-best-practices"></a>Procedure consigliate per l'installazione

È possibile installare il runtime di integrazione self-hosted scaricando un pacchetto di installazione MSI dall' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Per istruzioni dettagliate, vedere l'articolo [spostare i dati tra l'ambiente locale e il cloud](tutorial-hybrid-copy-powershell.md) .

- Configurare una combinazione per il risparmio di energia nel computer host per il runtime di integrazione self-hosted in modo che il computer non sia in stato di ibernazione. Se il computer host entra in stato di ibernazione, il runtime di integrazione self-hosted passa in modalità offline.
- Eseguire regolarmente il backup delle credenziali associate al runtime di integrazione self-hosted.
- Per automatizzare le operazioni di installazione del runtime di integrazione self-hosted, vedere Configurare un runtime di integrazione self-hosted [esistente tramite PowerShell](#setting-up-a-self-hosted-integration-runtime).  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Installare e registrare un runtime di integrazione self-hosted dall'area download Microsoft

1. Accedere alla [pagina di download di Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
1. Selezionare **download**, selezionare la versione a 64 bit e fare clic su **Avanti**. La versione a 32 bit non è supportata.
1. Eseguire il file MSI direttamente oppure salvarlo sul disco rigido ed eseguirlo.
1. Nella finestra **iniziale** selezionare una lingua e fare clic su **Avanti**.
1. Accettare le condizioni di licenza software Microsoft e quindi selezionare **Avanti**.
1. Selezionare la **cartella** in cui installare il runtime di integrazione self-hosted e quindi selezionare **Avanti**.
1. Nella pagina **Pronto per l'installazione** selezionare **Installa**.
1. Selezionare **fine** per completare l'installazione.
1. Ottenere la chiave di autenticazione tramite PowerShell. Di seguito viene indicato un esempio di PowerShell per recuperare la chiave di autenticazione:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. Nella finestra **registra Integration Runtime (self-hosted)** di Microsoft Integration Runtime Configuration Manager in esecuzione nel computer, seguire questa procedura:

    1. Incollare la chiave di autenticazione nell'area di testo.

    1. Facoltativamente, selezionare **Mostra chiave di autenticazione** per visualizzare il testo della chiave.

    1. Selezionare **Registra**.

## <a name="high-availability-and-scalability"></a>Disponibilità e scalabilità elevate

È possibile associare un runtime di integrazione self-hosted a più computer locali o macchine virtuali in Azure. Questi computer sono chiamati nodi. È possibile avere fino a quattro nodi associati a un runtime di integrazione self-hosted. I vantaggi della presenza di più nodi nei computer locali in cui è installato un gateway per un gateway logico sono:

* Maggiore disponibilità del runtime di integrazione self-hosted in modo che non sia più il singolo punto di errore nella soluzione Big Data o nell'integrazione dei dati cloud con Data Factory. Questa disponibilità consente di garantire la continuità quando si usano fino a quattro nodi.
* Miglioramento delle prestazioni e della velocità effettiva durante lo spostamento dati tra archivi dati locali e cloud. Ottenere altre informazioni sui [confronti delle prestazioni](copy-activity-performance.md).

È possibile associare più nodi installando il software del runtime di integrazione self-hosted dall' [area download](https://www.microsoft.com/download/details.aspx?id=39717). Quindi, registrarlo usando una delle chiavi di autenticazione ottenute dal cmdlet **New-AzDataFactoryV2IntegrationRuntimeKey** , come descritto nell' [esercitazione](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Non è necessario creare un nuovo runtime di integrazione self-hosted per associare ogni nodo. È possibile installare il runtime di integrazione self-hosted in un altro computer e registrarlo con la stessa chiave di autenticazione.

> [!NOTE]
> Prima di aggiungere un altro nodo per la disponibilità elevata e la scalabilità, verificare che l'opzione **accesso remoto alla rete Intranet** sia abilitata nel primo nodo. A tale scopo, selezionare **Microsoft Integration Runtime Configuration Manager** **Impostazioni** >  > **accesso remoto alla rete Intranet**.

### <a name="scale-considerations"></a>Considerazioni sulla scalabilità

#### <a name="scale-out"></a>Scalabilità orizzontale

Quando l'utilizzo del processore è elevato e la memoria disponibile è insufficiente sul runtime di integrazione self-hosted, aggiungere un nuovo nodo per scalare orizzontalmente il carico tra i computer. Se le attività hanno esito negativo perché si verifica il timeout o il nodo IR self-hosted è offline, è utile se si aggiunge un nodo al gateway.

#### <a name="scale-up"></a>Aumentare le prestazioni

Quando il processore e la RAM disponibile non vengono utilizzati correttamente, ma l'esecuzione di processi simultanei raggiunge i limiti di un nodo, aumentando il numero di processi simultanei che possono essere eseguiti da un nodo. Potrebbe anche essere necessario aumentare le prestazioni quando si verifica il timeout delle attività perché il runtime di integrazione self-hosted è sovraccarico. Come illustrato nell'immagine seguente, è possibile aumentare la capacità massima di un nodo:  

![Aumentare il numero di processi simultanei che possono essere eseguiti in un nodo](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Requisiti del certificato TLS/SSL

Ecco i requisiti per il certificato TLS/SSL usato per proteggere la comunicazione tra i nodi di Integration Runtime:

- Deve essere un certificato X509 v3 pubblicamente attendibile. Si consiglia di usare i certificati emessi da un'autorità di certificazione (CA) del partner pubblico.
- Ogni nodo del runtime di integrazione deve considerare attendibile questo certificato.
- Non è consigliabile usare i certificati di nome alternativo del soggetto (SAN) perché viene usato solo l'ultimo elemento SAN. Tutti gli altri elementi SAN vengono ignorati. Se, ad esempio, si dispone di un certificato SAN le cui SANs sono **node1.Domain.contoso.com** e **node2.Domain.contoso.com**, è possibile utilizzare questo certificato solo in un computer il cui nome di dominio completo (FQDN) è **node2.Domain.contoso.com**.
- Il certificato può usare qualsiasi dimensione della chiave supportata da Windows Server 2012 R2 per i certificati SSL.
- I certificati che usano chiavi CNG non sono supportati.  

> [!NOTE]
> Questo certificato viene usato:
>
> - Per crittografare le porte in un nodo IR indipendente.
> - Per la comunicazione da nodo a nodo per la sincronizzazione dello stato, che include la sincronizzazione delle credenziali dei servizi collegati tra i nodi.
> - Quando un cmdlet di PowerShell viene usato per le impostazioni delle credenziali del servizio collegato dall'interno di una rete locale.
>
> Si consiglia di usare questo certificato se l'ambiente di rete privata non è sicuro o se si vuole proteggere la comunicazione tra i nodi all'interno della rete privata.
>
> Lo spostamento dei dati in transito da un runtime di integrazione self-hosted ad altri archivi di dati avviene sempre all'interno di un canale crittografato, indipendentemente dal fatto che il certificato sia impostato o meno.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Creare un runtime di integrazione self-hosted condiviso in Azure Data Factory

È possibile usare nuovamente un'infrastruttura del runtime di integrazione self-hosted esistente già configurata in una data factory. Questo riuso consente di creare un runtime di integrazione self-hosted collegato in un data factory diverso facendo riferimento a un runtime di integrazione self-hosted condiviso esistente.

Per visualizzare un'introduzione e una dimostrazione di questa funzionalità, guardare il video di 12 minuti seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **IR condiviso**: un runtime di integrazione self-hosted originale che viene eseguito in un'infrastruttura fisica.  
- **IR collegato**: un IR che fa riferimento a un altro IR condiviso. Il runtime di integrazione collegato è un IR logico e usa l'infrastruttura di un altro runtime di integrazione self-hosted condiviso.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Metodi per condividere un runtime di integrazione self-hosted

Per condividere un runtime di integrazione self-hosted con più data factory, vedere [creare un runtime di integrazione self-hosted condiviso](create-shared-self-hosted-integration-runtime-powershell.md) per informazioni dettagliate.

### <a name="monitoring"></a>Monitorare

#### <a name="shared-ir"></a>IR condiviso

![Selezioni per trovare un runtime di integrazione condiviso](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Monitorare un runtime di integrazione condivisa](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>IR collegato

![Selezioni per trovare un runtime di integrazione collegato](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Monitorare un runtime di integrazione collegato](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitazioni note della condivisione del runtime di integrazione self-hosted

* Il data factory in cui viene creato un runtime di integrazione collegato deve avere un file [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Per impostazione predefinita, le data factory create nel portale di Azure o i cmdlet di PowerShell hanno un MSI creato in modo implicito. Tuttavia, quando viene creata una data factory tramite un modello di Azure Resource Manager o un SDK, è necessario impostare la proprietà **Identity** in modo esplicito. Questa impostazione garantisce che Gestione risorse crei una data factory contenente un file MSI.

* Il Data Factory .NET SDK che supporta questa funzionalità deve essere la versione 1.1.0 o successiva.

* Per concedere l'autorizzazione, è necessario il ruolo proprietario o il ruolo proprietario ereditato nel data factory in cui è presente il runtime di integrazione condiviso.

* La funzionalità di condivisione funziona solo per le data factory nello stesso tenant Azure AD.

* Per Azure AD [utenti Guest](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews), la funzionalità di ricerca nell'interfaccia utente, che elenca tutte le data factory usando una parola chiave di ricerca, [non funziona](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Tuttavia, finché l'utente Guest è il proprietario del data factory, è possibile condividere il runtime di integrazione senza la funzionalità di ricerca. Per l'identità del servizio gestito del data factory che deve condividere il runtime di integrazione, immettere l'identità del servizio gestito nella casella **assegnazione autorizzazione** e selezionare **Aggiungi** nell'interfaccia utente di data factory.

  > [!NOTE]
  > Questa funzionalità è disponibile solo in Data Factory V2.

## <a name="notification-area-icons-and-notifications"></a>Icone e notifiche dell'area di notifica

Se si sposta il cursore sull'icona o sul messaggio nell'area di notifica, è possibile visualizzare i dettagli sullo stato del runtime di integrazione self-hosted.

![Notifiche nell'area di notifica](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>Porte e firewall

Ci sono due firewall da considerare:

- Il *firewall aziendale* in esecuzione nel router centrale dell'organizzazione
- *Windows Firewall* configurato come daemon nel computer locale in cui è installato il runtime di integrazione self-hosted

![Firewall](media/create-self-hosted-integration-runtime/firewall.png)

A livello di firewall aziendale, è necessario configurare i seguenti domini e porte in uscita:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

A livello di computer o di Windows Firewall queste porte in uscita sono normalmente abilitate. In caso affermativo, è possibile configurare i domini e le porte in un computer del runtime di integrazione self-hosted.

> [!NOTE]
> In base all'origine e ai sink, potrebbe essere necessario consentire altri domini e porte in uscita nel firewall aziendale o in Windows Firewall.
>
> Per alcuni database cloud, ad esempio il database SQL di Azure e Azure Data Lake, potrebbe essere necessario consentire gli indirizzi IP dei computer del runtime di integrazione self-hosted nella configurazione del firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiare i dati da un'origine a un sink

Assicurarsi di abilitare correttamente le regole del firewall nel firewall aziendale, Windows Firewall del computer del runtime di integrazione self-hosted e l'archivio dati. L'abilitazione di queste regole consente al runtime di integrazione self-hosted di connettersi all'origine e al sink. Abilitare le regole per ogni archivio dati interessato dall'operazione di copia.

Ad esempio, per eseguire la copia da un archivio dati locale a un sink di database SQL o a un sink di Azure SQL Data Warehouse, seguire questa procedura:

1. Consente la comunicazione TCP in uscita sulla porta 1433 per Windows Firewall e il firewall aziendale.
1. Configurare le impostazioni del firewall del database SQL per aggiungere l'indirizzo IP del computer del runtime di integrazione self-hosted all'elenco degli indirizzi IP consentiti.

> [!NOTE]
> Se il firewall non consente la porta in uscita 1433, il runtime di integrazione self-hosted non può accedere direttamente al database SQL. In questo caso, è possibile usare una [copia](copy-activity-performance.md) di gestione temporanea nel database SQL e SQL data warehouse. In questo scenario è necessario solo HTTPS (porta 443) per lo spostamento dei dati.

## <a name="proxy-server-considerations"></a>Considerazioni sui server proxy

Se nell'ambiente di rete aziendale è presente un server proxy per accedere a Internet, configurare il runtime di integrazione self-hosted per usare le impostazioni proxy appropriate. È possibile impostare il proxy durante la fase di registrazione iniziale.

![Specificare il proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Quando è configurato, il runtime di integrazione self-hosted usa il server proxy per connettersi all'origine e alla destinazione del servizio cloud (che usano il protocollo HTTP o HTTPS). Questo è il motivo per cui si seleziona **Modifica collegamento** durante l'installazione iniziale.

![Impostare il proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Sono disponibili tre opzioni di configurazione:

- **Non usare il proxy**: il runtime di integrazione self-hosted non usa in modo esplicito alcun proxy per connettersi ai servizi cloud.
- **Usa il proxy di sistema**: il runtime di integrazione self-hosted usa l'impostazione del proxy configurata in diahost. exe. config e diawp. exe. config. Se questi file non specificano alcuna configurazione proxy, il runtime di integrazione self-hosted si connette al servizio cloud direttamente senza passare attraverso un proxy.
- **Usa proxy personalizzato**: configurare l'impostazione proxy HTTP da usare per il runtime di integrazione self-hosted, anziché usare le configurazioni in diahost. exe. config e diawp. exe. config. I valori di **Indirizzo** e **porta** sono obbligatori. I valori di **nome utente** e **password** sono facoltativi, a seconda dell'impostazione di autenticazione del proxy. Tutte le impostazioni vengono crittografate con Windows DPAPI nel runtime di integrazione self-hosted e archiviate localmente nel computer.

Il servizio host di Integration Runtime viene riavviato automaticamente dopo avere salvato le impostazioni proxy aggiornate.

Dopo aver registrato il runtime di integrazione self-hosted, se si desidera visualizzare o aggiornare le impostazioni proxy, utilizzare Microsoft Integration Runtime Configuration Manager.

1. Aprire **Gestione configurazione di Microsoft Integration Runtime**.
1. Fare clic sulla scheda **Impostazioni**.
1. In **proxy http**selezionare il collegamento **Cambia** per aprire la finestra di dialogo **Imposta proxy http** .
1. Selezionare **Avanti**. Viene quindi visualizzato un avviso che richiede l'autorizzazione per salvare l'impostazione del proxy e riavviare il servizio host di Integration Runtime.

È possibile utilizzare lo strumento Gestione configurazione per visualizzare e aggiornare il proxy HTTP.

![Visualizzare e aggiornare il proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Se si configura un server proxy con autenticazione NTLM, il servizio host di Integration Runtime viene eseguito con l'account di dominio. Se successivamente si modifica la password per l'account di dominio, ricordarsi di aggiornare le impostazioni di configurazione per il servizio e riavviare il servizio. A causa di questo requisito, è consigliabile accedere al server proxy usando un account di dominio dedicato che non richiede di aggiornare la password di frequente.

### <a name="configure-proxy-server-settings"></a>Configurare le impostazioni del server proxy

Se si seleziona l'opzione **Usa il proxy di sistema** per il proxy http, il runtime di integrazione self-hosted usa le impostazioni proxy in diahost. exe. config e diawp. exe. config. Quando questi file non specificano alcun proxy, il runtime di integrazione self-hosted si connette al servizio cloud direttamente senza passare attraverso un proxy. La procedura seguente contiene le istruzioni per aggiornare il file diahost.exe.config:

1. In Esplora file creare una copia sicura di C:\Programmi\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config come backup del file originale.
1. Aprire il blocco note eseguito come amministratore.
1. Nel blocco note aprire il file di testo C:\Programmi\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config.
1. Trovare il tag **System.NET** predefinito, come illustrato nel codice seguente:

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

    Il tag proxy consente alle proprietà aggiuntive di specificare le impostazioni necessarie, ad esempio `scriptLocation`. Per la sintassi, vedere [\<elemento proxy\> (impostazioni di rete)](https://msdn.microsoft.com/library/sa91de1e.aspx) .

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. Salvare il file di configurazione nel percorso originale. Riavviare quindi il servizio host del runtime di integrazione self-hosted, che preleva le modifiche.

   Per riavviare il servizio, usare l'applet servizi del pannello di controllo. In alternativa, in Gestione configurazione di Integration Runtime selezionare il pulsante **Arresta servizio** e quindi selezionare **Avvia servizio**.

   Se il servizio non viene avviato, è probabile che sia stata aggiunta una sintassi di tag XML non corretta nel file di configurazione dell'applicazione modificato.

> [!IMPORTANT]
> Non dimenticare di aggiornare entrambi i file diahost.exe.config e diawp.exe.config.

È anche necessario assicurarsi che Microsoft Azure sia presente nell'elenco Consenti della società. È possibile scaricare l'elenco di indirizzi IP di Azure validi dall' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Possibili sintomi per i problemi correlati al firewall e al server proxy

Se vengono visualizzati messaggi di errore simili a quelli riportati di seguito, il motivo probabile è la configurazione non corretta del firewall o del server proxy. Tale configurazione impedisce al runtime di integrazione self-hosted di connettersi a Data Factory per l'autenticazione. Per verificare che la configurazione del firewall e del server proxy sia corretta, vedere la sezione precedente.

* Quando si tenta di registrare il runtime di integrazione self-hosted, viene visualizzato il messaggio di errore seguente: "Impossibile registrare il nodo Integration Runtime. Verificare che la chiave di autenticazione sia valida e che il servizio host del servizio di integrazione sia in esecuzione nel computer. "
* Quando si apre Gestione configurazione di Integration Runtime, lo stato del gateway visualizzato può essere **Disconnesso** o **Connessione**. Quando si visualizzano i registri eventi di Windows, in **Visualizzatore eventi** > **registri applicazioni e servizi** > **Microsoft Integration Runtime**, viene visualizzato un messaggio di errore simile al seguente:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>Abilitare l'accesso remoto da una rete Intranet

Se si usa PowerShell per crittografare le credenziali da un computer in rete diverso da quello in cui è stato installato il runtime di integrazione self-hosted, è possibile abilitare l'opzione **accesso remoto da Intranet** . Se si esegue PowerShell per crittografare le credenziali nel computer in cui è stato installato il runtime di integrazione self-hosted, non è possibile abilitare **l'accesso remoto dalla rete Intranet**.

Abilitare **l'accesso remoto dalla rete Intranet** prima di aggiungere un altro nodo per la disponibilità e la scalabilità elevate.  

Quando si esegue il programma di installazione del runtime di integrazione self-hosted versione 3,3 o successiva, per impostazione predefinita il programma di installazione del runtime di integrazione self-hosted Disabilita l' **accesso remoto dalla rete Intranet** nel computer del runtime di integrazione self-hosted.

Quando si usa un firewall da un partner o da altri, è possibile aprire manualmente la porta 8060 o la porta configurata dall'utente. Se si verifica un problema del firewall durante la configurazione del runtime di integrazione self-hosted, usare il comando seguente per installare il runtime di integrazione Self-Hosted senza configurare il firewall:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Se si sceglie di non aprire la porta 8060 nel computer del runtime di integrazione self-hosted, usare meccanismi diversi dall'applicazione impostazione credenziali per configurare le credenziali dell'archivio dati. Ad esempio, è possibile usare il cmdlet di PowerShell **New-AzDataFactoryV2LinkedServiceEncryptCredential** .

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni dettagliate, vedere [esercitazione: copiare i dati locali nel cloud](tutorial-hybrid-copy-powershell.md).
