---
title: SQL Server Business Intelligence | Microsoft Docs
description: "Questo argomento tratta le risorse create con il modello di distribuzione classica e descrive le funzionalità di Business Intelligence (BI) disponibili per SQL Server in esecuzione in macchine virtuali (VM) di Azure."
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 90e443fe69afdda8786dd00ffb32e9680a476cd4
ms.lasthandoff: 03/25/2017


---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>SQL Server Business Intelligence in Macchine virtuali di Azure
> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

La raccolta di macchine virtuali di Microsoft Azure include immagini che contengono installazioni SQL Server. Le edizioni di SQL Server supportate nelle immagini della raccolta sono gli stessi file di installazione che è possibile installare in computer locali e macchine virtuali. Questo argomento riepiloga le funzionalità di SQL Server Business Intelligence (BI) installate nelle immagini e i passaggi di configurazione necessari dopo il provisioning di una macchina virtuale. Questo argomento descrive inoltre le topologie di distribuzione supportate per le funzionalità di Business Intelligence e le procedure consigliate.

## <a name="license-considerations"></a>Considerazioni sulle licenze
Esistono due tipi di licenza di SQL Server in macchine virtuali di Microsoft Azure:

1. Vantaggi di mobilità delle licenze che fanno parte del contratto Software Assurance. Per altre informazioni, vedere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Pagare la tariffa oraria di macchine virtuali di Azure con SQL Server installato. Vedere la sezione "SQL Server" in [Prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Per altre informazioni sulla gestione delle licenze e le tariffe attuali, vedere l'articolo relativo alle [domande frequenti sulle licenze di macchine virtuali](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>Immagini SQL Server disponibili nella raccolta macchine virtuali di Azure
Nella raccolta di macchine virtuali di Microsoft Azure sono disponibili numerose immagini che contengono Microsoft SQL Server. Il software installato in immagini di macchina virtuale varia in base alla versione del sistema operativo e alla versione di SQL Server. L'elenco delle immagini disponibili nella raccolta di macchina virtuale di Azure viene spesso modificato.

![Immagine SQL nella raccolta di macchine virtuali di azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Lo script PowerShell seguente restituisce l'elenco di immagini di Azure contenenti "SQL Server" in ImageName:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure classic portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Per altre informazioni sulle edizioni e sulle funzionalità supportate da SQL Server, vedere gli argomenti seguenti:

* [Edizioni di SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)
* [Funzionalità supportate dalle edizioni di SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Funzionalità di Business Intelligence installate nelle immagini della raccolta di macchine virtuali di SQL Server
Nella tabella seguente sono riepilogate le funzionalità di Business Intelligence installate nelle immagini comuni della raccolta di Macchine virtuali di Microsoft Azure per SQL Server

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| Funzionalità SQL Server BI | Installato nell'immagine della raccolta | Note |
| --- | --- | --- |
| **Modalità nativa di Reporting Services** |Sì |Installata ma richiede la configurazione, inclusi l'URL di gestione report. Vedere la sezione [Configurare Reporting Services](#configure-reporting-services). |
| **Modalità SharePoint di Reporting Services** |No |L'immagine della raccolta macchine virtuali di Microsoft Azure non include SharePoint o i suoi file di installazione. <sup>1</sup> |
| **Modalità multidimensionale e di Data mining di Analysis Services (OLAP)** |Sì |Installata e configurata come istanza di Analysis Services predefinita |
| **Modalità tabulare di Analysis Services** |No |Supportata nelle immagini di SQL Server 2012, 2014 e 2016 ma non è installata per impostazione predefinita. Installare un'altra istanza di Analysis Services. Vedere la sezione Installare altri servizi e funzionalità di SQL Server in questo argomento. |
| **Analysis Services Power Pivot per SharePoint** |No |L'immagine della raccolta macchine virtuali di Microsoft Azure non include SharePoint o i suoi file di installazione. <sup>1</sup> |

<sup>1</sup> Per altre informazioni su SharePoint e le macchine virtuali di Azure, vedere gli articoli [Microsoft Azure Architectures for SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) (Architetture di Microsoft Azure per SharePoint 2013) e [SharePoint Deployment on Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598)(Distribuzione di SharePoint in macchine virtuali di Microsoft Azure).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Eseguire il comando PowerShell seguente per ottenere un elenco dei servizi installati che contengono "SQL" nel nome del servizio.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Indicazioni generali e procedure consigliate
* Le dimensioni minime consigliate per una macchina virtuale corrispondono ad **A3** quando si usa SQL Server Enterprise Edition. La dimensione **A4** della macchina virtuale è consigliata per distribuzioni di SQL Server Business Intelligence di Analysis Services e Reporting Services.
  
    Per informazioni sulle dimensioni correnti delle macchine virtuali, vedere [Dimensioni delle macchine virtuali](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* La procedura consigliata per la gestione del disco consiste nell'archiviare dati, log e file di backup in unità diverse da **C**: e **D**:. Ad esempio, è possibile creare i dischi dati **E**: e **F**:.
  
  * Il criterio di memorizzazione nella cache per l'unità predefinita **C**: non è ottimale per l'uso dei dati.
  * L'unità **D**: è un'unità temporanea usata principalmente per il file di paging. L'unità **D**: infatti non è permanente e non viene salvata nell'archiviazione BLOB. Le attività di gestione, ad esempio la modifica delle dimensioni della macchina virtuale, reimpostano l'unità **D**:. È consigliabile **NON** usare l'unità **D**: per i file di database, incluso tempdb.
    
    Per altre informazioni su come creare e collegare dischi, vedere [Come collegare un disco dati a una macchina virtuale Windows](../classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Arrestare o disinstallare i servizi che non si intende utilizzare. Ad esempio se la macchina virtuale viene utilizzata solo per Reporting Services, arrestare o disinstallare Analysis Services e SQL Server Integration Services. L'immagine seguente è un esempio dei servizi che vengono avviati per impostazione predefinita.
  
    ![Servizi di SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > Il motore del database di SQL Server è necessario negli scenari BI supportati. Nella topologia di macchina virtuale con un solo server, il motore del database deve essere in esecuzione nella macchina virtuale stessa.
  
    Per altre informazioni, vedere gli argomenti seguenti: [Disinstallare Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) e [Procedura: Disinstallazione di un'istanza di Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Controllare se in **Windows Update** sono disponibili nuovi "aggiornamenti importanti". Le immagini di macchina virtuale di Microsoft Azure vengono aggiornate di frequente, tuttavia alcuni aggiornamenti importanti potrebbero diventare disponibili in **Windows Update** dopo l'ultimo aggiornamento dell'immagine di macchina virtuale.

## <a name="example-deployment-topologies"></a>Topologie di distribuzione di esempio
Quelle riportate di seguito sono distribuzioni di esempio che utilizzano macchine virtuali di Microsoft Azure. Le topologie in questi diagrammi sono solo alcune delle topologie che è possibile utilizzare con le funzionalità di BI di SQL Server e le macchine virtuali di Microsoft Azure.

### <a name="single-virtual-machine"></a>Singola macchina virtuale
Analysis Services, Reporting Services e il motore di Database SQL Server e origini dati in una singola macchina virtuale.

![scenario BI iaaS con 1 macchina virtuale](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Due macchine virtuali
* Analysis Services, Reporting Services e il motore di Database SQL Server in una singola macchina virtuale. Questa distribuzione include i database del server di report.
* Origini dati in una seconda macchina virtuale. La seconda VM include il motore di Database di SQL Server come origine dati.

![Scenario BI IaaS con 2 macchine virtuali](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Dati misti di Azure nel database SQL di Azure
* Analysis Services, Reporting Services e il motore di Database SQL Server in una singola macchina virtuale. Questa distribuzione include i database del server di report.
* Origine dati è il database di SQL Azure.

![macchina virtuale scenari BI iaas e AzureSQL come origine dati](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Dati ibridi locali
* In questa distribuzione di esempio Analysis Services, Reporting Services e il motore di Database SQL Server vengono eseguiti su una singola macchina virtuale. La macchina virtuale ospita i database del server di report. La macchina virtuale è associata a un dominio locale tramite la rete virtuale di Azure o qualche altra soluzione di tunneling VPN.
* L'origine dati è locale.

![vm scenari BI iaas e origini dati locali](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Configurazione di Reporting Services in modalità nativa
L'immagine della raccolta di macchine virtuali per SQL Server include l’istallazione della modalità nativa di Reporting Services, tuttavia, il server di report non è configurato. I passaggi descritti in questa sezione consentono di configurare il server di report di Reporting Services. Per informazioni più dettagliate sulla configurazione della modalità nativa di Reporting Services, vedere [Installare il server di report in modalità nativa di Reporting Services](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Per contenuti simili che usano script di Windows PowerShell per configurare il server di report, vedere [Usare PowerShell per creare una macchina virtuale di Azure con un server di report in modalità nativa](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Connettersi alla macchina virtuale e avviare Gestione configurazione di Reporting Services
Esistono due flussi di lavoro comuni per la connessione a una macchina virtuale di Azure:

* Per la connessione fare clic sul nome della macchina virtuale e quindi fare clic su **Connetti**. Si apre una connessione desktop remoto e il nome del computer viene popolato automaticamente.
  
    ![connettersi alla macchina virtuale di azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Connettersi alla macchina virtuale tramite connessione desktop remoto di Windows. Nell'interfaccia utente del desktop remoto:
  
  1. Digitare il **nome del servizio cloud** come nome del computer.
  2. Digitare due punti (:) e il numero di porta pubblica configurato per l'endpoint del desktop remoto di TCP.
     
      Myservice.cloudapp.net:63133
     
      Per altre informazioni, vedere l'articolo relativo alla [definizione di servizio cloud](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).

**Avviare Gestione configurazione Reporting Services.**

1. In **Windows Server 2012/2016**:
2. Nella schermata **Start** digitare **Reporting Services** per visualizzare un elenco di app.
3. Fare clic con il pulsante destro del mouse su **Gestione configurazione Reporting Services** e scegliere **Esegui come amministratore**.
4. In **Windows Server 2008 R2**:
5. Fare clic su **Start** e quindi su **Tutti i programmi**.
6. Fare clic su **Microsoft SQL Server 2016**.
7. Fare clic su **Strumenti di configurazione**.
8. Fare clic con il pulsante destro del mouse su **Gestione configurazione Reporting Services** e scegliere **Esegui come amministratore**.

oppure

1. Fare clic su **Avvia**.
2. Nella finestra di dialogo **Cerca programmi e file** digitare **reporting services**. Se la macchina virtuale esegue Windows Server 2012, digitare **reporting services** nella schermata Start di Windows Server 2012.
3. Fare clic con il pulsante destro del mouse su **Gestione configurazione Reporting Services** e scegliere **Esegui come amministratore**.
   
    ![ricerca di Gestione configurazione ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurare Reporting Services
**Account del servizio e URL del servizio web:**

1. Verificare che nel campo **Nome server** sia specificato il nome del server locale e fare clic su **Connetti**.
2. Si noti lo spazio vuoto **Nome database del server di report**. Il database viene creato al completamento della configurazione.
3. Verificare che lo **Stato server di report** sia **Avviato**. Se si desidera verificare il servizio in Gestione server di Windows, il servizio è il servizio Windows **SQL Server Reporting Services** .
4. Fare clic su **Account del servizio** e modificare l'account in base alle esigenze. Se la macchina virtuale viene usata in un ambiente non associato a un dominio, l'account **ReportServer** incorporato è sufficiente. Per ulteriori informazioni sull'account del servizio, vedere [Account del servizio](https://msdn.microsoft.com/library/ms189964.aspx).
5. Nel riquadro a sinistra fare clic su **URL servizio Web** .
6. Fare clic su **Applica** per configurare i valori predefiniti.
7. Osservare gli **URL servizio Web ReportServer**. Tenere presente che la porta TCP predefinita è 80 e che fa parte dell'URL. Successivamente si creerà un endpoint della macchina virtuale Microsoft Azure per la porta.
8. Nel riquadro **Risultati** , verificare le azioni completate correttamente.

**Database:**

1. Nel riquadro a sinistra fare clic su **Database** .
2. Fare clic su **Modifica database**.
3. Accertarsi che l'opzione **Crea un nuovo database del server di report** sia selezionata e quindi fare clic su Avanti.
4. Verificare il **Nome server** e fare clic su **Test connessione**.
5. Se il risultato è **Test della connessione completato**, fare clic su **OK** e quindi su **Avanti**.
6. Il nome del database è **ReportServer** e la **Modalità server di report** è **Nativa**. Fare clic su **Avanti**.
7. Fare clic sul pulsante **Avanti** on the **Credenziali** .
8. Fare clic sul pulsante **Avanti** on the **Riepilogo** .
9. Fare clic sul pulsante **Avanti** on the **Continua e termina** .

**URL Portale Web o URL Gestione report per le versioni 2012 e 2014:**

1. Fare clic su **URL del portale Web** o su **URL Gestione report** per le versioni 2014 e 2012, nel riquadro a sinistra.
2. Fare clic su **Apply**.
3. Nel riquadro **Risultati** , verificare le azioni completate correttamente.
4. Fare clic su **Esci**.

Per informazioni sulle autorizzazioni del server di report, vedere [Concessione di autorizzazioni in un server di report in modalità nativa](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Passare a Gestione report locale
Per verificare la configurazione, passare a Gestione report sulla macchina virtuale.

1. Nella macchina virtuale, avviare Internet Explorer con privilegi di amministratore.
2. Passare a http://localhost/reports nella macchina virtuale.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Per connettersi a un portale Web remoto o a Gestione report per le versioni 2014 e 2012
Se si desidera connettersi al portale Web o a Gestione report per le versioni 2014 e 2012 sulla macchina virtuale da un computer remoto, creare un nuovo endpoint TCP della macchina virtuale. Per impostazione predefinita, il server di report è in ascolto delle richieste HTTP sulla **porta 80**. Se si configurano gli URL del server di report per l'utilizzo di una porta diversa, è necessario specificare il numero della porta nelle istruzioni seguenti.

1. Creare un endpoint per la macchina virtuale sulla Porta TCP 80. Per ulteriori informazioni vedere la sezione [Endpoint della macchina virtuale e porte del Firewall](#virtual-machine-endpoints-and-firewall-ports) in questo documento.
2. Aprire la porta 80 nel firewall della macchina virtuale.
3. Passare al portale Web o a Gestione report usando il **nome DNS** della macchina virtuale di Azure come nome del server nell'URL. Ad esempio:
   
    **Server di report**: http://uebi.cloudapp.net/reportserver  **Portale Web**: http://uebi.cloudapp.net/reports
   
    [Configurare un firewall per l'accesso al server di report](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Per creare e pubblicare i report in una macchina virtuale di Azure
Nella tabella seguente vengono riepilogate alcune delle opzioni disponibili per la pubblicazione di report esistenti da un computer locale nel server di report ospitato nella macchina virtuale di Microsoft Azure:

* **Generatore report**: la macchina virtuale include la versione ClickOnce di Generatore report di Microsoft SQL Server 2014 e 2012. Per avviare il Generatore report per la prima volta sulla macchina virtuale con SQL 2016:
  
  1. Avviare il browser con privilegi amministrativi.
  2. Accedere al portale Web nella macchina virtuale e selezionare l'icona di **download** in alto a destra.
  3. Selezionare **Generatore report**.
     
     Per altre informazioni, vedere [Start Report Builder](https://msdn.microsoft.com/library/ms159221.aspx)(Avviare il Generatore report).
* **SQL Server Data Tools - Macchina virtuale**: SQL Server Data Tools è installato nella macchina virtuale e può essere usato per creare **progetti server di report** e report nella macchina virtuale. SQL Server Data Tools è in grado di pubblicare i report nel server di report sulla macchina virtuale.
* **SQL Server Data Tools - Remoto**: nel computer locale creare un progetto di Reporting Services in SQL Server Data Tools contenente i report di Reporting Services. Configurare il progetto per connettersi all'URL del servizio Web.
  
    ![proprietà del progetto ssdt per un progetto SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Creare un disco rigido VHD che contenga i report e quindi caricare e collegare l'unità.
  
  1. Creare un disco rigido VHD nel computer locale che contenga i report.
  2. Creare e installare un certificato di gestione.
  3. Caricare il file VHD in Azure utilizzando il cmdlet Add-AzureVHD [Creazione e caricamento di un disco rigido virtuale di Windows Server in Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Collegare il disco alla macchina virtuale

## <a name="install-other-sql-server-services-and-features"></a>Installare altri servizi e funzionalità di SQL Server
Per installare servizi aggiuntivi di SQL Server, ad esempio Analysis Services in modalità tabulare, eseguire l'installazione guidata di SQL server. I file di installazione sono sul disco locale della macchina virtuale.

1. Fare clic su **Start** e quindi su **Tutti i programmi**.
2. Fare clic su **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** o **Microsoft SQL Server 2012** e quindi su **Strumenti di configurazione**.
3. Fare clic su **Centro installazione SQL Server**.

O eseguire C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe o C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> La prima volta che si esegue l'installazione di SQL Server, possono essere scaricati più file di installazione che richiedono un riavvio della macchina virtuale e il riavvio del programma di installazione di SQL Server.
> 
> Se è necessario personalizzare ripetutamente l'immagine selezionata dalla macchina virtuale Microsoft Azure, è consigliabile creare la propria immagine di SQL Server. È stata abilitata la funzionalità di Analysis Services SysPrep con SQL Server 2012 SP1 CU2. Per altre informazioni, vedere [Considerazioni sull'installazione di SQL Server tramite SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) e [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Supporto SysPrep per ruoli server).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Per installare la modalità tabulare di Analysis Services
I passaggi descritti in questa sezione **riepilogano** l'installazione della modalità tabulare di Analysis Services. Per altre informazioni, vedere gli argomenti seguenti: 

* [Installare Analysis Services in modalità tabulare](https://msdn.microsoft.com/library/hh231722.aspx)
* [Modellazione tabulare (esercitazione AdventureWorks)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Per installare Analysis Services in modalità tabulare:**

1. Nell'installazione guidata di SQL Server fare clic su **Installazione** nel riquadro sinistro e quindi fare clic su **Nuova installazione autonoma di SQL Server o aggiunta di funzionalità a un'installazione esistente**.
   
   * Se viene visualizzato **Sfoglia cartella**, passare a c:\SQLServer_13.0_full, c:\SQLServer_12.0_full o c:\SQLServer_11.0_full e quindi fare clic su **OK**.
2. Fare clic su **Avanti** nella pagina degli aggiornamenti del prodotto.
3. Nella pagina **Tipo di installazione** selezionare **Esegui una nuova installazione di SQL Server** e fare clic su **Avanti**.
4. Nella pagina **Impostazione ruolo** fare clic su **Installazione funzionalità SQL Server**.
5. Nella pagina **Selezione funzionalità** fare clic su **Analysis Services**.
6. Nella pagina **Configurazione istanza** digitare un nome descrittivo, ad esempio **Tabulare**, nelle caselle di testo **Istanza denominata** e **ID istanza**.
7. Nella pagina **Configurazione di Analysis Services** selezionare **Modalità tabulare**. Aggiungere l'utente corrente all'elenco delle autorizzazioni amministrative.
8. Completare e chiudere l'installazione guidata di SQL Server.

## <a name="analysis-services-configuration"></a>Configurazione di Analysis Services
### <a name="remote-access-to-analysis-services-server"></a>Accesso remoto al server Analysis Services
Il server Analysis Services supporta solo l'autenticazione di windows. Per accedere ad Analysis Services in modalità remota dalle applicazioni client, ad esempio SQL Server Management Studio o SQL Server Data Tools, la macchina virtuale deve essere aggiunta al dominio locale, utilizzando le funzionalità di rete virtuale di Azure. Per altre informazioni, vedere [Rete virtuale di Azure](../../../virtual-network/virtual-networks-overview.md).

Un'**istanza predefinita** di Analysis Services è in ascolto sulla porta TCP **2383**. Aprire la porta nel firewall delle macchine virtuali. Anche un'istanza denominata di Analysis Services in cluster è in ascolto sulla porta **2383**.

Per un' **istanza denominata** di Analysis Services, è necessario il servizio SQL Server Browser per gestire l'accesso alla porta. La configurazione predefinita di SQL Server Browser è la porta **2382**.

Nel firewall delle macchine virtuali aprire la porta **2382** e creare una porta statica dell'istanza denominata di Analysis Services.

1. Per verificare le porte che sono già in uso nella macchina virtuale e il processo utilizzato dalle porte, eseguire il comando seguente con privilegi amministrativi:
   
        netstat /ao
2. Utilizzare SQL Server Management Studio per creare una porta statica dell’istanza denominata di Analysis Services aggiornando il valore 'Porta' nelle proprietà generali dell’istanza AS tabulare. Per ulteriori informazioni, vedere "Utilizzare una porta fissa per impostazione predefinita o istanza denominata" in [Configurare Windows Firewall per consentire l'accesso ad Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Riavviare l'istanza tabulare del servizio Analysis Services.

Per ulteriori informazioni vedere la sezione **Endpoint della macchina virtuale e porte del Firewall** in questo documento.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Endpoint della macchina virtuale e porte del Firewall
Questa sezione riepiloga gli endpoint della macchina virtuale di Microsoft Azure da creare e le porte da aprire nel firewall della macchina virtuale. Nella tabella seguente sono riepilogate le porte **TCP** per creare endpoint e le porte da aprire nel firewall delle macchine virtuali.

* Se si utilizza una singola macchina virtuale e i due elementi seguenti sono true, non è necessario creare endpoint di macchina virtuale e non è necessario aprire le porte nel firewall della macchina virtuale.
  
  * La connessione alle funzionalità di SQL Server nella macchina virtuale non avviene in modalità remota. Stabilire una connessione desktop remoto alla macchina virtuale e accedere alle funzionalità di SQL Server in locale nella macchina virtuale non sono considerate operazioni di connessione remota alle funzionalità di SQL Server.
  * La macchina virtuale non è associata a un dominio locale tramite la rete virtuale di Azure o qualche altra soluzione di tunneling VPN.
* Se la macchina virtuale non è associata a un dominio ma si desidera connettersi in modalità remota alle funzionalità di SQL Server sulla macchina virtuale:
  
  * Aprire le porte nel firewall della macchina virtuale.
  * Creare endpoint della macchina virtuale per le porte indicate (*).
* Se la macchina virtuale è stata aggiunta a un dominio utilizzando un tunnel VPN, ad esempio le funzionalità di rete virtuale di Azure, gli endpoint non sono necessari. Tuttavia è possibile aprire le porte nel firewall della macchina virtuale.
  
  | Porta | Tipo | Descrizione |
  | --- | --- | --- |
  | **80** |TCP |Accesso remoto al server di report (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |Browser SQL Server. Necessario quando la macchina virtuale viene associata a un dominio. |
  | **2382** |TCP |Browser SQL Server. |
  | **2383** |TCP |Istanza predefinita di SQL Server Analysis Services e cluster delle istanze denominate. |
  | **Definito dall'utente** |TCP |Creare una porta statica dell’istanza Analysis Services per un numero di porta e sbloccare il numero di porta nel firewall. |

Per altre informazioni sulla creazione di endpoint, vedere:

* Creare endpoint:[Come configurare gli endpoint a una macchina virtuale](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQL Server: vedere la sezione "Completare la procedura di configurazione per connettersi alla macchina virtuale mediante SQL Server Management Studio" in [Provisioning di una macchina virtuale di SQL Server in Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Il diagramma seguente illustra le porte da aprire nel firewall della macchina virtuale per consentire l'accesso remoto alle funzionalità e i componenti nella macchina virtuale.

![porte da aprire per applicazioni di Business Intelligence in macchine virtuali di Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Risorse
* Esaminare i criteri di supporto per il software server Microsoft utilizzato nell'ambiente di macchina virtuale di Azure. L'argomento seguente riepiloga il supporto per funzionalità quali BitLocker, Clustering di Failover e bilanciamento carico di rete. [Supporto di software server Microsoft per le macchine virtuali Microsoft Azure](http://support.microsoft.com/kb/2721672).
* [Panoramica di SQL Server in Macchine virtuali di Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Provisioning di una macchina virtuale di SQL Server in Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Come collegare un disco dati a una macchina virtuale](../classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migrazione di un database a SQL Server su una macchina virtuale di Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Determinare la modalità Server di un'istanza di Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)
* [Modellazione multidimensionale (esercitazione AdventureWorks)](https://technet.microsoft.com/library/ms170208.aspx)
* [Centro di documentazione di Azure](https://azure.microsoft.com/documentation/)
* [Utilizzo di Power BI in un ambiente ibrido](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [È possibile inviare commenti e suggerimenti e informazioni di contatto tramite Microsoft SQL Server Connect](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Contenuti della community
* [Gestione del database SQL di Azure con PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)


