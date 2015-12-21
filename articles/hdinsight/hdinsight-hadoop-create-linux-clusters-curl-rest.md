<properties
   	pageTitle="Creare cluster Hadoop, HBase o Storm su Linux in HDInsight tramite cURL e l'API REST di Azure | Microsoft Azure"
   	description="Informazioni su come creare cluster HDInsight basati su Linux tramite cURL, i modelli di gestione risorse di Azure e l'API REST di Azure. È possibile specificare il tipo di cluster (Hadoop, HBase o Storm) o utilizzare gli script per installare i componenti personalizzati."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="12/04/2015"
   	ms.author="larryfr"/>

#Creare cluster basati su Linux in HDInsight tramite cURL e l’API REST di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-create-linux-cluster-selector.md)]

L'API REST di Azure consente di eseguire operazioni di gestione su servizi ospitati nella piattaforma Azure, inclusa la creazione di nuove risorse, ad esempio cluster HDInsight basati su Linux. In questo documento verrà descritto come creare modelli di Gestione risorse di Azure per configurare un cluster di HDInsight e l’archiviazione associata, quindi utilizzare cURL per distribuire il modello per l'API REST di Azure per creare un nuovo cluster HDInsight.

> [AZURE.IMPORTANT]I passaggi descritti in questo documento utilizzano il numero di nodi di lavoro predefinito (4) per un cluster HDInsight. Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
>
> Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Prerequisiti

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Interfaccia della riga di comando di Azure__. L’interfaccia della riga di comando di Azure viene utilizzata per creare un’entità servizio, quindi viene utilizzata per generare i token di autenticazione per le richieste all'API REST di Azure.

    Per informazioni sull'installazione dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

- __cURL__. Questa utilità è disponibile tramite il sistema di gestione del pacchetto o può essere scaricata da [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE]Se si utilizza PowerShell per eseguire i comandi in questo documento, è necessario innanzitutto rimuovere l’alias `curl` creato per impostazione predefinita. Questo alias utilizza Invoke-WebRequest, un cmdlet di PowerShell, anziché cURL quando si utilizza il comando `curl` da un prompt di PowerShell e restituisce errori per molti dei comandi utilizzati in questo documento.
    > 
    > Per rimuovere l'alias, utilizzare la seguente procedura dal prompt di PowerShell:
    >
    > ```Remove-item alias:curl`
    >
    > Una volta rimosso l'alias, sarà possibile utilizzare la versione di cURL installata nel sistema.

##Creare un modello

I modelli di Gestione risorse di Azure sono documenti JSON che descrivono un __gruppo di risorse__ e tutte le risorse in esso contenute, ad esempio HDInsight. Questo approccio basato sui modelli consente di definire tutte le risorse necessarie per HDInsight in un modello e di gestire le modifiche apportate all'intero gruppo mediante __distribuzioni__ che applicano modifiche al gruppo.

In genere i modelli vengono forniti modelli in due parti; il modello stesso e un file di parametri che viene popolato con valori specifici per la configurazione. Per esempio, nome del cluster, nome amministratore e password. Quando si utilizza direttamente l'API REST, è necessario combinarli in un file. Il formato di questo documento JSON è:

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Ad esempio, di seguito è un'unione dei file di modello e di parametri da [https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password), che consente di creare un cluster basato su Linux utilizzando una password per proteggere l'account utente SSH.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

Questo esempio verrà utilizzato nei passaggi di questo documento. È necessario sostituire il segnaposto _valori_ nella sezione __Parametri__ alla fine del documento con i valori che si desidera utilizzare per il cluster.

##Effettuare l'accesso alla sottoscrizione di Azure

Seguire i passaggi descritti in [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md) e connettersi alla sottoscrizione usando il metodo __login__.

##Creare un’entità servizio

> [AZURE.IMPORTANT]Quando si esegue la procedura descritta nell'articolo al collegamento di seguito, è necessario apportare le modifiche seguenti:
> 
> * Quando i passaggi indicano di utilizzare un valore di __lettore__, è necessario utilizzare invece __proprietario__. Verrà creata un’entità servizio in grado di apportare modifiche ai servizi sulla sottoscrizione, necessaria per la creazione di un cluster HDInsight.
>
> È inoltre necessario salvare le informazioni seguenti, utilizzate in questo processo:
> 
> * ID sottoscrizione: ricevuto quando si utilizza `azure account list`
> * ID tenant: ricevuto quando si utilizza `azure account list`
> * ID dell'applicazione: restituito durante la creazione dell'entità servizio
> * Password per l'entità servizio: utilizzata quando si crea l’entità servizio

