<properties
  pageTitle="Creare un cluster di MongoDB su Ubuntu | Microsoft Azure"
  description="Creare un cluster MongoDB in Ubuntu utilizzando un modello di Gestione risorse di Azure tramite Azure PowerShell o l’interfaccia della riga di comando di Azure"
  services="virtual-machines"
  documentationCenter=""
  authors="scoriani"
  manager="timlt"
  editor="tysonn"
  tags="azure-resource-manager"/>

<tags
  ms.service="virtual-machines"
  ms.workload="multiple"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="04/29/2015"
  ms.author="scoriani"/>

# Creare un cluster MongoDB in Ubuntu utilizzando un modello di Gestione risorse di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


MongoDB è un database di documenti open-source che fornisce alte prestazioni, elevata disponibilità e proporzioni automatiche. MongoDB può essere installato come database autonomo o in un cluster, sfruttando le funzionalità di replica incorporate. In alcuni casi, è possibile utilizzare la replica per aumentare la capacità di lettura. I client hanno la capacità di inviare operazioni di lettura e scrittura a server diversi. È inoltre possibile mantenere copie in data center diversi per aumentare la presenza e la disponibilità dei dati per le applicazioni distribuite. Con MongoDB, la replica, inoltre, fornisce ridondanza e aumenta la disponibilità dei dati. Con più copie dei dati nei diversi server di database, la replica consente di proteggere un database dalla perdita di un singolo server. La replica consente, inoltre, il ripristino da interruzioni di servizio ed errori hardware. Grazie alla disponibilità di copie aggiuntive dei dati, è possibile dedicarne una al ripristino di emergenza, alla creazione di report o al backup.

Oltre alle funzionalità già disponibili in Azure Marketplace, ora è possibile distribuire facilmente un nuovo cluster MongoDB nelle macchine virtuali Ubuntu utilizzando un modello di Gestione risorse di Azure distribuito tramite [Azure PowerShell](../powershell-install-configure.md) o l’[interfaccia della riga di comando di Azure](../xplat-cli-install.md).

La topologia dei cluster appena distribuiti basati su questo modello è descritta nel diagramma seguente, sebbene sia possibile ottenere facilmente altre topologie personalizzando il modello presentato in questo articolo:

![cluster-architecture](media/virtual-machines-mongodb-template/cluster-architecture.png)

Tramite un parametro è possibile definire il numero di nodi che verranno distribuiti nel nuovo cluster MongoDB e, in base a un altro parametro, un'istanza di macchina virtuale (Jumpbox) con un indirizzo IP pubblico può inoltre essere distribuita all'interno della stessa rete virtuale, fornendo la possibilità di connettersi al cluster dalla rete Internet pubblica e di eseguire qualsiasi tipo di attività amministrativa correlata a tale cluster. Un'altra opzione disponibile come parametro consiste nella possibilità di aggiungere un nodo Arbiter al set di repliche, soluzione consigliabile quando il set dispone di un numero di membri pari. Per ulteriori informazioni sulle topologie di replica di MongoDB e per i dettagli, fare riferimento alla [documentazione MongoDB](http://docs.mongodb.org/manual/core/replication-introduction/) ufficiale.

Una volta completata la distribuzione, è possibile accedere a Jumpbox utilizzando l'indirizzo DNS configurato sulla porta SSH 22.

Prima di addentrarsi in ulteriori dettagli relativi a Gestione risorse di Azure e al modello utilizzato per la distribuzione, assicurarsi che Azure PowerShell o l’interfaccia della riga di comando di Azure sia configurata correttamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Creare un cluster MongoDB con un modello di Gestione risorse

Attenersi alla seguente procedura per creare un cluster MongoDB utilizzando un modello di Gestione risorse dall'archivio dei modelli Github. Ogni passaggio includerà le indicazioni sia per Azure PowerShell che per l’interfaccia della riga di comando di Azure.

### Passaggio 1-a: Scaricare i file di modello tramite PowerShell

Creare una cartella locale per il modello JSON e gli altri file associati (ad esempio, C:\\Azure\\Templates\\MongoDB).

