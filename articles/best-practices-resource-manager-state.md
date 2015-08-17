<properties
	pageTitle="Procedure consigliate per la gestione dello stato in modelli di Gestione risorse di Azure"
	description="Mostra gli approcci consigliati per usare oggetti complessi per condividere i dati sullo stato con i modelli di Gestione risorse di Azure e i modelli collegati"
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="mmercuri"/>

# Condivisione dello stato in modelli di Gestione risorse di Azure

Questo argomento descrive come gestire e condividere lo stato in un modello di Gestione risorse di Azure e nei modelli collegati.

## Uso di oggetti complessi per condividere lo stato

Oltre ai parametri a valore singolo, è possibile usare oggetti complessi come parametri in un modello di Gestione risorse di Azure. Con gli oggetti complessi, è possibile implementare e fare riferimento a raccolte di dati per un'area specifica, ad esempio la taglia (per descrivere una macchina virtuale), le impostazioni di rete, le impostazioni del sistema operativo e le impostazioni di disponibilità.

Il seguente esempio mostra come definire variabili contenenti oggetti complessi per rappresentare raccolte di dati. Le raccolte definiscono i valori usati per le dimensioni della macchina virtuale, le impostazioni di rete, le impostazioni del sistema operativo e le impostazioni di disponibilità.

    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "slaveCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    },
    "osSettings": {
      "scripts": [
        "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
      ],
      "imageReference": {
				"publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    },
    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    },
    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

È possibile fare riferimento a queste variabili in un secondo momento nel modello. La possibilità di fare riferimento a variabili denominate e alle relative proprietà semplifica la sintassi del modello e semplifica la comprensione del contesto. Il seguente esempio definisce una risorsa da distribuire usando gli oggetti indicati sopra per impostare i valori. Ad esempio, si noti che le dimensioni della VM vengono impostate recuperando il valore di `variables('tshirtSize').vmSize`, mentre il valore delle dimensioni del disco viene recuperato da `variables('tshirtSize').diskSize`. Inoltre, l'URI di un modello collegato viene impostato con il valore di `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
	    "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## Passaggio dello stato a un modello e ai modelli collegati

È possibile condividere le informazioni sullo stato in un modello e nei modelli collegati tramite:

- parametri forniti direttamente al modello principale durante la distribuzione
- parametri, variabili statiche e variabili generate che il modello principale condivide con i modelli collegati

### Parametri comuni forniti al modello principale

La seguente tabella elenca i parametri di uso comune nei modelli.

**Parametri di uso comune passati al modello principale**

Nome | Valore | Descrizione
---- | ----- | -----------
location | Stringa da un elenco vincolato di aree di Azure | Posizione in cui verranno distribuite le risorse.
storageAccountNamePrefix | Stringa | Nome DNS univoco dell'account di archiviazione in cui verranno inseriti i dischi della VM
domainName | Stringa | Nome di dominio della VM Jumpbox accessibile pubblicamente nel formato: **{domainName}.{location}.cloudapp.com**, ad esempio: **mydomainname.westus.cloudapp.azure.com**
adminUsername | Stringa | Nome utente per le VM
adminPassword | Stringa | Password delle VM
tshirtSize | Stringa da un elenco vincolato di taglie offerte | Dimensioni dell'unità di scala denominata di cui effettuare il provisioning. Ad esempio, "Small", "Medium", "Large"
virtualNetworkName | Stringa | Nome della rete virtuale che l'utente vuole usare.
enableJumpbox | Stringa da un elenco vincolato (abilitato/disabilitato) | Parametro che indica se abilitare Jumpbox per l'ambiente. Valori: "enabled", "disabled"

### Parametri inviati a modelli collegati

Quando ci si connette a modelli collegati, si userà spesso una combinazione di variabili statiche e generate.

#### Variabili statiche

Le variabili statiche vengono usate spesso per fornire valori di base, ad esempio URL, usati in un modello oppure valori usati per comporre valori per variabili dinamiche.

Nell'estratto di modello seguente, *templateBaseUrl* specifica il percorso radice del modello in GitHub. La riga successiva compila una nuova variabile *sharedTemplateUrl* che concatena il valore di *templateBaseUrl* con il nome noto del modello di risorse condiviso. Sotto, una variabile oggetto complesso viene usata per archiviare una taglia, dove *templateBaseUrl* viene concatenato per specificare il percorso del modello di configurazione noto archiviato nella proprietà *vmTemplate*.

Il vantaggio di questo approccio è di poter facilmente spostare, biforcare o usare il modello come base per uno nuovo. Se il percorso del modello cambia, è sufficiente modificare la variabile statica nell'unica posizione, ovvero il modello principale, che lo passa in tutti i modelli.

    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "slaveCount": 1,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    }

#### Variabili generate

Oltre alle variabili statiche, alcune variabili vengono generate dinamicamente. Questa sezione identifica alcuni dei tipi comuni di variabili generate.

##### tshirtSize

Quando si chiama il modello principale, è possibile selezionare una taglia da un numero fisso di opzioni, che di solito includono valori come *Small*, *Medium* e *Large*.

