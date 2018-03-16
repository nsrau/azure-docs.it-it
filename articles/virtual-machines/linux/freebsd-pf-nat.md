---
title: Come usare Packet Filter di FreeBSD per creare un firewall in Azure | Microsoft Docs
description: Informazioni su come distribuire un firewall NAT usando PF di FreeBSD in Azure.
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 0bed776526e3e2f3ee4cdce596c591d0b8419030
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Procedura: usare Packet Filter di FreeBSD per creare un firewall sicuro in Azure
In questo articolo viene illustrato come distribuire un firewall NAT usando Packet Filter di FreeBSD tramite un modello di Azure Resource Manager per uno scenario server Web comune.

## <a name="what-is-pf"></a>Informazioni su PF
PF (Packet Filter, chiamato anche pf) è un filtro di pacchetti con stato con licenza BSD, un componente software fondamentale per un firewall. Dalla sua creazione, PF si è rapidamente evoluto e ora dispone di numerosi vantaggi rispetto ad altri firewall disponibili. La funzionalità Network Address Translation (NAT) è sempre stata presente in PF. In seguito sono state aggiunte le funzionalità di pianificazione dei pacchetti e di gestione della coda attiva, integrando ALTQ e rendendolo configurabile tramite PF. Anche le funzionalità come pfsync e CARP per il failover e la ridondanza, authpf per l'autenticazione delle sessione e ftp-proxy per facilitare le operazioni del firewall sul complesso protocollo FTP dispongono di PF esteso. In breve, PF è un firewall potente e ricco di funzionalità. 

## <a name="get-started"></a>Attività iniziali
Se si desidera configurare un firewall sicuro nel cloud per i server Web, proseguire nella lettura dell'articolo. È inoltre possibile applicare gli script usati in questo modello di Azure Resource Manager per configurare la topologia di rete.
Il modello Azure Resource Manager configura una macchina virtuale FreeBSD che esegue operazioni NAT/reindirizzamento tramite PF e due macchine virtuali FreeBSD con installato e configurato il server Web Nginx. Oltre a eseguire NAT per i il traffico in uscita dei due server Web, la macchina virtuale di NAT/reindirizzamento intercetta le richieste HTTP e le reindirizza ai due server Web in modo round robin. La rete virtuale usa lo spazio di indirizzi IP non instradabile 10.0.0.2/24 ed è possibile modificare i parametri del modello. Il modello di Azure Resource Manager definisce inoltre una tabella route per l'intera rete virtuale, ovvero una raccolta di route individuali usate per sostituire quelle predefinite di Azure in base all'indirizzo IP di destinazione. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Distribuire tramite l'interfaccia della riga di comando di Azure
È necessario aver installato l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/reference-index#az_login). Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella posizione `West US`.

```azurecli
az group create --name myResourceGroup --location westus
```

Quindi distribuire il modello [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) con [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Scaricare [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) nello stesso percorso e definire i valori di risorse specifici, ad esempio `adminPassword`, `networkPrefix` e `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Dopo circa cinque minuti, si otterranno le informazioni su `"provisioningState": "Succeeded"`. Sarà quindi possibile usare ssh verso la macchina virtuale front-end (NAT) o accedere al server Web Nginx in un browser usando l'indirizzo IP pubblico o FQDN della macchina virtuale front-end (NAT). Nell'esempio seguente vengono elencati gli indirizzi FQDN e IP pubblici assegnati alla macchina virtuale front-end (NAT) nel gruppo di risorse `myResourceGroup`. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Passaggi successivi
Si desidera configurare un proprio NAT in Azure? Open Source, gratuito ma potente? PF è quindi una scelta ottimale. Usando il modello [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) sono necessari solo cinque minuti per configurare un firewall NAT con bilanciamento del carico round robin tramite PF di FreeBSD in Azure per uno scenario di server Web comune. 

Se si desiderano altre informazioni sull'offerta di FreeBSD in Azure, fare riferimento all'[introduzione a FreeBSD in Azure](freebsd-intro-on-azure.md).

Se si desidera approfondire la conoscenza di PF, fare riferimento al [manuale di FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) o al [Manuale dell'utente di PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