Nell’esempio seguente sostituire il nome cartella della cartella locale ed eseguire questi comandi:

    $folderName="C:\Azure\Templates\MongoDB"
    $webclient = New-Object System.Net.WebClient
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy.json"
    $filePath = $folderName + "\azuredeploy.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy-parameters.json"
    $filePath = $folderName + "\azuredeploy-parameters.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/arbiter-resources.json"
    $filePath = $folderName + "\arbiter-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/empty-resources.json"
    $filePath = $folderName + "\empty-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/jumpbox-resources.json"
    $filePath = $folderName + "\jumpbox-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D1.json"
    $filePath = $folderName + "\member-resources-D1.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D2.json"
    $filePath = $folderName + "\member-resources-D2.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D3.json"
    $filePath = $folderName + "\member-resources-D3.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D4.json"
    $filePath = $folderName + "\member-resources-D4.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D11.json"
    $filePath = $folderName + "\member-resources-D11.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D12.json"
    $filePath = $folderName + "\member-resources-D12.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D13.json"
    $filePath = $folderName + "\member-resources-D13.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D14.json"
    $filePath = $folderName + "\member-resources-D14.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/mongodb-ubuntu-install.sh"
    $filePath = $folderName + "\mongodb-ubuntu-install.sh"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/metadata.json"
    $filePath = $folderName + "\metadata.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/shared-resources.json"
    $filePath = $folderName + "\shared-resources.json"
    $webclient.DownloadFile($url,$filePath)  

### Passaggio 1-b: Scaricare i file di modello tramite l'interfaccia della riga di comando di Azure

Nell’esempio riportato di seguito viene illustrato come clonare l’intero repository dei modelli utilizzando un client Git di propria scelta.

    git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Una volta completata l’operazione, cercare la cartella mongodb-high-availability nella directory C:\\Azure\\Templates.

### Passaggio 2: (facoltativo) Comprendere i parametri del modello

Quando si distribuiscono soluzioni non semplici come un cluster MongoDB, è necessario specificare un set di parametri di configurazione per gestire un numero di impostazioni necessarie. Dichiarando i parametri nella definizione del modello, è possibile specificare i valori durante la distribuzione tramite un file esterno o nella riga di comando.

Nella sezione dei parametri nella parte superiore del file azuredeploy.json si individuerà il set di parametri richiesti dal modello per configurare un cluster MongoDB. Nell’esempio di seguito è illustrata la sezione dei parametri dal file azuredeploy.json di questo modello.

    "parameters": {
      "adminUsername": {
          "type": "string",
          "metadata": {
            "Description": "Administrator user name used when provisioning virtual machines (which also becomes a system user administrator in MongoDB)"
          }
        },
        "adminPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Administrator password used when provisioning virtual machines (which is also a password for the system administrator in MongoDB)"
          }
        },
        "storageAccountName": {
          "type": "string",
          "defaultValue": "",
          "metadata": {
            "Description": "Unique namespace for the storage account where the Virtual Machine's disks will be placed (this name will be used as a prefix to create one or more storage accounts as per t-shirt size)"
          }
        },
        "region": {
          "type": "string",
          "metadata": {
            "Description": "Location where resources will be provisioned"
          }
        },
        "virtualNetworkName": {
          "type": "string",
          "defaultValue": "mongodbVnet",
          "metadata": {
            "Description": "The arbitrary name of the virtual network provisioned for the MongoDB deployment"
          }
        },
        "subnetName": {
          "type": "string",
          "defaultValue": "mongodbSubnet",
          "metadata": {
            "Description": "Subnet name for the virtual network that resources will be provisioned in to"
          }
        },
        "addressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/16",
          "metadata": {
            "Description": "The network address space for the virtual network"
          }
        },
        "subnetPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/24",
          "metadata": {
            "Description": "The network address space for the virtual subnet"
          }
        },
        "nodeAddressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.1",
          "metadata": {
            "Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
          }
        },
        "jumpbox": {
          "type": "string",
          "defaultValue": "Disabled",
          "allowedValues": [
          "Enabled",
          "Disabled"
          ],
          "metadata": {
            "Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the MongoDB environment"
          }
        },
        "tshirtSize": {
          "type": "string",
          "defaultValue": "XSmall",
          "allowedValues": [
          "XSmall",
          "Small",
          "Medium",
          "Large",
          "XLarge",
          "XXLarge"
          ],
          "metadata": {
            "Description": "T-shirt size of the MongoDB deployment"
          }
        },
        "osFamily": {
          "type": "string",
          "defaultValue": "Ubuntu",
          "allowedValues": [
          "Ubuntu"
          ],
          "metadata": {
            "Description": "The target OS for the virtual machines running MongoDB"
          }
        },
        "mongodbVersion": {
          "type": "string",
          "defaultValue": "3.0.2",
          "metadata": {
            "Description": "The version of the MongoDB packages to be deployed"
          }
        },
        "replicaSetName": {
          "type": "string",
          "defaultValue": "rs0",
          "metadata": {
            "Description": "The name of the MongoDB replica set"
          }
        },
        "replicaSetKey": {
          "type": "string",
          "metadata": {
            "Description": "The shared secret key for the MongoDB replica set"
          }
        }
      },

