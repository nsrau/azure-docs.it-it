---
title: Azure VMware Solution by CloudSimple - Configure vSAN encryption for Private Cloud
description: Viene descritto come configurare la funzionalità di crittografia software vSAN in modo che il cloud privato CloudSimple possa usare un server di gestione delle chiavi in esecuzione nella rete virtuale di Azure.Describes how to configure vSAN software encryption feature so your CloudSimple Private Cloud can work with a key management server running in your Azure virtual network.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020642"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>Configurare la crittografia vSAN per CloudSimple Private Cloud

È possibile configurare la funzionalità di crittografia software vSAN in modo che il cloud privato CloudSimple possa funzionare con un server di gestione delle chiavi in esecuzione nella rete virtuale di Azure.You can configure the vSAN software encryption feature so your CloudSimple Private Cloud can work with a key management server running in your Azure virtual network.

VMware richiede l'utilizzo di uno strumento esterno del server di gestione delle chiavi (KMS) conforme al KMIP 1.1 quando si utilizza la crittografia vSAN. È possibile sfruttare qualsiasi servizio di gestione delle chiavi supportato certificato da VMware ed è disponibile per Azure.You can leverage any supported KMS that is certified by VMware and is available for Azure.

Questa guida descrive come usare HyTrust KeyControl KMS in esecuzione in una rete virtuale di Azure.This guide describes how to use HyTrust KeyControl KMS running in an Azure virtual network. Un approccio simile può essere utilizzato per qualsiasi altra soluzione di terze parti certificata per vSAN.

Questa soluzione KMS richiede di:

* Installare, configurare e gestire uno strumento di terze parti certificato VMware nella rete virtuale di Azure.Install, configure, and manage a VMware certified third-party KMS tool in your Azure virtual network.
* Fornire le proprie licenze per lo strumento KMS.
* Configurare e gestire la crittografia vSAN nel cloud privato usando lo strumento KMS di terze parti in esecuzione nella rete virtuale di Azure.Configure and manage vSAN encryption in your Private Cloud using the third-party KMS tool running in your Azure virtual network.

## <a name="kms-deployment-scenario"></a>Scenario di distribuzione del Servizio di gestione delle chiavi

Il cluster di server KMS viene eseguito nella rete virtuale di Azure ed è ip raggiungibile dal vCenter del cloud privato tramite la connessione Azure ExpressRoute configurata.

