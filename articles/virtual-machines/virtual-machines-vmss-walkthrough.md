<properties
	pageTitle="Ridimensionare automaticamente i set di scalabilità di macchine virtuali | Microsoft Azure"
	description="Iniziare a creare e gestire i primi set di scalabilità di macchine virtuali di Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# Ridimensionare automaticamente i computer in un set di scalabilità di macchine virtuali

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md).


Questo documento è una guida rapida per iniziare a creare e gestire i primi set di scalabilità delle macchine virtuali di Azure durante l'anteprima pubblica.

La guida presuppone che si abbia già familiarità con l'uso dei modelli di Gestione risorse di Azure (ARM) per distribuire le risorse di Azure, ad esempio macchine virtuali e reti virtuali. In caso contrario, i primi 3 collegamenti della sezione Risorse forniscono una panoramica di Gestione risorse.

## Che cosa sono i set di scalabilità VM e perché usarli?

I set di scalabilità VM sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire un set di VM identiche.

Con tutte le VM configurate allo stesso modo, i set di scalabilità VM sono progettati per supportare la vera scalabilità automatica (non è necessario il pre-provisioning delle VM) e facilitare quindi la compilazione di servizi su larga scala per il Big Compute, i Big Data, i carichi di lavoro nei contenitori e qualsiasi applicazione abbia necessità di aumentare o ridurre le risorse di calcolo. Le operazioni di scalabilità vengono implicitamente bilanciate tra domini di errore e di aggiornamento. Per un'introduzione dei set di scalabilità VM, vedere il recente [annuncio nel blog di Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview).

## Creazione e uso dei set di scalabilità VM

I set di scalabilità VM possono essere definiti e distribuiti usando modelli JSON e chiamate REST esattamente come le singole VM di Gestione risorse di Azure. È quindi possibile usare qualsiasi metodo di distribuzione standard di Gestione risorse di Azure, di cui questo documento illustra alcuni esempi.

Le aree specifiche di integrazione dei set di scalabilità delle macchine virtuali, ad esempio il supporto per i comandi imperativi nei linguaggi di scripting e di programmazione, e l'integrazione completa con il portale sono in fase di sviluppo e verranno rilasciate in più fasi durante l'anteprima.

## Distribuzione e gestione di set di scalabilità VM con il portale di Azure