Ogni parametro include dettagli quali il tipo di dati e i valori consentiti. Ciò consente la convalida dei parametri passati durante l'esecuzione del modello in modalità interattiva (ad esempio, PowerShell o l’interfaccia della riga di comando di Azure) e un'interfaccia utente di individuazione automatica che può essere compilata dinamicamente analizzando l'elenco dei parametri richiesti e le relative descrizioni.

### Passaggio 3-a: Distribuzione di un cluster MongoDB con un modello tramite PowerShell

Preparare un file dei parametri per la distribuzione creando un file JSON con i valori di runtime per tutti i parametri. Questo file verrà quindi passato al comando di distribuzione come singola entità. Se non si include un file dei parametri, in PowerShell verranno utilizzati tutti i valori predefiniti specificati nel modello, quindi verrà richiesto di inserire i valori rimanenti.

Nell’esempio di seguito viene illustrato un set di parametri dal file azuredeploy-parameters.json.

    {
      "adminUsername": {
          "value": "MongoAdmin"
      },
      "adminPassword": {
          "value": ""
      },
      "storageAccountName": {
          "value": ""
      },
      "region": {
          "value": "West US"
      },
      "virtualNetworkName": {
          "value": "mongodbVnet"
      },
      "subnetName": {
          "value": "mongodbSubnet"
      },
      "addressPrefix": {
          "value": "10.0.0.0/16"
      },
      "subnetPrefix": {
          "value": "10.0.0.0/24"
      },
      "nodeAddressPrefix": {
          "value": "10.0.0.1"
      },
      "jumpbox": {
          "value": "Disabled"
      },
      "tshirtSize": {
          "value": "XSmall"
      },
      "osFamily": {
          "value": "Ubuntu"
      },
      "mongodbVersion": {
          "value": "3.0.2"
      },
      "replicaSetName": {
          "value": "rs0"
      },
      "replicaSetKey":  {
          "value": ""
      }
    }

Inserire un nome per la distribuzione Azure, un nome per il gruppo di risorse, il percorso di Azure e la cartella in cui è stato salvato il file di distribuzione JSON. Quindi, eseguire i comandi seguenti:

    $deployName="<deployment name>"
    $RGName="<resource group name>"
    $locName="<Azure location, such as West US>"
    $folderName="<folder name, such as C:\Azure\Templates\MongoDB>"
    $templateFile= $folderName + "\azuredeploy.json"
    $templateParameterFile= $folderName + "\azuredeploy-parameters.json"

    New-AzureResourceGroup –Name $RGName –Location $locName

    New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Quando si esegue il comando **New-AzureResourceGroupDeployment**, verranno estratti i valori dei parametri dal file dei parametri JSON e verrà avviata l'esecuzione del modello di conseguenza. La definizione e l'uso di più file di parametri con ambienti diversi (ad esempio, test, produzione e così via) promuoveranno il riutilizzo e la semplificazione di soluzioni con più ambienti complesse.

