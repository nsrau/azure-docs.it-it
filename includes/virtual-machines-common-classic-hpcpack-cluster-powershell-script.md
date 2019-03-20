---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2df08968ad66bd330611b975c045c9e9c9b240aa
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "58115073"
---
A seconda dell'ambiente e delle scelte effettuate, lo script può creare tutta l'infrastruttura del cluster, inclusi la rete virtuale di Azure, gli account di archiviazione, i servizi cloud, il controller di dominio, i database SQL locali o remoti, il nodo head e altri nodi del cluster. In alternativa, lo script può usare l'infrastruttura di Azure esistente e creare solo i nodi del cluster HPC.

Per informazioni generali sulla pianificazione di un cluster HPC Pack, vedere la [valutazione e pianificazione del prodotto](https://technet.microsoft.com/library/jj899596.aspx) e l'[introduzione](https://technet.microsoft.com/library/jj899590.aspx) nella libreria TechNet per HPC Pack 2012 R2.

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**: È possibile usare una sottoscrizione nel servizio Azure globale o Azure Cina. I limiti della sottoscrizione riguardano il numero e il tipo di nodi del cluster che è possibile distribuire. Per informazioni, vedere [Limiti, quote e vincoli delle sottoscrizioni e dei servizi di Microsoft Azure](../articles/azure-subscription-service-limits.md).
* **Computer client Windows con Azure PowerShell 0.8.10 o versione successiva installato e configurato**: Visualizzare [iniziare a usare Azure PowerShell](/powershell/azureps-cmdlets-docs) per istruzioni sull'installazione e i passaggi per connettersi alla sottoscrizione di Azure.
* **Script di distribuzione IaaS di HPC Pack**: Scaricare e decomprimere la versione più recente dello script dal [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Controllare la versione dello script eseguendolo `New-HPCIaaSCluster.ps1 –Version`. Questo articolo si basa sulla versione 4.5.2 dello script.
* **File di configurazione script**: Creare un file XML usato dallo script per configurare il cluster HPC. Per informazioni ed esempi, vedere le sezioni più avanti in questo articolo e il file Manual.rtf che accompagna lo script di distribuzione.

## <a name="syntax"></a>Sintassi
```PowerShell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> Eseguire lo script come amministratore.
> 
> 

### <a name="parameters"></a>Parametri
* **ConfigFile**: Specifica il percorso del file di configurazione che descrive il cluster HPC. Altre informazioni sul file di configurazione sono disponibili in questo argomento o nel file Manual.rtf, incluso nella cartella che contiene lo script.
* **AdminUserName**: Specifica il nome utente. Se la foresta di domini viene creata dallo script, si tratta del nome utente amministratore locale per tutte le macchine virtuali, nonché del nome dell'amministratore di dominio. Se la foresta di domini esiste già, specifica l'utente di dominio come nome utente amministratore locale per installare HPC Pack.
* **AdminPassword**: Specifica la password dell'amministratore. Se non è specificata nella riga di comando, lo script richiederà di immettere la password.
* **HPCImageName** (facoltativo): Specifica il nome dell'immagine di VM di HPC Pack usato per distribuire il cluster HPC. Deve essere un'immagine HPC Pack fornita da Microsoft da Azure Marketplace. Se non è specificata (scelta generalmente consigliata), lo script sceglie l'ultima [immagine HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) pubblicata. L'immagine corrente è basata su Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 3 installato.
  
  > [!NOTE]
  > Se non si specifica un'immagine HPC Pack valida, la distribuzione avrà esito negativo.
  > 
  > 
* **File di log** (facoltativo): Specifica il percorso di file di log di distribuzione. Se non è specificato, lo script creerà un file di log nella directory temporanea del computer che esegue lo script.
* **Force** (facoltativo): Elimina tutte le richieste di conferma.
* **NoCleanOnFailure** (facoltativo): Specifica che le macchine virtuali di Azure distribuite non correttamente non vengono rimossi. Rimuovere manualmente queste macchine virtuali prima di eseguire nuovamente lo script per continuare la distribuzione. In caso contrario, la distribuzione potrebbe non riuscire.
* **PSSessionSkipCACheck** (optional): Per ogni servizio cloud con macchine virtuali distribuite da questo script, un certificato autofirmato viene generato automaticamente da Azure e tutte le macchine virtuali nel servizio cloud utilizzano questo certificato come certificato di gestione remota Windows (WinRM) predefinito. Per distribuire funzionalità HPC in queste macchine virtuali di Azure, per impostazione predefinita lo script installa temporaneamente questi certificati nell'archivio computer locale\\Autorità di certificazione radice disponibile nell'elenco locale del computer client, in modo da eliminare l'errore di sicurezza relativo all'autorità di certificazione non attendibile durante l'esecuzione dello script. I certificati vengono rimossi al termine dello script. Se questo parametro è specificato, i certificati non vengono installati nel computer client e l'avviso di sicurezza viene eliminato.
  
  > [!IMPORTANT]
  > Questo parametro non è consigliato per distribuzioni di produzione.
  > 
  > 

### <a name="example"></a>Esempio
L'esempio seguente crea un cluster HPC Pack usando il file di configurazione *MyConfigFile.xml* e specifica le credenziali amministrative per l'installazione del cluster.

```PowerShell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Ulteriori considerazioni
* Lo script può facoltativamente consentire l'invio di processi tramite il portale Web di HPC Pack o l'API REST HPC Pack.
* Se si vuole installare altro software o configurare altre impostazioni, lo script può facoltativamente eseguire script di preconfigurazione e post-configurazione personalizzati nel nodo head.

## <a name="configuration-file"></a>File di configurazione
Il file di configurazione per lo script di distribuzione è un file XML. Il file di schema HPCIaaSClusterConfig.xsd si trova nella cartella dello script di distribuzione IaaS di HPC Pack. **IaaSClusterConfig** è l'elemento radice del file di configurazione, che contiene gli elementi figlio descritti in dettaglio nel file Manual.rtf nella cartella dello script di distribuzione.