Inizialmente non sarà possibile creare da zero un set di scalabilità VM nel portale di Azure. Poiché si sta ancora lavorando al supporto del portale, in questa prima fase è possibile visualizzare nel portale solo i set di scalabilità già creati e, per crearli, sarà necessario ricorrere a un approccio basato su modelli/script. In tutti i casi, con "portale" si intende il nuovo portale di Azure: [https://portal.azure.com/](https://portal.azure.com/).

È tuttavia possibile usare il portale per distribuire un modello per la distribuzione di qualsiasi risorsa, inclusi i set di scalabilità. È possibile distribuire facilmente un set di scalabilità semplice usando un collegamento come questo:

_https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>_

Un set di modelli di esempio per i set di scalabilità VM è disponibile nel repository GitHub dei modelli di avvio rapido di Azure, all'indirizzo: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates). Cercare i modelli con _vmss_ nel titolo.

Nei file leggimi di questi modelli sarà visualizzato un pulsante come questo che collega alla funzionalità di distribuzione del portale.

Per distribuire il set di scalabilità, fare clic sul pulsante e quindi immettere tutti i parametri richiesti nel portale. Nota: se non si è certi se una risorsa supporti le lettere maiuscole o miste, è più sicuro usare sempre valori di parametri con lettere minuscole.

**Monitoraggio della risorsa del set di scalabilità VM nel portale o in Esplora risorse**

Dopo la distribuzione di un set di scalabilità, il portale mostrerà una visualizzazione di base del set, ma inizialmente, durante l'anteprima, non mostrerà i dettagli delle proprietà né consentirà di eseguire il drill-down delle VM in esecuzione nel set di scalabilità VM.

Finché non viene ultimata l'integrazione completa del portale, è consigliabile usare **Esplora risorse di Azure** ([https://resources.azure.com](https://resources.azure.com)) per visualizzare i set di scalabilità VM. I sei di scalabilità VM sono una risorsa di Microsoft.Compute, perciò da questo sito è possibile visualizzarli espandendo i collegamenti seguenti:

Sottoscrizioni -> sottoscrizione utente -> resourceGroups -> provider -> Microsoft.Compute -> virtualMachineScaleSets -> set di scalabilità VM utente -> e così via.

## Distribuzione e gestione di set di scalabilità VM con PowerShell

È possibile distribuire i modelli di set di scalabilità delle macchine virtuali ed eseguire una query delle risorse con qualsiasi versione corrente di Azure PowerShell.

**Nota importante:** gli esempi mostrati sotto sono relativi ad [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/) in cui è stato introdotto il prefisso _AzureRm_ per molti comandi. Se si usa una versione meno recente di PowerShell, ad esempio 0.9.8 o precedente, rimuovere "**Rm**" dai comandi di esempio. L'esempio presuppone anche che sia già stata stabilita una connessione di accesso ad Azure nell'ambiente PowerShell (_Login-AzureRmAccount_).

Esempi:

&#35; **Creare un gruppo di risorse**

New-AzureRmResourcegroup -name myrg -location 'Southeast Asia'

&#35; **Creare un set di scalabilità di 2 VM**

New-AzureRmResourceGroupDeployment -name dep1 -vmSSName myvmss -instanceCount 3 -ResourceGroupName myrg -TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

Verranno richiesti eventuali parametri mancanti (in questo esempio, posizione, nome utente e password).

&#35; **Ottenere i dettagli di un set di scalabilità VM**

Get-AzureRmResource -name myvmss -ResourceGroupName myrg -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

&#35; oppure, per altri dettagli, inviare pipe con | ConvertTo-Json -Depth 10

&#35; oppure, per altri dettagli ancora, aggiungere –debug al comando.

&#35; Aumento o riduzione

New-AzureRmResourceGroupDeployment -name dep2 -vmSSName myvmss -instanceCount 2 -ResourceGroupName myrg -TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json)

&#35; **Rimuovere un set di scalabilità**

&#35; Esempio semplice: rimuove l'intero gruppo di risorse e il relativo contenuto:

Remove-AzureRmResourceGroup -Name myrg

&#35; Esempio preciso: rimuove una risorsa specifica:

Remove-AzureRmResource -Name myvmss -ResourceGroupName myrg -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

### Comandi di PowerShell imperativi per i set di scalabilità

Una delle prossime build di Azure PowerShell includerà un set di comandi imperativi per creare e gestire set di scalabilità VM senza usare i modelli. Questa build è disponibile in anteprima all'indirizzo [https://github.com/AzureRT/azure-powershell/releases](https://github.com/AzureRT/azure-powershell/releases). Per esempi dell'uso dell'API imperativa, fare clic qui:

[https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1](https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1)

## Distribuzione e gestione di set di scalabilità VM con l'interfaccia della riga di comando di Azure

È possibile distribuire i modelli di set di scalabilità delle macchine virtuali ed eseguire una query delle risorse con qualsiasi versione corrente dell'interfaccia della riga di comando di Azure.

Il modo più semplice per installare l'interfaccia della riga di comando è da un contenitore Docker. Per l'installazione, vedere: [https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/](https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/)