Quando si effettua la distribuzione, tenere presente che è necessario creare un nuovo account di archiviazione di Azure, in modo che il nome fornito come parametro di account di archiviazione sia univoco e soddisfi tutti i requisiti di un account di archiviazione di Azure (solo lettere minuscole e numeri).

Durante e dopo la distribuzione, è possibile controllare tutte le richieste effettuate durante il provisioning, compresi gli errori che si sono verificati.

A tale scopo, visitare il [portale di Azure](https://portal.azure.com) ed effettuare le seguenti operazioni:

- Fare clic su **Sfoglia** nella barra di spostamento a sinistra, scorrere verso il basso e fare clic su **Gruppi di risorse**.
- Dopo aver fatto clic sul gruppo di risorse appena creato, viene visualizzato il pannello Gruppo di risorse.
- Facendo clic sul grafico a barre "Eventi" nella parte "Monitoraggio" del pannello Gruppo di risorse, è possibile visualizzare gli eventi per la distribuzione. Facendo clic sui singoli eventi è possibile esaminare i dettagli di ogni singola operazione eseguita per conto del modello.

Dopo aver effettuato i test, per rimuovere il gruppo di risorse e tutte le relative risorse (l'account di archiviazione, la macchina virtuali e la rete virtuale), utilizzare questo comando:

    Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Passaggio 3-a: Distribuzione di un cluster MongoDB con un modello tramite l’interfaccia della riga di comando di Azure

Per distribuire un cluster MongoDB tramite l’interfaccia della riga di comando di Azure, è necessario prima creare un gruppo di risorse specificando un nome e un percorso con il seguente comando:

    azure group create mdbc "West US"

Passare il nome di questo gruppo di risorse, il percorso del file di modello JSON e il percorso del file dei parametri (vedere la sezione precedente di PowerShell per i dettagli) nel comando seguente.

    azure group deployment create mdbc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

È possibile controllare lo stato delle distribuzioni delle singole risorse con il comando seguente:

    azure group deployment list mdbc

## Panoramica della struttura del modello MongoDB e dell’organizzazione dei file

Per progettare un modello di Gestione risorse di Azure efficace e riutilizzabile, è necessaria un’ulteriore valutazione per organizzare la serie di attività complesse e correlate necessarie durante la distribuzione di una soluzione complessa come MongoDB. Sfruttando le funzionalità di *collegamento dei modelli* e *ciclo delle risorse* di Gestione risorse di Azure, oltre all'esecuzione di script tramite le estensioni correlate, è possibile implementare un approccio modulare che in teoria può essere riutilizzato con qualsiasi distribuzione complessa basata sui modelli.

In questo diagramma sono illustrate le relazioni tra tutti i file scaricati da GitHub per questa distribuzione.

![mongodb-files](media/virtual-machines-mongodb-template/mongodb-files.png)

In questa sezione viene fornita una descrizione della struttura del file azuredeploy.json per il cluster MongoDB.

### Sezione dei parametri

Nella sezione dei parametri di azuredeploy.json vengono specificati i parametri modificabili utilizzati in questo modello. Il file azuredeploy-parameters.json descritto in precedenza in questo articolo viene utilizzato per passare i valori nella sezione dei parametri di azuredeploy.json durante l'esecuzione del modello.

### Sezione delle variabili

Nella sezione delle variabili vengono specificate le variabili che possono essere usate in questo modello. Contiene un numero di campi (tipi di dati o frammenti JSON) che verranno impostati su costanti o valori calcolati in fase di esecuzione. Nell'esempio seguente viene illustrata la sezione delle variabili per il modello MongoDB.

    "variables": {
          "_comment0": "/* T-shirt sizes may vary for different reasons, and some customers may want to modify these - so feel free to go ahead and define your favorite t-shirts */",
          "tshirtSizeXSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 1,
              "totalMemberCount": 2,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 2,
              "totalMemberCount": 3,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeMedium": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 3,
              "totalMemberCount": 4,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 2,
              "dataDiskSize": 250
          },
          "tshirtSizeLarge": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 250
          },
          "tshirtSizeXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 500
          },
          "tshirtSizeXXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 15,
              "totalMemberCount": 16,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 8,
              "dataDiskSize": 500
          },
          "osFamilyUbuntu": {
              "osName": "ubuntu",
              "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
              "installerPackages": "mongodb-org",
              "imagePublisher": "Canonical",
              "imageOffer": "UbuntuServer",
              "imageSKU": "14.04.2-LTS"
          },
          "vmStorageAccountContainerName": "vhd-mongodb",
          "vmStorageAccountDomain": ".blob.core.windows.net",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "sharedScriptUrl": "[concat('https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/', variables('osFamilySpec').osName, '/')]",
          "scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-high-availability/",
          "templateBaseUrl": "[variables('scriptUrl')]",
          "jumpboxTemplateEnabled": "jumpbox-resources.json",
          "jumpboxTemplateDisabled": "empty-resources.json",
          "arbiterTemplateEnabled": "arbiter-resources.json",
          "arbiterTemplateDisabled": "empty-resources.json",
          "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
          "jumpboxTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('jumpboxTemplate', parameters('jumpbox'))))]",
          "arbiterTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('arbiterTemplate', variables('clusterSpec').arbiter)))]",
          "commonSettings": {
              "availabilitySetName": "mongodbAvailSet",
              "region": "[parameters('region')]"
          },
          "storageSettings": {
              "vhdStorageAccountName": "[parameters('storageAccountName')]",
              "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
              "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]",
              "storageAccountCount": "[variables('clusterSpec').storageAccountCount]"
          },
          "networkSettings": {
              "virtualNetworkName": "[parameters('virtualNetworkName')]",
              "addressPrefix": "[parameters('addressPrefix')]",
              "subnetName": "[parameters('subnetName')]",
              "subnetPrefix": "[parameters('subnetPrefix')]",
              "subnetRef": "[concat(variables('vnetID'), '/subnets/', parameters('subnetName'))]",
              "machineIpPrefix": "[parameters('nodeAddressPrefix')]"
          },
          "machineSettings": {
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]",
              "machineNamePrefix": "mongodb-",
              "osImageReference": {
                  "publisher": "[variables('osFamilySpec').imagePublisher]",
                  "offer": "[variables('osFamilySpec').imageOffer]",
                  "sku": "[variables('osFamilySpec').imageSKU]",
                  "version": "latest"
              }
          },
          "clusterSpec": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
          "osFamilySpec": "[variables(concat('osFamily', parameters('osFamily')))]",
          "installCommand": "[concat('bash mongodb-', variables('osFamilySpec').osName, '-install.sh', ' -i ', variables('osFamilySpec').installerBaseUrl, ' -b ', variables('osFamilySpec').installerPackages, ' -r ', parameters('replicaSetName'), ' -k ', parameters('replicaSetKey'), ' -u ', parameters('adminUsername'), ' -p ', parameters('adminPassword'), ' -x ', variables('networkSettings').machineIpPrefix, ' -n ', variables('clusterSpec').totalMemberCount)]",
          "vmScripts": {
              "scriptsToDownload": [
                  "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
                  "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
              ],
              "regularNodeInstallCommand": "[variables('installCommand')]",
              "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
              "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
          },
          "_comment1": "/* The list of values below helps partition VM disks across multiple storage accounts to achieve a better throughput */",
          "_comment2": "/* Feel free to modify the default allocations if you are comfortable and understand what you are doing */",
          "storageAccountForXSmall_0": "0",
          "storageAccountForXSmall_1": "0",
          "storageAccountForSmall_0": "0",
          "storageAccountForSmall_1": "0",
          "storageAccountForSmall_2": "0",
          "storageAccountForMedium_0": "0",
          "storageAccountForMedium_1": "0",
          "storageAccountForMedium_2": "0",
          "storageAccountForMedium_3": "0",
          "storageAccountForLarge_0": "0",
          "storageAccountForLarge_1": "1",
          "storageAccountForLarge_2": "2",
          "storageAccountForLarge_3": "3",
          "storageAccountForLarge_4": "0",
          "storageAccountForLarge_5": "1",
          "storageAccountForLarge_6": "2",
          "storageAccountForLarge_7": "3",
          "storageAccountForXLarge_0": "0",
          "storageAccountForXLarge_1": "1",
          "storageAccountForXLarge_2": "2",
          "storageAccountForXLarge_3": "3",
          "storageAccountForXLarge_4": "0",
          "storageAccountForXLarge_5": "1",
          "storageAccountForXLarge_6": "2",
          "storageAccountForXLarge_7": "3",
          "storageAccountForXXLarge_0": "0",
          "storageAccountForXXLarge_1": "1",
          "storageAccountForXXLarge_2": "2",
          "storageAccountForXXLarge_3": "3",
          "storageAccountForXXLarge_4": "4",
          "storageAccountForXXLarge_5": "5",
          "storageAccountForXXLarge_6": "6",
          "storageAccountForXXLarge_7": "7",
          "storageAccountForXXLarge_8": "0",
          "storageAccountForXXLarge_9": "1",
          "storageAccountForXXLarge_10": "2",
          "storageAccountForXXLarge_11": "3",
          "storageAccountForXXLarge_12": "4",
          "storageAccountForXXLarge_13": "5",
          "storageAccountForXXLarge_14": "6",
          "storageAccountForXXLarge_15": "7"
      },

