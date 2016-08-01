<properties
	pageTitle="Procedure consigliate per il modello di Resource Manager | Microsoft Azure"
	description="Linee guida per la semplificazione dei modelli di Azure Resource Manager."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="tomfitz"/>

# Procedure consigliate per la creazione di modelli di Azure Resource Manager

Le linee guida seguenti consentono di creare modelli di Resource Manager affidabili e facili da usare e vengono fornite solo come suggerimenti e non requisiti assoluti. Lo scenario potrebbe richiedere variazioni rispetto a queste linee guida.

## Nomi di risorse

In genere esistono tre tipi di nomi di risorse da usare:

1. Nomi di risorse che devono essere univoci.
2. Nomi di risorse che non devono necessariamente essere univoci, ma che si desidera identifichino il contesto.
3. Nomi di risorse che possono essere generici.

Per informazioni sulla creazione di una convenzione di denominazione, vedere [Infrastructure naming guidelines](./virtual-machines/virtual-machines-windows-infrastructure-naming-guidelines.md) (Linee guida di denominazione di un'infrastruttura). Per informazioni sulle restrizioni relative ai nomi di risorse, vedere [Recommended naming conventions for Azure resources](./guidance/guidance-naming-conventions.md) (Convenzioni di denominazione consigliate per le risorse di Azure).

### Nomi di risorse univoci

È necessario fornire un nome univoco per qualsiasi tipo di risorsa con un endpoint di accesso ai dati. Alcuni tipi comuni che richiedono un nome univoco includono:

- Account di archiviazione
- Sito Web
- Server SQL
- Insieme di credenziali delle chiavi
- Cache Redis
- Account Batch
- Gestione traffico
- Servizio di ricerca
- Cluster HDInsight

I nomi di account di archiviazione devono essere formati da lettere minuscole, un massimo di 24 caratteri e non devono includere alcun trattino.

Anziché fornire un parametro per questi nomi di risorse e tentare di individuare un nome univoco durante la distribuzione, è possibile creare una variabile che usi la funzione [uniqueString()](resource-group-template-functions.md#uniquestring) per generare un nome. Spesso è consigliabile aggiungere un prefisso o un suffisso al risultato **uniqueString** per poter determinare in modo più semplice il tipo di risorsa tramite il nome. Ad esempio, è possibile generare un nome univoco per un account di archiviazione con la variabile seguente.

    "variables": {
        "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
    }

Gli account di archiviazione con un prefisso uniqueString non verranno raggruppati negli stessi rack.

### Nomi di risorse per l'identificazione

Per denominare i tipi di risorse di cui non è necessario garantire l'univocità, è sufficiente fornire un nome che identifichi il contesto e il tipo di risorsa. È opportuno attribuire un nome descrittivo che ne faciliti il riconoscimento in un elenco di nomi di risorse. Se è necessario modificare il nome di risorsa durante le distribuzioni, usare un parametro per il nome:

    "parameters": {
        "vmName": { 
            "type": "string",
            "defaultValue": "demoLinuxVM",
            "metadata": {
                "description": "The name of the VM to create."
            }
        }
    }

Se non è necessario passare un nome durante la distribuzione, usare una variabile:

    "variables": {
        "vmName": "demoLinuxVM"
    }

In alternativa, un valore hardcoded:

    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "demoLinuxVM",
      ...
    }

### Nomi di risorse generici

Per i tipi di risorse in gran parte accessibili tramite un'altra risorsa, è possibile usare un nome generico che sia hardcoded nel modello. Ad esempio, probabilmente non si vorrà assegnare un nome personalizzabile per le regole del firewall in SQL Server.

    {
        "type": "firewallrules",
        "name": "AllowAllWindowsAzureIps",
        ...
    }

## Parametri

1. Ridurre al minimo i parametri laddove possibile. Se è possibile usare una variabile o un valore letterale, è necessario farlo. Specificare solo parametri per:
 - Impostazioni da variare in base all'ambiente (ad esempio SKU, dimensione o capacità).
 - Nomi di risorse da specificare per facilitare l'identificazione.
 - Valori usati spesso per completare altre attività (ad esempio nome utente dell'amministratore).
 - Segreti (ad esempio password)
 - Il numero o la matrice di valori da usare durante la creazione di più istanze di un tipo di risorsa.

1. I nomi dei parametri devono seguire la **notazione Camel**.

1. Fornire una descrizione nei metadati per ogni parametro.

        "parameters": {
            "storageAccountType": {
                "type": "string",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }

1. Definire i valori predefiniti per i parametri (ad eccezione delle password e delle chiavi SSH).

        "parameters": {
            "storageAccountType": {
                "type": "string",
                "defaultValue": "Standard_GRS",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }

1. Usare **securestring** per tutte le password e i segreti.

        "parameters": {
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        }
 
1. Quando possibile, evitare di usare un parametro per specificare la **posizione**. Usare invece la proprietà location del gruppo di risorse. Tramite l'espressione **resourceGroup().location** per tutte le risorse, le risorse nel modello verranno distribuite nella stessa posizione del gruppo di risorse.

        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              ...
          }
        ]
  
     Se un tipo di risorsa è supportato solo in un numero limitato di posizioni, provare a specificare una posizione valida direttamente nel modello. Se è necessario usare un parametro location, condividere per quanto possibile il relativo valore con le risorse che potrebbero essere nella stessa posizione. Questo approccio permette di ridurre al minimo gli utenti che devono fornire percorsi per ogni tipo di risorsa.

1. Evitare di usare un parametro o una variabile per la versione dell'API per un tipo di risorsa. I valori e le proprietà delle risorse possono variare in base al numero di versione. IntelliSense negli editor di codice non sarà in grado di determinare lo schema corretto quando la versione dell'API è impostata su un parametro o una variabile. Impostare invece la versione dell'API come hardcoded nel modello.

## Variabili 

1. Usare le variabili per i valori da usare più volte in un modello. Se un valore viene usato una sola volta, un valore hardcoded renderà più facile la lettura del modello.

1. Non è possibile usare la funzione [reference](resource-group-template-functions.md#reference) nella sezione delle variabili. La funzione reference deriva il proprio valore dallo stato di runtime della risorsa, ma le variabili vengono risolte durante l'analisi iniziale del modello. Costruire invece valori che richiedono la funzione **reference** direttamente nella sezione **resources** o **outputs** del modello.

1. Includere le variabili per i nomi di risorse che devono essere univoci, come illustrato in [Nomi di risorse](#resource-names).

1. È possibile raggruppare le variabili in oggetti complessi. È possibile fare riferimento a un valore da un oggetto complesso nel formato **variable.subentry**. Il raggruppamento delle variabili contribuisce a tenere traccia di variabili correlate e migliora la leggibilità del modello.

        "variables": {
            "storage": {
                "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
                "type": "Standard_LRS"
            }
        },
        "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storage').name]",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "sku": {
                  "name": "[variables('storage').type]"
              },
              ...
          }
        ]
 
     > [AZURE.NOTE] Un oggetto complesso non può contenere un'espressione che fa riferimento a un valore da un oggetto complesso. A questo scopo, definire una variabile separata.

     Per esempi avanzati di uso di oggetti complessi come variabili, vedere [Condivisione dello stato in modelli di Azure Resource Manager](best-practices-resource-manager-state.md).

## Risorse

1. Specificare **comments** per ogni risorsa nel modello per consentire ad altri collaboratori di comprendere lo scopo della risorsa.

        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "comments": "This storage account is used to store the VM disks",
              ...
          }
        ]