Per l'uso dell'interfaccia della riga di comando, vedere: [https://azure.microsoft.com/documentation/articles/xplat-cli/](https://azure.microsoft.com/documentation/articles/xplat-cli/)

### Esempi di interfaccia della riga di comando per i set di scalabilità VM

&#35; **Creare un gruppo di risorse**

azure group create myrg "Southeast Asia"

&#35; **Creare un set di scalabilità VM**

azure group deployment create -g myrg -n dep2 --template-uri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

&#35; Questo comando dovrebbe chiedere i parametri in modo dinamico oppure è possibile specificarli come argomenti

&#35; **Ottenere i dettagli di un set di scalabilità**

azure resource show -n vmssname -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g myrg

&#35; Oppure, per altri dettagli:

azure resource show –n vmssname –r Microsoft.Compute/virtualMachineScaleSets –o 2015-06-15 –g myrg –json –vv

Una delle prossime build dell'interfaccia della riga di comando di Azure includerà comandi imperativi per distribuire e gestire i set di scalabilità VM direttamente senza modelli ed eseguire le operazioni sulle VM nei set di scalabilità VM. È possibile tenere traccia di questa build all'indirizzo: [https://github.com/AzureRT/azure-xplat-cli/releases/](https://github.com/AzureRT/azure-xplat-cli/releases/)

## Modelli di set di scalabilità VM

Questa sezione illustra un semplice modello di Azure per creare un set di scalabilità VM e lo confronta con i modelli usati per creare macchine virtuali a istanza singola. È anche disponibile un'utile analisi video di un modello di set di scalabilità VM all'indirizzo: [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

### Anatomia di un modello

Si inizierà con un modello semplice che crea un set di scalabilità di macchine virtuali Ubuntu e lo si suddividerà in più componenti. Questo esempio crea anche le risorse da cui dipende un set di scalabilità VM, ad esempio la rete virtuale, l'account di archiviazione e così via. L'esempio è disponibile qui: [https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json](https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json). Si noti che per questo esempio "hello world" non sarà possibile connettersi direttamente alle VM del set di scalabilità dall'esterno della rete virtuale perché alle VM sono assegnati indirizzi IP interni. Per informazioni su come connettersi dall'esterno con i servizi di bilanciamento del carico o i jumpbox, vedere la sezione degli scenari e gli esempi nella pagina dei [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates).

### Intestazione del modello

Specificare lo schema e la versione del contenuto:

{

   "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json",

   "contentVersion": "1.0.0.0",

### Parametri

Come per gli altri modelli di Gestione risorse di Azure (ARM), questa sezione definisce i parametri specificati in fase di distribuzione, inclusi, in questo esempio, il nome del set di scalabilità VM, il numero di istanze VM con cui iniziare e una stringa univoca da usare quando si creano gli account di archiviazione (usare sempre lettere minuscole quando si immettono i valori per oggetti come gli account di archiviazione, a meno che non si conosca l'uso delle lettere maiuscole e minuscole).

````
 "parameters": {

    "vmSSName": {

      "type": "string",

      "metadata": {

        "description": "Scale Set name, also used in this template as a base for naming resources (hence limited less than 10 characters)."

      },

      "maxLength": 10

    },

    "instanceCount": {

      "type": "int",

      "metadata": {

        "description": "Number of VM instances"

      },

      "maxValue": 100

    },

    "adminUsername": {

      "type": "string",

      "metadata": {

        "description": "Admin username on all VMs."

      }

    },

    "adminPassword": {

      "type": "securestring",

      "metadata": {

        "description": "Admin password on all VMs."

      }

    }

  },
````

### Variabili

Componente del modello ARM standard che definisce le variabili a cui si farà riferimento più avanti nel modello. In questo esempio si useranno le variabili per definire il sistema operativo scelto, alcuni dettagli della configurazione di rete, le impostazioni di archiviazione e la posizione. Per la posizione verrà usata la funzione _location()_ per selezionarla dal gruppo di risorse in cui viene distribuita.

Si noti che viene definita una matrice di stringhe univoche per un prefisso dell'account di archiviazione. Per una spiegazione, vedere la sezione relativa all'archiviazione.

````
  "variables": {

    "apiVersion": "2015-06-15",

    "newStorageAccountSuffix": "[concat(parameters('vmssName'), 'sa')]",

    "uniqueStringArray": [

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '0')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '1')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '2')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '3')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '4')))]"

    ],

    "vmSize": "Standard\_A1",

    "vhdContainerName": "[concat(parameters('vmssName'), 'vhd')]",

    "osDiskName": "[concat(parameters('vmssName'), 'osdisk')]",

    "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",

    "subnetName": "[concat(parameters('vmssName'), 'subnet')]",

    "nicName": "[concat(parameters('vmssName'), 'nic')]",

    "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",

    "addressPrefix": "10.0.0.0/16",

    "subnetPrefix": "10.0.0.0/24",

    "storageAccountType": "Standard\_LRS",

    "location": "[resourceGroup().location]",

    "osType": {

      "publisher": "Canonical",

      "offer": "UbuntuServer",

      "sku": "15.10",

      "version": "latest"

    },

    "imageReference": "[variables('osType')]"

  },
