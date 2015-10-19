<properties
 pageTitle="Cluster HPC Pack per Excel e SOA | Microsoft Azure"
 description="Introduzione all'uso di un cluster HPC Pack per l'esecuzione di carichi di lavoro di Excel e SOA mediante il modello di distribuzione di Gestione risorse."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager"/>

<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/18/2015"
 ms.author="danlep"/>

# Introduzione all'uso di un cluster HPC Pack in Azure per l'esecuzione di carichi di lavoro di Excel e SOA

In questo articolo viene illustrato come distribuire un cluster HPC Pack nei servizi di infrastruttura di Azure (IaaS) mediante un modello di Guida introduttiva di Azure o uno script di distribuzione di Azure PowerShell. Usare le immagini di macchina virtuale di Azure Marketplace progettate per l'esecuzione di carichi di lavoro di architettura orientata ai servizi (SOA) o Microsoft Excel con HPC Pack. È possibile usare il cluster per eseguire semplici servizi Excel HPC e SOA da un computer client locale. I servizi Excel HPC includono l'offload di cartelle di lavoro di Excel e funzioni definite dall'utente di Excel o UDF.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Questo articolo illustra la creazione delle risorse mediante il modello di distribuzione di Gestione risorse.

Nel seguente diagramma viene illustrato il cluster HPC Pack che verrà creato, a livello elevato.

![Cluster HPC con nodi che eseguono carichi di lavoro di Excel][scenario]

## Prerequisiti

* **Computer client**: è necessario un computer client basato su Windows per eseguire lo script di distribuzione del cluster di Azure PowerShell (se si sceglie tale metodo di distribuzione) e l'invio di processi di Excel e SOA di esempio al cluster.

* **Sottoscrizione di Azure**: se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

* **Quota di core**: potrebbe essere necessario aumentare la quota di core, soprattutto se si sceglie di distribuire più nodi del cluster con dimensioni delle macchine virtuali multicore. Se si usa un modello di Guida introduttiva di Azure, tenere presente che la quota di core in Gestione risorse è indicata per area di Azure, quindi potrebbe essere necessario aumentare la quota in un'area specifica. Vedere [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](../azure-subscription-service-limits.md). Per aumentare una quota, è possibile [aprire una richiesta di assistenza clienti online](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.


## Passaggio 1. Configurazione di un cluster HPC Pack in Azure

Verranno mostrati due metodi per configurare il cluster: nel primo, vengono usati un modello di Guida introduttiva di Azure e il portale di anteprima di Azure e nel secondo viene usato uno script di distribuzione di Azure PowerShell.


### Uso di un modello di Guida introduttiva
Usare un modello di Guida introduttiva di Azure per distribuire con rapidità e facilità un cluster HPC Pack nel portale di anteprima di Azure. Aprendo il modello nel portale, si ottiene un'interfaccia utente semplice in cui è possibile immettere le impostazioni per il cluster. Di seguito sono riportati i passaggi necessari.

