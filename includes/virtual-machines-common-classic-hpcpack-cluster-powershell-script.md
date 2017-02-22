



A seconda dell'ambiente e delle scelte effettuate, lo script può creare tutta l'infrastruttura del cluster, inclusi la rete virtuale di Azure, gli account di archiviazione, i servizi cloud, il controller di dominio, i database SQL locali o remoti, il nodo head e altri nodi del cluster. In alternativa, lo script può usare l'infrastruttura di Azure esistente e creare solo i nodi del cluster HPC.

Per informazioni generali sulla pianificazione di un cluster HPC Pack, vedere la [valutazione e pianificazione del prodotto](https://technet.microsoft.com/library/jj899596.aspx) e l'[introduzione](https://technet.microsoft.com/library/jj899590.aspx) nella libreria TechNet per HPC Pack 2012 R2.

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**: è possibile usare una sottoscrizione nel servizio Azure globale o Azure Cina. I limiti della sottoscrizione riguardano il numero e il tipo di nodi del cluster che è possibile distribuire. Per informazioni, vedere [Limiti, quote e vincoli delle sottoscrizioni e dei servizi di Microsoft Azure](../articles/azure-subscription-service-limits.md).
* **Computer client Windows in cui è stato installato e configurato Azure PowerShell 0.8.10 o versioni successive**. Vedere [Introduzione ad Azure PowerShell](/powershell/azureps-cmdlets-docs) per le istruzioni di installazione e le operazioni per la connessione alla sottoscrizione di Azure.
* **Script di distribuzione di HPC Pack IaaS**: scaricare e decomprimere la versione più recente dello script dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Controllare la versione dello script eseguendolo `New-HPCIaaSCluster.ps1 –Version`. Questo articolo si basa sulla versione 4.5.2 dello script.
* **File di configurazione script**: creare un file XML usato dallo script per configurare il cluster HPC. Per informazioni ed esempi, vedere le sezioni più avanti in questo articolo e il file Manual.rtf che accompagna lo script di distribuzione.

## <a name="syntax"></a>Sintassi
```PowerShell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> Eseguire lo script come amministratore.
> 
> 

### <a name="parameters"></a>parameters
* **ConfigFile**: specifica il percorso del file di configurazione che descrive il cluster HPC. Altre informazioni sul file di configurazione sono disponibili in questo argomento o nel file Manual.rtf, incluso nella cartella che contiene lo script.
* **AdminUserName**: specifica il nome utente. Se la foresta di domini viene creata dallo script, si tratta del nome utente amministratore locale per tutte le macchine virtuali, nonché del nome dell'amministratore di dominio. Se la foresta di domini esiste già, specifica l'utente di dominio come nome utente amministratore locale per installare HPC Pack.
* **AdminPassword**: specifica la password dell'amministratore. Se non è specificata nella riga di comando, lo script richiederà di immettere la password.
* **HPCImageName** (facoltativo): specifica il nome immagine di macchina virtuale HPC Pack usato per distribuire il cluster HPC. Deve essere un'immagine HPC Pack fornita da Microsoft da Azure Marketplace. Se non è specificata (scelta generalmente consigliata), lo script sceglie l'ultima [immagine HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) pubblicata. L'immagine corrente è basata su Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 3 installato.
  
  > [!NOTE]
  > Se non si specifica un'immagine HPC Pack valida, la distribuzione avrà esito negativo.
  > 
  > 
* **LogFile** (facoltativo): specifica il percorso del file di log della distribuzione. Se non è specificato, lo script creerà un file di log nella directory temporanea del computer che esegue lo script.
* **Force** (facoltativo): elimina tutte le richieste di conferma.
* **NoCleanOnFailure** (facoltativo): specifica che le macchine virtuali di Azure non distribuite correttamente non verranno rimosse. Rimuovere manualmente queste macchine virtuali prima di eseguire nuovamente lo script per continuare la distribuzione. In caso contrario, la distribuzione potrebbe non riuscire.
* **PSSessionSkipCACheck** (facoltativo): per ogni servizio cloud con VM distribuite da questo script, viene automaticamente generato da Azure un certificato autofirmato che viene usato da tutte le VM nel servizio cloud come certificato di Gestione remota Windows (WinRM) predefinito. Per distribuire funzionalità HPC in queste macchine virtuali di Azure, per impostazione predefinita lo script installa temporaneamente questi certificati nell'archivio computer locale\\Autorità di certificazione radice disponibile nell'elenco locale del computer client, in modo da eliminare l'errore di sicurezza relativo all'autorità di certificazione non attendibile durante l'esecuzione dello script. I certificati vengono rimossi al termine dello script. Se questo parametro è specificato, i certificati non vengono installati nel computer client e l'avviso di sicurezza viene eliminato.
  
  > [!IMPORTANT]
  > Questo parametro non è consigliato per distribuzioni di produzione.
  > 
  > 

### <a name="example"></a>Esempio
L'esempio seguente crea un cluster HPC Pack usando il file di configurazione *MyConfigFile.xml* e specifica le credenziali amministrative per l'installazione del cluster.

```PowerShell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Considerazione aggiuntive
* Lo script può facoltativamente consentire l'invio di processi tramite il portale Web di HPC Pack o l'API REST HPC Pack.
* Se si vuole installare altro software o configurare altre impostazioni, lo script può facoltativamente eseguire script di preconfigurazione e post-configurazione personalizzati nel nodo head.

## <a name="configuration-file"></a>File di configurazione
Il file di configurazione per lo script di distribuzione è un file XML. Il file di schema HPCIaaSClusterConfig.xsd si trova nella cartella dello script di distribuzione IaaS di HPC Pack. **IaaSClusterConfig** è l'elemento radice del file di configurazione, che contiene gli elementi figlio descritti in dettaglio nel file Manual.rtf nella cartella dello script di distribuzione.



<!--HONumber=Jan17_HO2-->