````

### Risorse

In questa sezione viene definito ogni tipo di risorsa.

````
   "resources": [
````


**Archiviazione.** Quando si crea un set di scalabilità VM, si specifica una matrice di account di archiviazione. I dischi del sistema operativo per le istanze VM verranno quindi distribuiti equamente tra ogni account. In futuro i set di scalabilità VM adotteranno un approccio basato su disco gestito in cui non sarà necessario gestire gli account di archiviazione, ma sarà sufficiente descrivere le proprietà di archiviazione durante la definizione del set di scalabilità. Per ora, si dovrà creare in anticipo tutti gli account di archiviazione necessari. È consigliabile creare 5 account di archiviazione, che supporteranno senza problemi fino a 100 VM in un set di scalabilità (il massimo, per il momento).

Quando viene creato un set di account di archiviazione, gli account vengono distribuiti nelle partizioni in un timbro di archiviazione e lo schema di distribuzione dipende dalle prime lettere del nome degli account di archiviazione. Perciò, per ottenere prestazioni ottimali, è consigliabile che ogni account di archiviazione creato inizi con una lettera diversa dell'alfabeto. È possibile assegnare un nome manualmente oppure, per questo esempio, usare la funzione uniqueString() per eseguire una distribuzione pseudocasuale:

````
    {

      "type": "Microsoft.Storage/storageAccounts",

      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "copy": {

        "name": "storageLoop",

        "count": 5

      },

      "properties": {

        "accountType": "[variables('storageAccountType')]"

      }

    },
````

**Rete virtuale.** Creare una rete virtuale. Si noti che possono essere presenti più set di scalabilità, oltre alle singole VM nella stessa rete virtuale.

````
    {

      "type": "Microsoft.Network/virtualNetworks",

      "name": "[variables('virtualNetworkName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "properties": {

        "addressSpace": {

          "addressPrefixes": [

            "[variables('addressPrefix')]"

          ]

        },

        "subnets": [

          {

            "name": "[variables('subnetName')]",

            "properties": {

              "addressPrefix": "[variables('subnetPrefix')]"

            }

          }

        ]

      }

    },
````

### Risorsa virtualMachineScaleSets

Sotto molti aspetti la risorsa _virtualMachineScaleSets_ è simile a una risorsa _virtualMachines_. Entrambe vengono fornite dal provider di risorse Microsoft.Compute e sono allo stesso livello. La differenza principale è che si specifica un valore _capacity_ che indica quante VM verranno create e che quanto si definisce qui è valido per tutte le VM del set di scalabilità.

Si noti che la sezione _dependsOn_ fa riferimento agli account di archiviazione e alla rete virtuale creati prima e che la capacità fa riferimento al parametro _instanceCount_.

````
    {

      "type": "Microsoft.Compute/virtualMachineScaleSets",

      "name": "[parameters('vmssName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "dependsOn": [

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"

      ],

      "sku": {

        "name": "[variables('vmSize')]",

        "tier": "Standard",

        "capacity": "[parameters('instanceCount')]"

      },
````

**Proprietà**