Nel modello principale, questa opzione appare come parametro, ad esempio *tshirtSize*:

    "tshirtSize": {
      "type": "string",
      "defaultValue": "Small",
      "allowedValues": [
        "Small",
        "Medium",
        "Large"
      ],
      "metadata": {
        "Description": "T-shirt size of the MongoDB deployment"
      }
    }

Nel modello principale, le variabili corrispondono a ognuna delle dimensioni. Ad esempio, se le dimensioni disponibili sono small, medium e large, la sezione delle variabili includerà le variabili denominate *tshirtSizeSmall*, *tshirtSizeMedium* e *tshirtSizeLarge*.

Come mostra l'esempio seguente, queste variabili definiscono le proprietà di una particolare taglia. Ognuna identifica il tipo di VM, le dimensioni del disco, il modello di risorse dell'unità di scala associato a cui creare il collegamento, il numero di istanze, i dettagli dell'account di archiviazione e lo stato Jumpbox.

Il prefisso del nome dell'account di archiviazione deriva da un parametro fornito da un utente e il modello collegato è la concatenazione dell'URL di base per il modello e del nome file di un modello di risorse dell'unità di scala specifico.

    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
			"diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    },
    "tshirtSizeMedium": {
      "vmSize": "Standard_A3",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1],
        "jumpbox": 0
      }
    },
    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    }

La variabile *tshirtSize* è visibile più in basso nella sezione delle variabili. La parte finale della taglia specificata (*Small*, *Medium*, *Large*) viene concatenata con il testo *tshirtSize* per recuperare la variabile oggetto complesso associata per tale taglia:

    "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",

Questa variabile viene passata al modello di risorse dell'unità di scala collegato.

##### networkSettings

In una capacità, funzionalità o modello di soluzione con ambito end-to-end, i modelli collegati di solito creano risorse esistenti in una rete. Un approccio semplice consiste nell'usare un oggetto complesso per archiviare le impostazioni di rete e passarle ai modelli collegati.

Di seguito è riportato un esempio di impostazioni di rete di comunicazione.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

##### availabilitySettings

Le risorse create nei modelli collegati vengono spesso inserite in un set di disponibilità. Nell'esempio seguente, vengono specificati il nome del set di disponibilità e anche il conteggio di domini di errore e di domini di aggiornamento da usare.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Se sono necessari più set di disponibilità (ad esempio, uno per i nodi master e un altro per i nodi dati), è possibile usare un nome come prefisso, specificare più set di disponibilità o seguire il modello mostrato prima per creare una variabile per una taglia specifica.

##### storageSettings

I dettagli di archiviazione spesso vengono condivisi con i modelli collegati. Nell'esempio seguente, un oggetto *storageSettings* fornisce i dettagli sull'account di archiviazione e sui nomi dei contenitori.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

##### osSettings

Con i modelli collegati, potrebbe essere necessario passare le impostazioni del sistema operativo a vari tipi di nodi tra tipi di configurazione noti diversi. Un oggetto complesso consente di archiviare e condividere facilmente le informazioni sul sistema operativo e di supportare più scelte del sistema operativo per la distribuzione.

Il seguente esempio mostra un oggetto per*osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

##### machineSettings

Una variabile generata, *machineSettings* è un oggetto complesso contenente una combinazione di variabili principali per la creazione di una nuova VM: nome utente e password dell'amministratore, un prefisso per i nomi delle VM e un riferimento a un'immagine del sistema operativo come mostrato sotto:

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

Si noti che *osImageReference* recupera i valori della variabile *osSettings* definita nel modello principale. Ciò significa che è possibile modificare facilmente il sistema operativo per una VM, interamente o in base alla preferenza di un utente del modello.

##### vmScripts

L'oggetto *vmScripts* contiene i dettagli sugli script per il download e l'esecuzione in un'istanza di una VM, inclusi i riferimenti esterni e interni. I riferimenti esterni includono l'infrastruttura. I riferimenti interni includono il software installato e la configurazione.

La proprietà *scriptsToDownload* viene usata per elencare gli script per il download nella VM.

Come mostra l'esempio seguente, questo oggetto contiene anche i riferimenti agli argomenti della riga di comando per diversi tipi di azioni. Queste azioni includono l'esecuzione dell'installazione predefinita per ogni nodo, un'installazione eseguita dopo che tutti i nodi sono stati distribuiti ed eventuali altri script specifici di un determinato modello.

Questo esempio deriva da un modello usato per distribuire MongoDB, che richiede un arbitro per offrire elevati livelli di disponibilità. *arbiterNodeInstallCommand* è stato aggiunto a *vmScripts* per installare l'arbitro.

Nella sezione delle variabili è possibile trovare le variabili che definiscono il testo specifico per eseguire lo script con i valori appropriati.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## Restituzione dello stato da un modello

Non è solo possibile passare i dati a un modello, ma anche condividerli di nuovo con il modello chiamante. Nella sezione **outputs** di un modello collegato, è possibile specificare le coppie chiave/valore che possono essere utilizzate dal modello di origine.

Il seguente esempio mostra come passare l'indirizzo IP privato generato in un modello collegato.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

All'interno del modello principale, è possibile usare tali dati con la sintassi seguente:

    "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]"
    }

## Passaggi successivi
- [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md)
- [Funzioni del modello di Gestione risorse di Azure](resource-group-template-functions.md)

<!---HONumber=August15_HO6-->