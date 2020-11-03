---
title: Guida di avvio rapido - WildFly in CentOS
description: Distribuire applicazioni Java in WildFly in una macchina virtuale CentOS
author: Theresa-Nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.date: 10/23/2020
ms.openlocfilehash: 875d04751475d1d5236e9f15fbca585cdc9b1ab0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897626"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Avvio rapido: WildFly in CentOS 8

Questa guida di avvio rapido illustra come distribuire il nodo autonomo di WildFly in una macchina virtuale CentOS 8. È ideale per lo sviluppo e il test di applicazioni Java aziendali in Azure. Per distribuire questa guida di avvio rapido non è necessaria la sottoscrizione server dell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi dell'abbonamento a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) o [creare un account gratuito](https://azure.microsoft.com/pricing/free-trial).

## <a name="use-case"></a>Caso d'uso

WildFly è ideale per lo sviluppo e il test di applicazioni Java aziendali in Azure. Gli elenchi delle tecnologie incluse nei profili di configurazione server di WildFly 18 sono disponibili nella [guida introduttiva di WildFly](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly).

È possibile usare WildFly in modalità autonoma o cluster, a seconda del caso d'uso. Per garantire la disponibilità elevata delle applicazioni Jakarta EE critiche, è possibile usare WildFly su un cluster di nodi, apportare un numero limitato di modifiche alla configurazione dell'applicazione e quindi distribuire l'applicazione nel cluster. Per altre informazioni, vedere la [guida alla disponibilità elevata di WildFly](https://docs.wildfly.org/18/High_Availability_Guide.html).

## <a name="configuration-choice"></a>Scelta della configurazione

WildFly può essere avviato in modalità **server autonomo**. Un'istanza di server autonomo è un processo indipendente, molto simile a un'istanza del server applicazioni JBoss Application Server 3, 4, 5 o 6. Le istanze autonome possono essere avviate tramite lo script di avvio standalone.sh o standalone.bat. In caso di più istanze autonome, è responsabilità dell'utente coordinare la gestione multiserver tra i server.

È anche possibile avviare l'istanza di WildFly con una configurazione alternativa usando i file di configurazione disponibili nella cartella configuration.

Questi sono i file di configurazione della modalità server autonomo:

- standalone.xml (impostazione predefinita): questa configurazione è il file predefinito usato per avviare l'istanza di WildFly. Contiene la configurazione certificata del profilo Web Jakarta con le tecnologie necessarie.
   
- standalone-ha.xml: configurazione certificata del profilo Web Jakarta EE 8 con disponibilità elevata (destinata alle applicazioni Web).
   
- standalone-full.xml: configurazione certificata di Jakarta EE Platform 8, che include tutte le tecnologie necessarie per l'hosting delle applicazioni Jakarta EE.

- standalone-full-ha.xml: configurazione certificata di Jakarta EE Platform 8 con disponibilità elevata per l'hosting delle applicazioni Jakarta EE.

Per avviare il server WildFly autonomo con un'altra configurazione, usare l'argomento --server-config con il file server-config.

Ad esempio, per usare Jakarta EE Platform 8 con le funzionalità di clustering, usare il comando seguente:

`./standalone.sh --server-config=standalone-full-ha.xml`

Per altre informazioni sulle configurazioni, vedere la [guida introduttiva di WildFly](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations).

## <a name="licensing-support-and-subscription-notes"></a>Note sulle licenze, il supporto e le sottoscrizioni

L'immagine di Azure CentOS 8 è un'immagine di VM con pagamento in base al consumo e non richiede all'utente di ottenere una licenza. Al primo avvio della VM, viene attivato automaticamente il sistema operativo della VM dotato di licenza e viene addebitata una tariffa oraria, in aggiunta alle tariffe orarie Microsoft per le VM Linux. Per altre informazioni, vedere [Prezzi di Macchine virtuali Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux). WildFly può essere scaricato e usato gratuitamente e non richiede una sottoscrizione o una licenza di Red Hat.

## <a name="how-to-consume"></a>Modalità di utilizzo

È possibile distribuire il modello nei tre modi seguenti:

- Tramite PowerShell. Distribuire il modello eseguendo i comandi seguenti: Per informazioni sull'installazione e la configurazione di Azure PowerShell, vedere [Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

    `New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment`

    `New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json`
    
- Tramite l'interfaccia della riga di comando di Azure. Distribuire il modello eseguendo i comandi seguenti: Per informazioni sull'installazione e la configurazione dell'interfaccia della riga di comando di Azure multipiattaforma, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

    `az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment`

    `az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json`

- Tramite il portale di Azure. Distribuire il modello facendo clic <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">qui</a> per accedere al portale di Azure.

## <a name="arm-template"></a>Modello ARM

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank">WildFly 18 on CentOS 8 (stand-alone VM)</a>. Questo modello di avvio rapido crea un nodo autonomo di WildFly 18.0.1.Final sulla macchina virtuale CentOS 8 nel gruppo di risorse che include un IP privato per la VM, la rete virtuale e l'account di archiviazione di diagnostica. Distribuisce anche un'applicazione Java di esempio denominata JBoss-EAP in Azure su WildFly.

## <a name="resource-links"></a>Collegamenti alle risorse

* Altre informazioni su [WildFly 18](https://wildfly.org/18)
* Altre informazioni sulle [distribuzioni Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)
* [Documentazione per gli sviluppatori di Azure per Java](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Passaggi successivi

Per l'ambiente di produzione, vedere i modelli di Resource Manager per l'avvio rapido di Azure per Red Hat JBoss EAP:

Macchina virtuale RHEL autonoma con applicazione di esempio:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP su RHEL (VM autonoma)</a>

Macchine virtuali RHEL in cluster con applicazione di esempio:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP su RHEL (VM in cluster)</a>

Set di scalabilità di macchine virtuali RHEL in cluster con applicazione di esempio:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP su RHEL (set di scalabilità di macchine virtuali in cluster)</a>