I set di scalabilità VM hanno un'impostazione upgradePolicy. Le versioni future supporteranno gli aggiornamenti suddivisi (ad esempio, la modifica della configurazione per il 20% delle VM per volta), ma per ora upgradePolicy può essere impostato come manuale o automatico. L'aggiornamento manuale comporta che, se si modifica il modello e lo si ridistribuisce, le modifiche verranno applicate solo quando verranno create nuove VM o verranno aggiornate le estensioni, ad esempio quando si aumenta il valore _capacity_. L'aggiornamento automatico comporta l'aggiornamento immediato di tutte le VM, riavviando tutto. Quello manuale in genere è un approccio più sicuro. È possibile eliminare le singole VM e ridistribuirle come è necessario per un aggiornamento graduale.

````
      "properties": {

         "upgradePolicy": {

         "mode": "Manual"

        },
````

**Proprietà->virtualMachineProfile**

Qui si fa riferimento agli account di archiviazione creati prima come a contenitori per le VM. Non è necessario creare prima i contenitori veri e propri, ma solo gli account.

````
        "virtualMachineProfile": {

          "storageProfile": {

            "osDisk": {

              "vhdContainers": [

                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"

              ],

              "name": "[variables('osDiskName')]",

              "caching": "ReadOnly",

              "createOption": "FromImage"

            },

            "imageReference": "[variables('imageReference')]"

          },
````

**Proprietà->osProfile**

Una differenza dei set di scalabilità VM rispetto alle VM singole è che il nome computer è un prefisso. Le istanze delle VM nel set di scalabilità verranno create con nomi come myvm\_0, myvm\_1 e così via.

````
          "osProfile": {

            "computerNamePrefix": "[parameters('vmSSName')]",

            "adminUsername": "[parameters('adminUsername')]",

            "adminPassword": "[parameters('adminPassword')]"

          },
````

**Proprietà->networkProfile**

Quando si definisce il profilo di rete per una set di scalabilità VM, ricordare che sia la configurazione della scheda di interfaccia di rete che la configurazione IP hanno un nome. La configurazione della scheda di interfaccia di rete ha un'impostazione "_primary_" e l'ID subnet si riferisce alla risorsa subnet creata prima come parte della rete virtuale.

````
          "networkProfile": {

            "networkInterfaceConfigurations": [

              {

                "name": "[variables('nicName')]",

                "properties": {

                  "primary": "true",

                  "ipConfigurations": [

                    {

                      "name": "[variables('ipConfigName')]",

                      "properties": {

                        "subnet": {

                          "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'), '/subnets/', variables('subnetName'))]"

                        }

                      }

                    }

                  ]

                }

              }

            ]

          }
````

**Proprietà->extensionProfile**

Il semplice esempio precedente non ha richiesto un profilo estensione. È possibile vederne uno in azione in questo modello che distribuisce Apache e PHP con l'estensione CustomScript: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale). Si noti che in questo esempio le proprietà dell'IP di rete fanno riferimento anche a un servizio di bilanciamento del carico. Si parlerà ancora dei servizi di bilanciamento del carico nella sezione Scenari di set di scalabilità VM.

## Scenari di set di scalabilità VM

Questa sezione illustra alcuni tipici scenari di set di scalabilità VM e modelli di esempio. Anche se per ora richiedono i modelli, alcuni di essi verranno integrati nel portale in futuro. Anche alcuni servizi di Azure di livello più elevato (ad esempio, Batch, infrastruttura di servizi, servizio contenitore di Azure) useranno questi scenari.

## Connessione RDP/SSH alle istanze di set di scalabilità VM

Un set di scalabilità VM viene creato in una rete virtuale e alle singole VM non vengono allocati indirizzi IP pubblici. Questo è positivo perché in genere non si vogliono sostenere le spese e il sovraccarico di gestione richiesti dall'allocazione di indirizzi IP distinti a tutte le risorse senza stato della griglia di calcolo ed è possibile connettersi facilmente a queste VM da altre risorse della rete virtuale, incluse quelle con indirizzi IP pubblici, ad esempio i servizi di bilanciamento del carico o le macchine virtuali autonome.

Due semplici modi per connettersi alle VM di un set di scalabilità sono descritti di seguito:

### Connettersi alle VM usando regole NAT

È possibile creare un indirizzo IP pubblico, assegnarlo a un servizio di bilanciamento del carico e definire le regole NAT in ingresso che eseguono il mapping di una porta nell'indirizzo IP a una porta su una VM del set di scalabilità. Ad esempio,

IP pubblico->Porta 50000 -> vmss\_0->Porta 22 IP pubblico->Porta 50001 -> vmss\_1->Porta 22 IP pubblico->Porta 50002-> vmss\_2->Porta 22

Ecco un esempio di creazione di un set di scalabilità VM che usa le regole NAT per abilitare la connessione SSH a ogni VM in un set di scalabilità usando un solo IP pubblico: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

Ecco un esempio della stessa operazione con RDP e Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

### Connettersi alle VM usando un "jumpbox"

Se si crea un set di scalabilità VM e una VM autonoma nella stessa rete virtuale, la VM autonoma e le VM del set di scalabilità possono connettersi l'una all'altra usando gli indirizzi IP interni definiti dalla rete virtuale/subnet.

Se si crea un indirizzo IP pubblico e lo si assegna alla VM autonoma, è possibile connettersi con RDP o SSH alla VM autonoma e quindi connettersi da tale computer alle istanze del set di scalabilità VM. A questo punto si potrebbe notare che un semplice set di scalabilità VM è intrinsecamente più sicuro di una semplice VM autonoma con un indirizzo IP pubblico nella configurazione predefinita.

Come esempio di questo approccio, questo modello crea un semplice cluster Mesos costituito da una VM master autonoma che gestisce un cluster di VM basato sul set di scalabilità VM: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

## Bilanciamento del carico con round robin per le istanze del set di scalabilità VM

Per trasmettere dati a un cluster di elaborazione di VM con un approccio "round-robin", è possibile configurare un servizio di bilanciamento del carico di Azure con le relative regole di bilanciamento del carico. È anche possibile definire alcuni probe per verificare che l'applicazione sia in esecuzione effettuando il ping delle porte con un protocollo, un intervallo e un percorso di richiesta specificati.

Ecco un esempio che crea un set di scalabilità di VM che eseguono il server Web IIS e usa un servizio di bilanciamento del carico per bilanciare il carico ricevuto da ogni VM. Usa anche il protocollo HTTP per effettuare il ping di un URL specifico su ogni VM: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json). Esaminare il tipo di risorsa Microsoft.Network/loadBalancers e networkProfile ed extensionProfile in virtualMachineScaleSet.

## Istanze del set di scalabilità VM con la scalabilità automatica di Azure

Per variare il conteggio delle istanze (_capacity_) del set di scalabilità VM a seconda dell'utilizzo di CPU/memoria/disco o di altri eventi, è disponibile un'ampia gamma di impostazioni di scalabilità automatica nel provider di risorse Microsoft.Insights. Per informazioni sulle impostazioni disponibili, vedere la documentazione su REST di Insights: [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx).

La scalabilità automatica di Insights si integra direttamente con i set di scalabilità VM. Per configurarla, si definisce un tipo di risorsa Microsoft.Insights/autoscaleSettings con i valori _targetResourceUri_ e _metricResourceUri_ che fanno riferimento al set di scalabilità. Quando si definisce il set di scalabilità, si include un valore _extensionProfile_ che installa l'agente Diagnostica di Azure (WAD) in Windows o l'estensione di diagnostica Linux in Linux. Ecco un esempio per Linux che aggiunge le VM fino a un limite predefinito quando l'utilizzo medio della CPU è >50% con una granularità temporale di 1 minuto rispetto a un periodo di campionamento di 5 minuti:

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale).