Nell'esempio precedente, è possibile visualizzare due diversi approcci. In questo primo frammento la variabile "osFamilyUbuntu" verrà impostata su un elemento JSON contenente 6 coppie chiave-valore.

    "osFamilyUbuntu": {
      "osName": "ubuntu",
      "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
      "installerPackages": "mongodb-org",
      "imagePublisher": "Canonical",
      "imageOffer": "UbuntuServer",
      "imageSKU": "14.04.2-LTS"
    },

In questo secondo frammento, la variabile "vmScripts" è assegnata a una matrice JSON in cui i singoli elementi verranno calcolati in fase di esecuzione utilizzando una funzione del linguaggio del modello (concat) e il valore di un'altra variabile, più le costanti di stringa:

    "vmScripts": {
      "scriptsToDownload": [
      "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
      "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],

Un concetto importante in questo modello è il modo diverso in cui vengono definite le "taglie" per i cluster MongoDB. Esaminando una delle variabili "tshirtSizeXXXX", è possibile notare che vengono descritte le caratteristiche importanti del modo in cui un cluster viene distribuito. Nell’esempio seguente viene preso come esempio la taglia media:

    "tshirtSizeMedium": {
      "vmSizeMember": "Standard_D2",
      "vmSizeArbiter": "Standard_A1",
      "numberOfMembers": 3,
      "totalMemberCount": 4,
      "arbiter": "Enabled",
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
      "storageAccountCount": 2,
      "dataDiskSize": 250
    },

Un cluster MongoDB "Medio" utilizzerà D2 come dimensione della macchina virtuale per i tre nodi MongoDB che ospitano i dati più una quarta macchina virtuale A1 che verrà utilizzata come Arbiter con finalità di replica. Il modello secondario corrispondente richiamato per distribuire i nodi di dati sarà `member-resources-D2.json` e i file di dati (250 GB ciascuno) verranno archiviati in due account di archiviazione. Queste variabili saranno utilizzate nella sezione delle risorse per orchestrare le distribuzioni di nodi e altre attività.

### Sezione risorse

La sezione delle risorse è il punto in cui si svolge la maggior parte delle operazioni. Analizzando attentamente questa sezione, è possibile identificare immediatamente due diversi casi: il primo è un elemento definito di tipo `Microsoft.Resources/deployments` essenzialmente ciò implica la chiamata di una distribuzione nidificata all'interno di quella principale. Tramite l'elemento "templateLink" (e la proprietà della versione correlata), è possibile specificare un file di modello collegato che verrà richiamato passando un set di parametri come input, come è possibile notare nel seguente esempio:

    {
      "name": "shared-resources",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2015-01-01",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('sharedTemplateUrl')]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "commonSettings": {
            "value": "[variables('commonSettings')]"
          },
          "storageSettings": {
            "value": "[variables('storageSettings')]"
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          }
        }
      }
    },

