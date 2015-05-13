<properties
   pageTitle="Distribuzione di un modello tramite la CLI di Azure per Mac, Linux e Windows"
   description="Vengono descritti i passaggi di base per distribuire o aggiornare un modello."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="04/21/2015"
   ms.author="rasquill"/>

# Distribuzione di un modello tramite la CLI di Azure per Mac, Linux e Windows

## Installare curl, wget o altro strumento di download
Questo argomento viene utilizzato **curl**. Utilizzare Gestione pacchetti del sistema operativo o scaricarlo da [qui](http://curl.haxx.se/download.html).

## Scaricare il file di parametri di modello \(o il modello, se necessario\)

I passaggi seguenti consentono di distribuire un modello di Azure, anche se è complessa. In questo argomento viene utilizzato il modello crea un server Ubuntu base con l'estensione VM customscript installato come un esempio. I file sono inclusi anche alla fine dell'argomento di riferimento.

### Scaricare il file azuredeploy parameters.json

Scaricare il file azuredeploy parameters.json se ne esiste per il modello che si desidera distribuire.

    curl -O https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy-parameters.json
    
## Immettere le informazioni sulla distribuzione gruppo di risorse
    
Aprire il file con l'editor preferito. Noterete che è necessario specificare un valore per molte delle chiavi, in particolare **adminUsername**, **adminPassword** \(tenere presente che le regole di complessità!\) e il nome account di archiviazione e i nomi DNS che si desidera.
    
    {
      "newStorageAccountName": {
        "value": "uniquestorageaccountname"
      },
      "adminUsername": {
        "value": ""
      },
      "adminPassword": {
        "value": ""
      },
      "dnsNameForPublicIP": {
        "value": "uniquednsnameforpublicip"
      },
      "vmSourceImageName": {
        "value": "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_2_LTS-amd64-server-20150309-it-it-30GB"
      },
      "location": {
        "value": "West US"
      },
      "virtualNetworkName": {
        "value": "myVNET"
      },
      "vmSize": {
        "value": "Standard_A0"
      },
      "vmName": {
        "value": "myVM"
      },
      "publicIPAddressName": {
        "value": "myPublicIP"
      },
      "nicName": {
        "value": "myNic"
      }
    }
    
Aggiungere nuovi valori, Azure creerà nuove risorse di archiviazione e di risorse DNS per se possibile, o utilizzare le risorse che è stato già creato. Il file azuredeploy parameters.json riportato di seguito viene illustrato un esempio:




l'url seguente estrae i file dei parametri dal "empty" azuredeploy-parameters.json, che funziona se si utilizza il metodo interattivo. Se si utilizza lo scaricati e personalizzati parametri file approccio, è necessario utilizzare--file di modello <template-file> opzione. Ho anche gli script scritti che estrarre singole sezioni di qualsiasi parte di questi file, a seconda che si desidera eseguire. È opportuno ricordare che per eseguire l'analisi json può desiderare jq: curl http://stedolan.github.io/jq/download/linux64/jq /usr/bin/jq -o


### Distribuire i file di modello e parametri


[AZURE.NOTE]È probabile che alcuni modelli non possono avere alcun file azuredeploy parameters.json correspdonding.

parametri per impostare o potrebbero essere già una parte del modello, a seconda di quali modelli si utilizza. In questi casi, potrebbe essere

Se il modello contiene i parametri direttamente o si desidera estrarre manualmente i dati dei parametri. Per ulteriori informazioni sulla struttura dei modelli, vedere [lingua del modello di gestione delle risorse Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).


https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy.json \(o solo il file azuredeploy parameters.json\) È possibile estrarre sia la sezione di parametri del modello, nel qual caso sarà necessario salvarlo nuovamente all'interno del modello stesso o come file separato azuredeploy-parameters.json. È necessario passare a ottenere i valori da inserire nei parametri.

## Modificare il file azuredeploy templates.json

Raccogliere i valori che è necessario

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Vestibul nuovi livelli ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit, eros pretium di purus diam, vitae tincidunt nulla lorem sed turpis: [3 collegamento a un altro argomento della documentazione azure.microsoft.com](storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--HONumber=52-->