![.. /media/KMS cluster nella rete virtuale di Azure](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Come distribuire la soluzione

Il processo di distribuzione prevede i passaggi seguenti:The deployment process has the following steps:

1. [Verificare che i prerequisiti siano soddisfatti](#verify-prerequisites-are-met)
2. [Portale CloudSimple: ottenere informazioni sul peering ExpressRouteCloudSimple portal: Obtain ExpressRoute Peering Information](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Portale di Azure: Connettere la rete virtuale al cloud privatoAzure portal: Connect your virtual network to the Private Cloud](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Portale di Azure: distribuire un cluster HyTrust KeyControl nella rete virtualeAzure portal: Deploy a HyTrust KeyControl Cluster in your virtual network](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Configurare il server KMIP](#hytrust-webui-configure-the-kmip-server)
6. [Interfaccia utente di vCenter: Configurare la crittografia vSAN per l'uso del cluster KMS nella rete virtuale di Azure](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Verificare che i prerequisiti siano soddisfatti

Verificare quanto segue prima della distribuzione:

* Il fornitore, lo strumento e la versione del Servizio di gestione delle chiavi selezionati sono presenti nell'elenco di compatibilità vSAN.
* The selected vendor supports a version of the tool to run in Azure.
* La versione di Azure dello strumento KMS è compatibile con KMIP 1.1.
* Sono già stati creati un Azure Resource Manager e una rete virtuale.
* È già stato creato un cloud privato CloudSimple.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>Portale CloudSimple: ottenere informazioni sul peering ExpressRouteCloudSimple portal: Obtain ExpressRoute peering information

To continue the setup, you need the authorization key and peer circuit URI for ExpressRoute plus access to your Azure Subscription. Queste informazioni sono disponibili nella pagina Connessione di rete virtuale nel portale CloudSimple.This information is available on the Virtual Network Connection page in the CloudSimple portal. Per istruzioni, vedere Configurare una connessione di [rete virtuale al cloud privato.](virtual-network-connection.md) In caso di problemi nell'ottenere le informazioni, aprire una richiesta di [supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Portale di Azure: Connettere la rete virtuale al cloud privatoAzure portal: Connect your virtual network to your Private Cloud

1. Creare un gateway di rete virtuale per la rete virtuale seguendo le istruzioni in Configurare un gateway di rete virtuale per ExpressRoute usando il portale di Azure.Create a virtual network gateway for your virtual network by following the instructions in [Configure a virtual network gateway for ExpressRoute using the Azure portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
2. Collegare la rete virtuale al circuito CloudSimple ExpressRoute seguendo le istruzioni in [Connettere una rete virtuale a un circuito ExpressRoute tramite il portale](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
3. Usare le informazioni sul circuito CloudSimple ExpressRoute ricevute nella posta elettronica di benvenuto da CloudSimple per collegare la rete virtuale al circuito CloudSimple ExpressRoute in Azure.Use the CloudSimple ExpressRoute circuit information received in your welcome email from CloudSimple to link your virtual network to the CloudSimple ExpressRoute circuit in Azure.
4. Immettere la chiave di autorizzazione e l'URI del circuito peer, assegnare un nome alla connessione e fare clic su **OK**.

![Fornire l'URI del circuito peer CS durante la creazione della rete virtualeProvide CS peer circuit URI when creating the virtual network](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Portale di Azure: distribuire un cluster HyTrust KeyControl in Azure Resource Manager nella rete virtualeAzure portal: Deploy a HyTrust KeyControl cluster in the Azure Resource Manager in your virtual network

Per distribuire un cluster HyTrust KeyControl in Azure Resource Manager nella rete virtuale, eseguire le attività seguenti. Per informazioni dettagliate, vedere la [documentazione di HyTrust.](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

1. Creare un gruppo di sicurezza di rete di Azure (nsg-hytrust) con le regole in ingresso specificate seguendo le istruzioni nella documentazione di HyTrust.Create an Azure network security group (nsg-hytrust) with specified inbound rules by following the instructions in the HyTrust documentation.
2. Generare una coppia di chiavi SSH in Azure.Generate an SSH key pair in Azure.
3. Distribuire il nodo KeyControl iniziale dall'immagine in Azure Marketplace.Deploy the initial KeyControl node from the image in Azure Marketplace.  Usare la chiave pubblica della coppia di chiavi generata e selezionare **nsg-hytrust** come gruppo di sicurezza di rete per il nodo KeyControl.
4. Convertire l'indirizzo IP privato di KeyControl in un indirizzo IP statico.
5. SSH alla macchina virtuale KeyControl utilizzando il relativo indirizzo IP pubblico e la chiave privata della coppia di chiavi menzionata in precedenza.
6. Quando richiesto nella shell SSH, selezionare per impostare `No` il nodo come nodo KeyControl iniziale.
7. Aggiungere altri nodi KeyControl ripetendo i passaggi `Yes` da 3 a 5 di questa procedura e selezionando quando viene richiesto di aggiungere a un cluster esistente.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Configurare il server KMIP

Passare a https://*public-ip*, dove *public-ip* è l'indirizzo IP pubblico della macchina virtuale del nodo KeyControl. Attenersi alla seguente procedura dalla [documentazione HyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0).

1. [Configurazione di un server KMIP](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Creazione di un pacchetto di certificati per la crittografia VMwareCreating a Certificate Bundle for VMware Encryption](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>Interfaccia utente di vCenter: Configurare la crittografia vSAN per l'uso del cluster KMS nella rete virtuale di Azure

Seguire le istruzioni HyTrust per [creare un cluster KMS in vCenter](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![Aggiungere i dettagli del cluster KMS in vCenter](media/vsan-config01.png)

In vCenter passare a **Cluster > Configura** e selezionare **l'opzione Generale** per vSAN. Abilitare la crittografia e selezionare il cluster KMS aggiunto in precedenza a vCenter.

![Abilitare la crittografia vSAN e configurare il cluster KMS in vCenter](media/vsan-config02.png)

## <a name="references"></a>Riferimenti

### <a name="azure"></a>Azure

[Configurare un gateway di rete virtuale per ExpressRoute usando il portale di Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Connettere una rete virtuale a un circuito ExpressRoute usando il portale](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust (Itrust)

[HyTrust DataControl e Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Configurazione di un server KMPI](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Creazione di un pacchetto di certificati per la crittografia VMwareCreating a Certificate Bundle for VMware Encryption](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Creazione del cluster KMS in vSphereCreating the KMS Cluster in vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