In futuro la scalabilità automatica con i set di scalabilità VM verrà integrata anche direttamente nel portale di Azure.

## Distribuzione di un set di scalabilità VM come cluster di elaborazione in un gestore di cluster PaaS

I set di scalabilità VM a volte vengono definiti come il ruolo di lavoro di prossima generazione. È una definizione valida che però rischia di confondere le funzionalità dei set di scalabilità con quelle del ruolo di lavoro PaaS v1.

In un certo senso i set di scalabilità VM forniscono un vero "ruolo di lavoro" o risorsa di lavoro. Infatti forniscono una risorsa di calcolo generalizzata indipendente dalla piattaforma o dal runtime, personalizzabile e integrata nel servizio IaaS di Gestione risorse di Azure.

Un ruolo di lavoro PaaS v1, anche se limitato in termini di supporto per la piattaforma o il runtime (solo immagini della piattaforma Windows), include anche servizi come lo scambio di indirizzi VIP, impostazioni di aggiornamento configurabili, impostazioni specifiche della distribuzione del runtime o delle app che non sono _ancora_ disponibili nei set di scalabilità VM o che verranno forniti da altri servizi PaaS di livello più elevato come l'infrastruttura di servizi. Tenendo questo presente, è possibile considerare i set di scalabilità VM come un'infrastruttura che supporta PaaS, ovvero le soluzioni PaaS, come l'infrastruttura di servizi o i gestori cluster come Mesos, possono basarsi sui set di scalabilità VM come livello di calcolo scalabile.