Seguire i passaggi nella sezione _Autenticare l’entità servizio con una password - Interfaccia di riga di comando di Azure_ del documento [Autenticazione di un'entità servizio con Gestione risorse di Azure](https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/#authenticate-service-principal-with-password---azure-cli). Verrà creata una nuova entità servizio che può essere utilizzata per autenticare la richiesta di creazione del cluster.

##Ottenere un token di autenticazione

Utilizzare le operazioni seguenti per ottenere un nuovo token da Azure. Sostituire __TENANTID__, __APPLICATIONID__ e __PASSWORD__ con le informazioni salvate durante la creazione di un'entità servizio:

    curl -X "POST" "https://login.microsoftonline.com/TENANTID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=APPLICATIONID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=PASSWORD" \
    --data-urlencode "resource=https://management.azure.com/"

Se la richiesta ha esito positivo, si riceverà una risposta serie 200 e il corpo della risposta conterrà un documento JSON.

> [AZURE.IMPORTANT]Il documento JSON restituito da questa richiesta contiene un elemento denominato __access\_token__; il valore di questo elemento è il token di accesso da utilizzare per l'autenticazione delle richieste utilizzate nelle sezioni successive di questo documento.

##Creare un gruppo di risorse

Per creare un nuovo gruppo di risorse, seguire questa procedura. È necessario creare innanzitutto il gruppo prima di poter creare le risorse, ad esempio il cluster HDInsight.

* Sostituire __SUBSCRIPTIONID__ con l’ID sottoscrizione ricevuto durante la creazione dell'entità servizio.
* Sostituire __ACCESSTOKEN__ con il token di accesso ricevuto nel passaggio precedente.
* Sostituire __DATACENTERLOCATION__ con il datacenter in cui si desidera creare il gruppo di risorse e le risorse. Ad esempio "Stati Uniti centrali del sud". 
* Sostituire __GROUPNAME__ con il nome che si desidera usare per questo gruppo:

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d $'{ "location": "DATACENTERLOCATION" }’

Se la richiesta ha esito positivo, si riceverà una risposta serie 200 e il corpo della risposta conterrà un documento JSON che include le informazioni del gruppo. L’elemento `"provisioningState"` conterrà un valore di `"Succeeded"`.

##Creare una distribuzione

Utilizzare le operazioni seguenti per distribuire la configurazione del cluster (valori di modello e di parametri,) nel gruppo di risorse.

* Sostituire __SUBSCRIPTIONID__ e __ACCESSTOKEN__ con i valori utilizzati in precedenza. 
* Sostituire __GROUPNAME__ con il nome del gruppo di risorse creato nella sezione precedente.
* Sostituire __DEPLOYMENTNAME__ con il nome che si desidera utilizzare per la distribuzione.

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d "{impostare la stringa del corpo su modello e parametri}"

> [AZURE.NOTE]Se il documento JSON contenente il modello e i parametri è stato salvato in un file, è possibile utilizzare quanto indicato di seguito invece di `-d "{modello e parametri}"':
>
> ```--data-binary "@/path/to/file.json"```

Se la richiesta ha esito positivo, si riceverà una risposta serie 200 e il corpo della risposta conterrà un documento JSON che include le informazioni dell’operazione di distribuzione.

> [AZURE.IMPORTANT]Si noti che la distribuzione è stata inviata, ma non è stata completata in questo momento. Possono essere necessari diversi minuti, in genere circa 15, per completare la distribuzione.

##Controllare lo stato di una distribuzione

Per verificare lo stato della distribuzione, usare il comando seguente:

* Sostituire __SUBSCRIPTIONID__ e __ACCESSTOKEN__ con i valori utilizzati in precedenza. 
* Sostituire __GROUPNAME__ con il nome del gruppo di risorse creato nella sezione precedente.

    curl -X "GET" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json"

Restituirà informazioni di un documento JSON che contiene informazioni sull'operazione di distribuzione. L’elemento `"provisioningState"` conterrà lo stato della distribuzione; se contiene un valore di `"Succeeded"`, quindi la distribuzione è stata completata correttamente. A questo punto, il cluster deve essere disponibile per l'utilizzo.

##Passaggi successivi

Dopo aver creato un cluster HDInsight, usare le informazioni seguenti per acquisire familiarità con il cluster.

###Cluster Hadoop

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###Cluster HBase

* [Introduzione a HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Cluster Storm

* [Sviluppare topologie Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_1210_2015-->