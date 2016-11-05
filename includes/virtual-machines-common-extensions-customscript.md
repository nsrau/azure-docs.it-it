

Fin dalla sua introduzione, l'estensione script personalizzato è stata ampiamente usata per configurare i carichi di lavoro nelle macchine virtuali Windows e Linux. Con l'introduzione dei modelli di Gestione risorse di Azure, gli utenti possono ora creare un singolo modello che non solo esegue il provisioning della macchina virtuale ma ne configura anche i carichi di lavoro.

## Informazioni sui modelli di Gestione risorse di Azure
I modelli di Gestione risorse di Azure consentono di specificare in modo dichiarativo l'infrastruttura IaaS di Azure in linguaggio Json definendo le dipendenze tra risorse. Per una panoramica dettagliata dei modelli di Gestione risorse di Azure, consultare gli articoli seguenti:

* [Panoramica del gruppo di risorse](../articles/resource-group-overview.md)
* [Distribuire modelli con Azure Powershell](../articles/virtual-machines/virtual-machines-windows-ps-manage.md)

### Prerequisiti
1. Scaricare gli strumenti della riga di comando di Azure per il sistema operativo in uso da [qui](https://azure.microsoft.com/downloads/).
2. Se gli script vengono eseguiti in una VM esistente, assicurarsi che l'agente VM sia abilitato nella VM e, in caso contrario, seguire le istruzioni di [Linux](../articles/virtual-machines/virtual-machines-linux-classic-manage extensions.md) o di [Windows](../articles/virtual-machines/virtual-machines-windows-classic-manage extensions.md) per installarne uno.
3. Caricare gli script da eseguire sulla macchina virtuale in Archiviazione di Azure. Gli script possono provenire da uno o più contenitori di archiviazione.
4. In alternativa è possibile caricare gli script in un account GitHub.
5. Lo script deve essere creato in modo tale che lo script di ingresso che viene avviato dall'estensione a turno avvii altri script.

## Utilizzo dell'estensione Script personalizzato
Per la distribuzione con i modelli si usa la stessa versione dell'estensione di script personalizzato disponibile per le API di Gestione dei servizi di Azure. L'estensione supporta gli stessi parametri e scenari, come il caricamento dei file nell’account di archiviazione di Azure o nel percorso Github. La differenza principale quando si utilizzano i modelli consiste nella versione esatta dell'estensione che deve essere specificata anziché specificare la versione nel formato majorversion.*.

<!----HONumber=AcomDC_0420_2016-->