Dall’esempio precedente è chiaro come il file azuredeploy.json in questo scenario sia stato organizzato come meccanismo di orchestrazione, che richiama un determinato numero di altri file di modello, ciascuno responsabile di parte delle attività di distribuzione necessarie.

In particolare, i seguenti modelli collegati verranno usati per la distribuzione:

-	**shared-resource.json**: contiene la definizione di tutte le risorse che verranno condivise durante la distribuzione. Esempi di account di archiviazione utilizzati per archiviare i dischi del sistema operativo della macchina virtuale e le reti virtuali.
-	**Jumpbox-resources.json**: quando abilitato, è responsabile della distribuzione di tutte le risorse correlate alla macchina virtuale Jumpbox, quella con un indirizzo IP pubblico che può essere utilizzato per accedere al cluster MongoDB dalla rete pubblica.
-	**Arbiter-resources.json**: quando abilitato, questo modello distribuisce un membro Arbiter nel cluster MongoDB. Un membro Arbiter non contiene dati, ma viene utilizzato quando un set di repliche contiene un numero pari di nodi per gestire le elezioni primarie.
-	**Member-resources-Dx.json**: specifica i modelli di risorse che distribuiscono in modo efficace i nodi MongoDB. Verrà utilizzato un file specifico in base alla taglia selezionata, in cui ogni file differirà unicamente per il numero di dischi collegati per ciascun nodo.
-	**Mongodb-ubuntu-install.sh**: file di script bash richiamato dall’estensione CustomScriptForLinux su ogni nodo del cluster. Responsabile del montaggio e della formattazione dei dischi di dati e dell'installazione dei bit MongoDB nel nodo.