1. Visitare la [pagina del modello per la creazione del cluster HPC su GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Se lo si desidera, esaminare le informazioni sul modello e il codice sorgente.

2. Fare clic su **Distribuisci in Azure**per avviare una distribuzione con il modello nel portale di anteprima di Azure.

    ![Modello di distribuzione in Azure][github]

3. Nel portale, attenersi alla procedura seguente per immettere i parametri del modello di cluster HPC.

    a. Nella pagina **Modifica modello** fare clic su **Salva**.

    ![Salvare il modello][template]

    b. Nella pagina **Parametri** immettere i valori per i parametri del modello. (Per visualizzare informazioni della Guida, fare clic sull'icona accanto a ogni impostazione). Nella seguente schermata vengono visualizzati valori di esempio. In questo esempio viene creato un nuovo cluster HPC Pack denominato *hpc01* nel dominio *hpc.local* composto da un nodo head e 2 nodi di calcolo. I nodi di calcolo verranno creati da un'immagine di VM di HPC Pack che include Microsoft Excel.

    ![Immettere i parametri][parameters]

    >[AZURE.NOTE]La VM del nodo head verrà creata automaticamente [dall'immagine più recente del Marketplace](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) di HPC Pack 2012 R2 su Windows Server 2012 R2. Attualmente l'immagine è basata su HPC Pack 2012 R2 Update 2.
    >
    >Le VM del nodo di calcolo verranno create dall'immagine più recente della famiglia di nodi di calcolo selezionata. Selezionare l'opzione **ComputeNode** per l'immagine più recente del nodo di calcolo di HPC Pack 2012 R2 Update 2 per usi generici. Selezionare l'opzione **ComputeNodeWithExcel** per l'immagine più recente del nodo di calcolo di HPC Pack che include una versione di valutazione di Microsoft Excel Professional Plus 2013. Se si desidera distribuire un cluster per sessioni SOA generiche o per l'offload di UDF di Excel, scegliere l'opzione **ComputeNode** (senza Excel installato).
    >
    >Quando si usa **ComputeNodeWithExcel** per carichi di lavoro di produzione, è necessario disporre di una licenza Excel valida per attivare Excel sui nodi di calcolo. In caso contrario, la versione di valutazione di Excel potrebbe scadere entro 30 giorni e l'esecuzione della cartella di lavoro di Excel restituirebbe costantemente l'errore COMExeption (0x800AC472). In questo caso, è possibile accedere al nodo head per eseguire il comando clusrun per il file "%ProgramFiles(x86)%\\Microsoft Office\\Office15\\OSPPREARM.exe" in tutti i nodi di calcolo di Excel mediante la console HPC Cluster Manager per estendere il periodo di valutazione di Excel di altri 30 giorni. Il valore massimo della riattivazione del periodo di prova per il periodo di tolleranza è 2, dopodiché è necessaria una licenza di Excel valida.

    c. Selezionare la sottoscrizione.

    d. Creare un nuovo gruppo di risorse per il cluster, ad esempio*hpc01RG*.

    e. Scegliere un percorso per il gruppo di risorse, ad esempio Stati Uniti orientali.

    f. Nella pagina **Note legali** esaminare le condizioni. Se si accettano le condizioni, fare clic su **Acquista**.

    g. Dopo aver impostato i valori per il modello, fare clic su **Crea**.

    ![Creare il cluster][create]

3.	Al termine della distribuzione (in genere richiede circa 30 minuti), esportare il file del certificato del cluster dal nodo head del cluster. In un passaggio successivo questo certificato pubblico verrà importato nel computer client per fornire l'autenticazione sul lato server per l'associazione HTTP protetta.

    a. Connettersi al nodo head da Desktop remoto nel portale di anteprima di Azure.

     ![Connettersi al nodo head][connect]

    b. Impiegare le procedure standard per usare Gestione certificati per esportare il certificato del nodo head (situato in Cert: \\LocalMachine\\My) senza la chiave privata. In questo esempio esportare *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Esportare il certificato][cert]

### Uso dello script di distribuzione di HPC Pack IaaS

Lo script di distribuzione di HPC Pack IaaS offre un altro modo versatile per distribuire un cluster HPC Pack. Viene eseguito in modalità di gestione del servizio di Azure (ASM), mentre il modello viene eseguito in modalità di gestione delle risorse di Azure (ARM). Inoltre, lo script è compatibile con una sottoscrizione nel servizio Azure globale o Azure Cina.

**Ulteriori prerequisiti**

* **Azure PowerShell**: [installare e configurare Azure PowerShell](../powershell-install-configure.md) (versione 0.8.10 o versione successiva) nel computer client.

* **Script di distribuzione di HPC Pack IaaS**: scaricare e decomprimere la versione più recente dello script dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Controllare la versione dello script eseguendolo `New-HPCIaaSCluster.ps1 –Version`. Questo articolo si basa sulla versione dello script 4.4.0 o versione successiva.

**Creazione del file di configurazione**

 Lo script di distribuzione di HPC Pack IaaS usa come input un file di configurazione XML che descrive l'infrastruttura del cluster HPC. Per distribuire un cluster costituito da un nodo head e 18 nodi di calcolo creati dall'immagine del nodo di calcolo che include Microsoft Excel, sostituire i valori per il proprio ambiente nel file di configurazione di esempio riportato di seguito. Per altre informazioni sul file di configurazione, vedere il file Manual.rtf nella cartella dello script e l'articolo relativo alla [creazione di un cluster HPC con lo script di distribuzione di HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
<PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName HPCPackInstalled="true">96316178b0644ae08bc4e037635ce104__HPC-Pack-2012R2-Update2-CN-Excel-4.4.4868.0-WS2012R2-ENU</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Note sul file di configurazione**

* Il valore di **VMName** del nodo head deve essere identico al valore di **ServiceName**. In caso contrario sarebbe impossibile eseguire il processo SOA.

* Assicurarsi di specificare **EnableWebPortal** per consentire la generazione e l'esportazione del certificato del nodo head.

* Lo script PostConfig.ps1 di post-configurazione di PowerShell, consente di configurare determinate impostazioni nel nodo head, ad esempio l'impostazione della stringa di connessione di archiviazione di Azure, la rimozione del ruolo del nodo di calcolo dal nodo head e l'impostazione di tutti i nodi in linea quando vengono distribuiti. Di seguito è riportato uno script di esempio.

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Esecuzione dello script**

1. Aprire la console di PowerShell nel computer client come amministratore.

2. Passare alla cartella dello script (E:\\IaaSClusterScript in questo esempio).

    ```
    cd E:\IaaSClusterScript
```

4. Eseguire il comando seguente per distribuire il cluster HPC Pack. In questo esempio si presuppone che il file di configurazione si trovi in E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
```

Per qualche istante, viene eseguito lo script di distribuzione di HPC Pack. Con l'esecuzione dello script, il certificato del cluster viene esportato, scaricato e salvato nell'attuale cartella Documenti dell'utente sul computer client. Lo script genera un messaggio simile al seguente. In un passaggio successivo, il certificato verrà importato nell'archivio certificati appropriato.

```
You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
 C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_2015070716
2011.cer
```

## Passaggio 2. Offload di cartelle di lavoro di Excel ed esecuzione di funzioni definite dall'utente da un client locale

### Offload di cartelle di lavoro di Excel

Per eseguire l'offload di una cartella di lavoro di Excel da eseguire nel cluster HPC Pack in Azure, attenersi alla procedura riportata di seguito. A tale scopo, è necessario disporre di Excel 2010 o 2013 già installato nel computer client.

1. Usare uno dei metodi illustrati nel passaggio 1 per distribuire un cluster HPC Pack con l'immagine del nodo di calcolo di Excel. Ottenere il file del certificato del cluster (con estensione cer) e nome utente e password del cluster.

2. Nel computer client, importare il certificato del cluster in Cert: \\CurrentUser\\Root.

3. Verificare che Excel sia installato. Creare un file Excel.exe.config con i seguenti contenuti nella stessa cartella di Excel.exe del computer client. Ciò garantisce che il componente aggiuntivo COM di Excel di HPC Pack 2012 R2 e la libreria di archiviazione di Azure vengano caricati correttamente. Si noti che l''href' seguente deve fare riferimento a "%CCP\_HOME%Bin\\Microsoft.WindowsAzure.Storage.dll" sul computer client.

    ```
<?xml version="1.0"?>
<configuration>
    <startup useLegacyV2RuntimeActivationPolicy="true">
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
    </startup>
    <runtime>
        <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
            <dependentAssembly>
                <assemblyIdentity name="Microsoft.WindowsAzure.Storage"  culture="neutral" publicKeyToken="31bf3856ad364e35"/>
                <codeBase version="4.3.0.0" href="C:\Program Files\Microsoft HPC Pack 2012\Bin\Microsoft.WindowsAzure.Storage.dll"/>
            </dependentAssembly>
        </assemblyBinding>
    </runtime>
</configuration>
```
4.	Scaricare la versione completa di [installazione di HPC Pack 2012 R2 Update 2](http://www.microsoft.com/download/details.aspx?id=47755) e installare il client di HPC Pack oppure scaricare e installare le [utilità client di HPC Pack 2012 R2 Update 2](https://www.microsoft.com/download/details.aspx?id=47754) e il file ridistribuibile Visual C++ 2010 appropriato per il computer ([x64](http://www.microsoft.com/download/details.aspx?id=14632)[x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.	In questo esempio viene usata una cartella di lavoro di esempio di Excel denominata ConvertiblePricing\_Complete.xlsb, disponibile per il download [qui](https://www.microsoft.com/it-IT/download/details.aspx?id=2939).

6.	Copiare la cartella di lavoro di Excel in una cartella di lavoro, ad esempio D:\\Excel\\Run.

7.	Aprire la cartella di lavoro di Excel. Nella barra multifunzione **Sviluppo** fare clic su **COM Add-Ins** e verificare che il componente aggiuntivo HPC Pack Excel COM venga caricato correttamente, come illustrato nella schermata seguente.

    ![Componente aggiuntivo Excel per HPC Pack][addin]

8.	Modificare la macro VBA HPCControlMacros in Excel cambiando le righe commentate, come illustrato nello script seguente. Sostituire con i valori appropriati per il proprio ambiente.

    ![Macro Excel per HPC Pack][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.	Copiare la cartella di lavoro di Excel in una directory di caricamento, ad esempio D:\\Excel\\Upload, come specificato nella costante HPC\_DependsFiles nella macro VBA.

10.	Scegliere il pulsante **Cluster** nel foglio di lavoro per eseguire la cartella di lavoro nel cluster IaaS di Azure.

### Esecuzione delle funzioni definite dall'utente di Excel

Per eseguire funzioni definite dall'utente di Excel, seguire i passaggi da 1 a 3 della precedente configurazione del computer client. Per le funzioni definite dall'utente di Excel, non è necessario che l'applicazione Excel sia installata sui nodi di calcolo, pertanto nel passaggio 1 è possibile scegliere un'immagine normale del nodo di calcolo anziché l'immagine del nodo di calcolo con Excel.

>[AZURE.NOTE]Esiste un limite di 34 caratteri nella finestra di dialogo del connettore del cluster di Excel 2010 e 2013. Se il nome completo del cluster è più lungo, ad esempio hpcexcelhn01.southeastasia.cloudapp.azure.com, lo spazio disponibile nella finestra di dialogo non sarà sufficiente. La soluzione alternativa consiste nell'applicare Update 2 QFE KB3085833 (scaricabile [qui](http://www.microsoft.com/it-IT/download/details.aspx?id=48725)) per l'API di sessione SOA sul computer client, quindi impostare una variabile per tutti i computer, ad esempio *CCP\_IAASHN* con il valore del nome di cluster lungo e l'input *%CCP\_IAASHN%* nella finestra di dialogo come nome del nodo head del cluster.

Dopo aver correttamente distribuito il cluster, continuare con la procedura seguente per eseguire una funzione definita dall'utente di Excel integrata di esempio. Per funzioni definite dall'utente di Excel personalizzate, vedere queste [risorse](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) per compilare librerie XLL e distribuirle sul cluster IaaS.

1.	Aprire una nuova cartella di lavoro di Excel. Nella barra multifunzione **Sviluppo** fare clic su **Componenti aggiuntivi**. Quindi, nella finestra di dialogo fare clic su **Sfoglia**, accedere alla cartella %CCP\_HOME%Bin\\XLL32 e selezionare il ClusterUDF32.xll di esempio. Se il ClusterUDF32 non esiste nel computer client, è possibile copiarlo dalla cartella %CCP\_HOME%Bin\\XLL32 nel nodo head.

    ![Selezionare la UDF][udf]

2.	Fare clic su **File** > **Opzioni** > **Avanzate**. In **Formule** selezionare **Consenti l'esecuzione di funzioni XLL definite dall'utente in un cluster di calcolo**. Fare clic su **Opzioni** e immettere il nome completo del cluster in **Nome del nodo head del cluster**. (Come indicato in precedenza la casella di immissione è limitata a 34 caratteri, pertanto un nome di cluster lungo potrebbe non essere adatto. È possibile applicare l'aggiornamento KB3085833 QFE 2 nel client e quindi impostare una macchina di larghezza variabile qui per il nome lungo del cluster.)

    ![Configurare la UDF][options]

3.	Fare clic sulla cella con valore =XllGetComputerNameC() e premere Invio per eseguire il calcolo della funzione definita dall'utente nel cluster IaaS. La funzione recupererà semplicemente il nome del nodo di calcolo in cui viene eseguita la funzione definita dall'utente. Per la prima esecuzione, una finestra di dialogo per l'immissione delle credenziali richiede nome utente e password per connettersi al cluster IaaS.

    ![Eseguire una UDF][run]

    Quando esistono varie celle da elaborare, premere Alt-Shift-Ctrl + F9 per eseguire il calcolo su tutte le celle.

## Passaggio 3. Esecuzione di un carico di lavoro SOA da un client locale

Per eseguire applicazioni SOA generiche nel cluster HPC Pack IaaS, usare in primo luogo uno dei metodi illustrati al passaggio 1 per distribuire il cluster IaaS, usando l'immagine di un nodo di calcolo generico (perché non è necessario Excel nei nodi di calcolo). Attenersi quindi alla procedura seguente:

1. Dopo avere recuperato il certificato del cluster, importarlo nel computer client, in Cert:\\CurrentUser\\Root.

2. Installare le [utilità client HPC Pack 2012 R2 Update 2 SDK](http://www.microsoft.com/download/details.aspx?id=47756) e [HPC Pack 2012 R2 Update 2](https://www.microsoft.com/download/details.aspx?id=47754) in modo che sia possibile sviluppare ed eseguire applicazioni client SOA.

3. Scaricare il [codice di esempio](https://www.microsoft.com/download/details.aspx?id=41633) HellowWorldR2. Aprire HelloWorldR2.sln in Visual Studio 2010 o 2012.

4. Compilare innanzitutto il progetto EchoService e distribuire il servizio nel cluster IaaS nello stesso modo usato per la distribuzione in un cluster locale. Per informazioni dettagliate, vedere il file Readme.doc in HelloWordR2. Modificare e compilare HellowWorldR2 e altri progetti, come descritto di seguito per generare le applicazioni client SOA in esecuzione in un cluster IaaS di Azure da un computer client locale.

### Uso dell'associazione Http con coda di archiviazione di Azure

Per usare l'associazione Http con una coda di archiviazione di Azure, apportare alcune modifiche al codice di esempio.

* Aggiornare il nome del cluster.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* Facoltativamente, usare il nome predefinito TransportScheme in SessionStartInfo o impostarlo in modo esplicito su Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Usare l'associazione predefinita per il BrokerClient.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    Oppure impostare in modo esplicito usando l'associazione basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Facoltativamente, impostare il flag UseAzureQueue su true in SessionStartInfo. Se non lo si imposta, verrà impostato in modo predefinito su true quando il nome del cluster dispone di suffissi di dominio di Azure e il TransportScheme è Http.

    ```
    info.UseAzureQueue = true;
```

###Uso dell'associazione Http senza coda di archiviazione di Azure

A tale scopo, impostare in modo esplicito il flag UseAzureQueue su false in SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### Uso dell'associazione NetTcp

Per usare l'associazione NetTcp, la configurazione è analoga alla connessione a un cluster locale. È necessario aprire alcuni endpoint nella VM del nodo head. Nel portale di gestione di Azure procedere come descritto di seguito.


1. Arrestare la VM.

2. Aggiungere le porte TCP 9090, 9087, 9091, 9094 rispettivamente per Sessione, Broker, Broker worker e Servizi dati

    ![Configurare gli endpoint][endpoint]

3. Avviare la VM.

L'applicazione client SOA non richiede alcuna modifica, ad eccezione della modifica al nome head per il nome completo del cluster IaaS.

## Passaggi successivi

* Per altre informazioni sull'esecuzione di carichi di lavoro di Excel con HPC Pack, vedere [queste risorse](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx).

* Vedere l'articolo relativo alla [gestione dei servizi SOA in Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) per altre informazioni sulla distribuzione e sulla gestione dei servizi SOA con HPC Pack.

<!--Image references-->
[scenario]: ./media/virtual-machines-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-excel-cluster-hpcpack/udf.png

<!---HONumber=Oct15_HO2-->