1. Usare i tag per aggiungere metadati alle risorse allo scopo di aumentare le informazioni sulle risorse. Ad esempio, è possibile aggiungere metadati a una risorsa quali dettagli di fatturazione. Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md).

1. Se si usa un **endpoint pubblico** nel modello, ad esempio un endpoint pubblico di archiviazione BLOB, **non impostare come hardcoded** lo spazio dei nomi. Usare la funzione **reference** per recuperare lo spazio dei nomi in modo dinamico. Ciò consente di distribuire il modello in ambienti diversi dello spazio dei nomi pubblico, senza modificare manualmente l'endpoint nel modello. Impostare apiVersion sulla stessa versione in uso per storageAccount nel modello.

        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }

     Se l'account di archiviazione viene distribuito nello stesso modello, non è necessario specificare lo spazio dei nomi del provider quando si fa riferimento alla risorsa. La sintassi semplificata è:
     
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }

     Se si dispone di altri valori nel modello configurato con uno spazio dei nomi pubblico, modificarli in modo da riflettere la stessa funzione di riferimento. Ad esempio, la proprietà storageUri di diagnosticsProfile della macchina virtuale.

        "diagnosticsProfile": {
            "bootDiagnostics": {
                "enabled": "true",
                "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
            }
        }
 
     È anche possibile **fare riferimento** a un account di archiviazione in un gruppo di risorse diverso.


        "osDisk": {
            "name": "osdisk", 
            "vhd": {
                "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
            }
        }

