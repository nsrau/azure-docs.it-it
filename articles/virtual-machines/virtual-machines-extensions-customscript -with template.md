<properties
   pageTitle="Utilizzo dei modelli di Gestione risorse di Azure dell’estensione di script personalizzato"
   description="Automazione delle attività di configurazione delle macchine virtuali di Azure utilizzando lo script personalizzato per modelli di Gestione risorse di Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="madhana"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/01/2015"
   ms.author="kundanap"/>

# Utilizzo dell’estensione di script personalizzato con modelli di gestione risorse di Azure

In questo articolo viene fornita una panoramica sulla scrittura di modelli di gestione risorse di Azure con l’estensione di script personalizzato per l'avvio di carichi di lavoro in una macchina virtuale Linux o Windows.

Per una panoramica sull’estensione di script personalizzato, fare clic <a href="https://azure.microsoft.com/it-it/documentation/articles/virtual-machines-extensions-customscript/" target="_blank">qui</a>.

A partire dall’introduzione, l'estensione di script personalizzato è stata utilizzata ampiamente per configurare i carichi di lavoro su macchine virtuali Linux e Windows. Con l'introduzione dei modelli di Gestione risorse di Azure, gli utenti possono ora creare un singolo modello che non solo esegue il provisioning della macchina virtuale ma ne configura anche i carichi di lavoro.

## Panoramica dei modelli di Gestione risorse di Azure

I modelli di Gestione risorse di Azure consentono di specificare in modo dichiarativo l'infrastruttura IaaS di Azure in linguaggio Json definendo le dipendenze tra risorse. Per una panoramica dettagliata dei modelli di Gestione risorse di Azure, consultare gli articoli seguenti:

<a href="https://azure.microsoft.com/it-it/documentation/articles/resource-group-overview/" target="_blank">Panoramica del gruppo di risorse</a>. <br/><a href="https://azure.microsoft.com/it-it/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">Distribuire modelli con l'interfaccia della riga di comando di Azure</a>. <br/> <a href="https://azure.microsoft.com/it-it/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">Distribuire modelli con Azure Powershell</a>.

### Prerequisiti per l'esecuzione dell'estensione Script personalizzato

1. Installare i cmdlet di Azure PowerShell o la CLI di Azure più recenti da <a href="http://azure.microsoft.com/downloads" target="_blank">qui</a>.
2. Se gli script vengono eseguiti in una macchina virtuale esistente, assicurarsi che l'agente VM sia abilitato nella macchina virtuale; in caso contrario, fare riferimento a questo <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">articolo</a> per installarne uno.
3. Caricare gli script da eseguire sulla macchina virtuale in Archiviazione di Azure. Gli script possono provenire da uno o più contenitori di archiviazione.
4. In alternativa è possibile caricare gli script in un account Github.
5. Lo script deve essere creato in modo tale che lo script di ingresso che viene avviato dall'estensione a turno avvii altri script.

## Panoramica dell'utilizzo dell’estensione di script personalizzato con i modelli:

Per la distribuzione con i modelli si utilizza la stessa versione dell'estensione di script personalizzato disponibile per l'API di gestione dei servizi di Azure. L'estensione supporta gli stessi parametri e scenari, come il caricamento dei file nell’account di archiviazione di Azure o nel percorso Github. La differenza principale quando si utilizzano i modelli consiste nella versione esatta dell'estensione che deve essere specificata anziché specificare la versione nel formato majorversion.*.

 Frammento di modello per l'estensione di script personalizzato in una macchina virtuale Linux

Definire la seguente risorsa di estensione nella sezione delle risorse del modello

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

## Frammento di modello per l'estensione di script personalizzato in una macchina virtuale Windows

Definire la seguente risorsa nella sezione delle risorse del modello

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.4",
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

Negli esempi precedenti, sostituire l'URL di file e il nome del file con le proprie impostazioni.

Dopo la creazione del modello, è possibile distribuirlo tramite l’interfaccia della riga di comando di Azure oppure Azure Powershell.

Consultare gli esempi seguenti per configurare applicazioni in una macchina virtuale utilizzando l'estensione di script personalizzato.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Estensione di script personalizzato in una macchina virtuale Linux</a>. </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Estensione di script personalizzato in una macchina virtuale Windows</a>.

<!---HONumber=August15_HO6-->