Per distribuire un cluster MongoDB, è necessaria una logica specifica per essere in grado di configurare correttamente un set di repliche. Nell’esempio seguente è illustrata la sequenza specifica che è necessario utilizzare durante la distribuzione:

DEPLOY DATA MEMBERS (in parallel) => DEPLOY LAST DATA MEMBER => (optional) DEPLOY ARBITER

In questa sequenza, la distribuzione di più nodi di dati verrà eseguita in parallelo, fatta eccezione per l'ultimo nodo. È qui che verrà formato il cluster e sarà distribuito il nuovo set di repliche, pertanto tutti i nodi precedenti dovranno essere operativi prima di tale momento. L’ultimo passaggio consisterà nella distribuzione del nodo facoltativo Arbiter (solo per le dimensioni per le quali è richiesto).

Guardando nuovamente all'interno del nostro modello principale (azuredeploy.json), vediamo in che modo è implementata questa logica, a partire da tutti i membri dati, nell’esempio seguente.

    {
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('member-resources', copyindex())]",
      "apiVersion": "2015-01-01",
      "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
      ],
      "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').numberOfMembers]"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('clusterSpec').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "commonSettings": {
            "value": "[variables('commonSettings')]"
          },
          "storageSettings": {
            "value": {
              "vhdStorageAccountName": "[concat(variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())))]",
                              "vhdContainerName": "[variables('storageSettings').vhdContainerName]",
                              "destinationVhdsContainer": "[concat('https://', variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())), variables('vmStorageAccountDomain'), '/', variables('storageSettings').vhdContainerName, '/')]"
            }
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          },
          "machineSettings": {
            "value": {
              "adminUsername": "[variables('machineSettings').adminUsername]",
                              "adminPassword": "[variables('machineSettings').adminPassword]",
                              "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                              "osImageReference": "[variables('machineSettings').osImageReference]",
                              "vmSize": "[variables('clusterSpec').vmSizeMember]",
                              "dataDiskSize": "[variables('clusterSpec').dataDiskSize]",
                              "machineIndex": "[copyindex()]",
                              "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                              "commandToExecute": "[variables('vmScripts').regularNodeInstallCommand]"
            }
          }
        }
      }
    },

Un concetto importante da evidenziare è come sia possibile distribuire più copie di un singolo tipo di risorsa e per ogni istanza impostare valori univoci per le impostazioni richieste. Questo concetto è noto come *Ciclo delle risorse o Resource Looping*.