1. Assegnare publicIPAddresses a una macchina virtuale solo se richiesto per un'applicazione. Per connettersi a fini amministrativi, di gestione o debug, usare inboundNatRules, virtualNetworkGateways o un jumpbox.

     Per altre informazioni sulla connessione alle macchine virtuali, vedere:
     - [Running VMs for an N-tier architecture on Azure](./guidance/guidance-compute-3-tier-vm.md) (Esecuzione di VM per un'architettura a più livelli su Azure)
     - [Configurare l'accesso WinRM per le macchine virtuali in Azure Resource Manager](./virtual-machines/virtual-machines-windows-winrm.md)
     - [Consentire l'accesso esterno alla VM mediante il portale di Azure](./virtual-machines/virtual-machines-windows-nsg-quickstart-portal.md)
     - [Consentire l'accesso esterno alla VM mediante PowerShell](./virtual-machines/virtual-machines-windows-nsg-quickstart-powershell.md)
     - [Apertura di porte e di endpoint](./virtual-machines/virtual-machines-linux-nsg-quickstart.md)

1. La proprietà **domainNameLabel** per publicIPAddresses deve essere univoca, deve comprendere tra 3 e 63 caratteri e deve seguire le regole specificate da questa espressione regolare `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Dal momento che la funzione uniqueString genera una stringa di 13 caratteri, nell'esempio seguente si presume che si sia verificato che la stringa di prefisso dnsPrefixString abbia un massimo di 50 caratteri e che sia conforme a tali regole.

        "parameters": {
            "dnsPrefixString": {
                "type": "string",
                "maxLength": 50,
                "metadata": {
                    "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
                }
            }
        },
        "variables": {
            "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
        }

1. Quando si aggiunge una password a un'estensione **customScriptExtension**, usare la proprietà **commandToExecute** in protectedSettings.

        "properties": {
            "publisher": "Microsoft.OSTCExtensions",
            "type": "CustomScriptForLinux",
            "settings": {
                "fileUris": [
                    "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
                ]
            },
            "protectedSettings": {
                "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
            }
        }

     > [AZURE.NOTE] Per garantire che i segreti passati come parametri a virtualMachines/extensions siano crittografati, è necessario usare la proprietà protectedSettings delle estensioni pertinenti.

## Output

Se un modello consente di creare un **publicIPAddresses** nuovo, allora deve avere una sezione **output** contenente i dettagli dell'indirizzo IP e un dominio completamente qualificato creato per recuperare facilmente queste informazioni dopo la distribuzione. Quando si fa riferimento alla risorsa, usare la versione dell'API usata per crearlo.

```
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## Modello singolo o modelli annidati

Per distribuire la soluzione, è possibile usare un modello singolo o un modello principale con più modelli annidati. I modelli annidati sono comuni per scenari più avanzati. I modelli annidati presentano i vantaggi seguenti:

1. È possibile scomporre la soluzione in componenti di destinazione
2. È possibile riusare i modelli annidati con modelli principali diversi

Quando si decide di scomporre la progettazione del modello in più modelli annidati, le linee guida seguenti aiutano a standardizzare la progettazione. Queste linee guida si basano sulla documentazione descritta nell'articolo [Procedure consigliate per la progettazione di modelli di Azure Resource Manager](best-practices-resource-manager-design-templates.md). La progettazione consigliata è costituita dai modelli seguenti.

+ **Modello principale** (azuredeploy.json). Usato per i parametri di input.
+ **Modello di risorse condivise**. Consente di distribuire le risorse condivise usate da tutte le altre risorse (ad esempio, rete virtuale, set di disponibilità). L'espressione dependsOn impone che questo modello venga distribuito prima degli altri.
+ **Modello di risorse facoltative**. Consente di distribuire risorse in modo condizionale in base a un parametro, ad esempio un jumpbox
+ **Modello di risorse membro**. Ogni tipo di istanza all'interno di un livello di applicazione prevede una propria configurazione. All'interno di un livello è possibile definire tipi di istanza diversi. Ad esempio, la prima istanza crea un nuovo cluster, le istanze aggiuntive vengono aggiunte al cluster esistente. Ogni tipo di istanza avrà un proprio modello di distribuzione.
+ **Script**. Per ogni tipo di istanza sono applicabili script ampiamente riutilizzabili, come ad esempio quelli di inizializzazione e formattazione di dischi aggiuntivi. Gli script personalizzati vengono creati per scopi di personalizzazione specifici e variano in base al tipo di istanza.

![modello annidato](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Per altre informazioni, vedere [Uso di modelli collegati con Gestione risorse di Azure](resource-group-linked-templates.md).

## Collegarsi in modo condizionale al modello annidato

È possibile collegarsi in modo condizionale ai modelli annidati usando un parametro che diventa parte dell'URI per il modello.

    "parameters": {
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },
    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                }
            }
        }
    ]

## Formato del modello

1. È consigliabile passare il modello tramite una convalida JSON per rimuovere virgole, parentesi e parentesi quadre estranee che potrebbero causare un errore durante la distribuzione. Provare [JSONlint](http://jsonlint.com/) o un pacchetto linter per l'ambiente di modifica preferito (Visual Studio Code, Atom, Sublime Text, Visual Studio, ecc.)
1. È anche consigliabile formattare il codice JSON per una migliore leggibilità. È possibile usare un pacchetto formattatore JSON per l'editor locale. In Visual Studio formattare il documento con **Ctrl+K, Ctrl+D**. In VS Code usare **Alt+Shift+F**. Se l'editor locale non formatta il documento, è possibile usare un [formattatore online](https://www.bing.com/search?q=json+formatter).

## Passaggi successivi

1. Per indicazioni sull'architettura della soluzione per le macchine virtuali, vedere [Running a Windows VM on Azure](./guidance/guidance-compute-single-vm.md) (Esecuzione di una VM Windows su Azure) e [Running a Linux VM on Azure](./guidance/guidance-compute-single-vm-linux.md) (Esecuzione di una VM Linux su Azure).
2. Per indicazioni sulla configurazione di un account di archiviazione, vedere [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](./storage/storage-performance-checklist.md).
3. Per assistenza con le reti virtuali, vedere [Networking infrastructure guidelines](./virtual-machines/virtual-machines-windows-infrastructure-networking-guidelines.md) (Linee guida sull'infrastruttura di rete).

<!---HONumber=AcomDC_0720_2016-->