Ecco un esempio di cluster Mesos che distribuisce un set di scalabilità VM nella stessa rete virtuale di una VM autonoma. La VM autonoma è un master Mesos e il set di scalabilità VM rappresenta un set di nodi slave: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Le versioni future del [servizio contenitore di Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) distribuiranno versioni più complesse/avanzate di questo scenario basato sui set di scalabilità VM.

## Aumento e riduzione di un set di scalabilità VM

Per aumentare o ridurre il numero di macchine virtuali in un set di scalabilità VM, è sufficiente modificare la proprietà _capacity_ e ridistribuire il modello. Questa semplicità consente di scrivere facilmente il proprio livello di scalabilità personalizzato per definire gli eventi di scalabilità personalizzati non supportati dalla scalabilità automatica di Azure.

Se si sta ridistribuendo un modello per modificare la capacità, è possibile definire un modello molto più piccolo che include solo lo SKU e la capacità aggiornata. Un esempio è disponibile all'indirizzo [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

## Prestazioni dei set di scalabilità VM e informazioni sulla scalabilità

- Durante l'anteprima pubblica, non creare più di 500 VM per volta in più set di scalabilità VM. Questo limite aumenterà in futuro.
- Non pianificare più di 40 VM per account di archiviazione.
- Diversificare il più possibile le prime lettere dei nomi degli account di archiviazione. I modelli di set di scalabilità VM di esempio nei [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/) offrono esempi di come procedere.
- Se si usano VM personalizzate, non pianificare più di 40 VM per set di scalabilità, in un singolo account di archiviazione. Sarà necessaria l'immagine copiata in precedenza nell'account di archiviazione prima di poter iniziare a distribuire i set di scalabilità VM. Per altre informazioni, vedere le domande frequenti.
- Non pianificare più di 2048 VM per rete virtuale. Questo limite aumenterà in futuro.
- Il numero di VM che è possibile creare è limitato dalla quota core di calcolo in ogni area. Potrebbe essere necessario contattare l'assistenza clienti per innalzare il limite di quota di calcolo aumentato anche se attualmente si ha un limite elevato di core da usare con i servizi cloud o IaaS v1. Per eseguire una query della quota, è possibile eseguire il comando dell'interfaccia della riga di comando di Azure seguente: _azure vm list-usage_ e il comando di PowerShell seguente: _Get-AzureRmVMUsage_ (se si usa una versione di PowerShell precedente alla 1.0, eseguire _Get-AzureVMUsage_).


## Domande frequenti sui set di scalabilità VM

**D. Quante VM possono esistere in un set di scalabilità?**

R. 100 se si usano immagini della piattaforma che è possibile distribuire in più account di archiviazione. Se si usano immagini personalizzate, fino a 40, perché le immagini personalizzate sono limitate a un solo account di archiviazione durante l'anteprima.