Nell’esempio precedente, un parametro (numero di nodi da distribuire nel cluster) verrà utilizzato per impostare una variabile ("numberOfMembers") che viene quindi passata all’elemento **"copia"** per attivare un numero (ciclo) di distribuzioni figlio, ciascuna delle quali comporterà la creazione di istanze del modello per ogni membro del cluster. Per essere in grado di definire tutte le impostazioni in cui sono necessari valori univoci tra le istanze, è possibile utilizzare la funzione **copyindex()** per ottenere un valore numerico che indica l’indice corrente nella creazione di quel particolare ciclo di risorse.

Un altro concetto importante nella creazione di risorse è la possibilità di specificare le dipendenze e le priorità tra risorse, come si noterà nella matriced JSON **dependsOn**. In questo particolare modello, la distribuzione di ciascun nodo dipende dalla precedente riuscita della distribuzione di **risorse condivise**.

I dischi collegati sono formattati nell’ambito delle attività di preparazione del nodo attivate dall'esecuzione del file di script mongodb-ubuntu-install.sh. All’interno di tale file, in effetti sarà possibile trovare un'istanza della seguente chiamata.

    bash ./vm-disk-utils-0.1.sh -b $DATA_DISKS -s

vm-disk-utils-0.1.sh fa parte della cartella shared\_scripts\\ubuntu all'interno dell'archivio github azure-quickstart-tempates e contiene funzioni molto utili per il montaggio, la formattazione e lo striping del disco, che possono essere riutilizzate ogni volta che è necessario eseguire attività simili nell’ambito della creazione del modello.

Un altro frammento interessante da esplorare, è quello correlato alle estensioni della macchina virtuale CustomScriptForLinux. Queste vengono installate come tipo di risorsa separato, con una dipendenza in ciascun modello di distribuzione del nodo del cluster. Ad esempio, vedere il frammento seguente alla fine di ogni file member-resources-Dx.json.

    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installmongodb')]",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('commonSettings').region]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
      ],
      "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
          "fileUris": "[parameters('machineSettings').vmScripts]", "commandToExecute":"[parameters('machineSettings').commandToExecute]"
        }
      }
    }

Acquisendo familiarità con gli altri file inclusi in questa distribuzione sarà possibile comprendere tutti i dettagli e le procedure consigliate necessarie per organizzare e orchestrare le strategie di distribuzione complesse per le soluzioni con più nodi, basate su qualsiasi tecnologia, che sfruttano i modelli di Gestione risorse di Azure. Sebbene non obbligatorio, un approccio consigliato è quello di strutturare i file di modello come mostrato nel diagramma seguente:

![mongodb-template-structure](media/virtual-machines-mongodb-template/mongodb-template-structure.png)

In pratica, questo approccio suggerisce che:

-	È necessario definire il file del modello di base come un punto centrale di orchestrazione per tutte le attività di distribuzione specifiche, sfruttando il modello di collegamento per richiamare le esecuzioni del sotto modello.
-	È necessario creare un file di modello specifici che consentiranno di distribuire tutte le risorse condivise tra tutte le altre attività di distribuzione specifiche (ad esempio gli account di archiviazione, configurazione di rete virtuale e così via). Questa funzione può essere riusata in modo intensivo tra distribuzioni che prevedono requisiti simili in termini di infrastruttura comune.
-	Includere modelli di risorsa facoltativi per requisiti specifici di una determinata risorsa.
-	Per i membri identici di un gruppo di risorse (nodi in un cluster e così via), creare modelli specifici che sfruttano il ciclo di risorse per poter distribuire più istanze con proprietà univoche.
-	Per tutte le attività di post-distribuzione (ad esempio, installazione del prodotto, configurazioni e così via) usare estensioni di distribuzione di script e creare script specifici per ogni tecnologia.

Per altre informazioni, vedere il [linguaggio del modello di Gestione risorse di Azure](../resource-group-authoring-templates.md).

<!---HONumber=Oct15_HO3-->