**Quali altri limiti di risorse esistono per i set di scalabilità VM?**

R. Vengono applicati i limiti dei servizi di Azure esistenti: [https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)

Esiste anche il limite della creazione di non più di 500 VM in più set di scalabilità per area durante il periodo di anteprima.

**D. I dischi dati sono supportati nei set di scalabilità VM?**

R. Non nella versione iniziale. Le opzioni per l'archiviazione dei dati sono:

- File di Azure (unità condivise SMB)

- Unità del sistema operativo

- Unità Temp (locale, non supportata dal servizio di archiviazione di Azure)

- Servizi dati esterni (ad esempio, database remoto, tabelle di Azure, BLOB di Azure)

**D. Quali aree di Azure supportano i set di scalabilità VM?**

R. Tutte le aree che supportano Gestione risorse di Azure supportano i set di scalabilità VM.

**D. Come si crea un set di scalabilità VM con un'immagine personalizzata?**

R. Lasciare vuota la proprietà vhdContainers, ad esempio:

````
"storageProfile": {
   "osDisk": {
      "name": "vmssosdisk",
      "caching": "ReadOnly",
      "createOption": "FromImage",
      "image": {
         "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
     },
     "osType": "Windows"
  }
},
````

**D. Se si riduce la capacità dei set di scalabilità VM da 20 a 15, quali VM verranno rimosse?**

R. Verranno rimosse le ultime 5 VM (indici più grandi).

**D. Cosa accade se poi si aumenta la capacità da 15 a 18?**

Se si passa a 18, verranno create VM con indice 15, 16, 17. In entrambi i casi le VM vengono bilanciate tra domini di aggiornamento e domini di errore.

**D. Quando si usano più estensioni in un set di scalabilità VM, è possibile imporre una sequenza di esecuzione?**

R. Non direttamente, ma, per l'estensione customScript, lo script potrebbe attendere il completamento di un'altra estensione, ad esempio monitorando il log di estensione. Vedere [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh).

**D. I set di scalabilità VM si integrano con i set di disponibilità di Azure?**

R. Sì. Un set di scalabilità è un set di disponibilità implicito impostato con 3 domini di errore e 5 domini di aggiornamento. Non è necessario configurare nulla in virtualMachineProfile. Nelle versioni future i set di scalabilità si estenderanno probabilmente su più tenant, ma per ora un set di scalabilità è un set di disponibilità singolo.

## Passaggi successivi

Durante l'anteprima per i set di scalabilità VM, la documentazione verrà aggiornata e verranno rese disponibili altre funzionalità di integrazione come il portale e la scalabilità automatica.

Il feedback degli utenti è molto importante perché aiuta a creare un servizio che offre le funzionalità necessarie. Inviare commenti su cosa funziona, cosa non funziona e cosa potrebbe essere migliorato. È possibile inviare commenti e suggerimenti all'indirizzo [vmssfeedback@microsoft.com](mailto:vmssfeedback@microsoft.com).

## Risorse

| **Argomento** | **Collegamento** |
| --- | --- |
| Panoramica di Gestione risorse di Microsoft Azure | [https://azure.microsoft.com/documentation/articles/resource-group-overview/](https://azure.microsoft.com/documentation/articles/resource-group-overview/) |
| Modelli di Gestione risorse di Azure | [https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) |
| Funzioni del modello di Gestione risorse di Azure | [https://azure.microsoft.com/documentation/articles/resource-group-template-functions/](https://azure.microsoft.com/documentation/articles/resource-group-template-functions/) |
| Modelli di esempio (github) | [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) |
| Guida alle API REST dei set di scalabilità VM | [https://msdn.microsoft.com/library/mt589023.aspx](https://msdn.microsoft.com/library/mt589023.aspx) |
| Video sui set di scalabilità VM | [https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) [https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player) |
| Impostazioni di scalabilità automatica (MSDN) | [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx) |
| Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure | [https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/](https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/) |

<!---HONumber=Nov15_HO3-->