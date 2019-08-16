---
title: 'Soluzione VMware di Azure di CloudSimple: configurare la crittografia rete VSAN per il cloud privato'
description: Viene descritto come configurare la funzionalità di crittografia del software rete VSAN in modo che il cloud privato CloudSimple possa funzionare con un server di gestione delle chiavi in esecuzione nella rete virtuale di Azure.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a00cce852878d65ca08927d937f5a0f1f37c1b87
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544474"
---
# <a name="configure-vsan-encryption-for-your-cloudsimple-private-cloud"></a>Configurare la crittografia rete VSAN per il cloud privato CloudSimple

È possibile configurare la funzionalità di crittografia del software rete VSAN in modo che il cloud privato CloudSimple possa funzionare con un server di gestione delle chiavi in esecuzione nella rete virtuale di Azure.

VMware richiede l'uso di uno strumento del server di gestione delle chiavi di terze parti compatibile con KMIP 1,1 esterno quando si usa la crittografia rete VSAN. È possibile usare qualsiasi KMS supportato certificato da VMware ed è disponibile per Azure.

Di seguito viene illustrato come usare il servizio di gestione delle chiavi di HyTrust in esecuzione in una rete virtuale di Azure. Un approccio simile può essere usato per qualsiasi altra soluzione di gestione delle chiavi di terze parti certificata per rete VSAN.

Per questa soluzione KMS è necessario:

* Installare, configurare e gestire uno strumento di gestione delle chiavi di terze parti con certificazione VMware nella rete virtuale di Azure.
* Fornire le proprie licenze per lo strumento KMS.
* Configurare e gestire la crittografia rete VSAN nel cloud privato usando lo strumento KMS di terze parti in esecuzione nella rete virtuale di Azure.

## <a name="kms-deployment-scenario"></a>Scenario di distribuzione KMS

Il cluster del server del servizio di gestione delle chiavi viene eseguito nella rete virtuale di Azure ed è raggiungibile dal cloud privato vCenter sulla connessione di Azure ExpressRoute configurata.

![Cluster KMS nella rete virtuale di Azure](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Come distribuire la soluzione

Il processo di distribuzione prevede i passaggi seguenti:

1. [Verificare che siano soddisfatti i prerequisiti](#verify-that-prerequisites-are-met)
2. [Portale di CloudSimple: Ottenere informazioni sul peering ExpressRoute](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Portale di Azure: Connettere la rete virtuale al cloud privato](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Portale di Azure: Distribuire un cluster HyTrust per la gestione delle reti virtuali nella rete virtuale](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Configurare il server KMIP](#hytrust-webui-configure-the-kmip-server)
6. [interfaccia utente di vCenter: Configurare la crittografia rete VSAN per usare il cluster KMS nella rete virtuale di Azure](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-that-prerequisites-are-met"></a>Verificare che siano soddisfatti i prerequisiti

Prima di procedere alla distribuzione, verificare quanto segue:

* Il fornitore, lo strumento e la versione del servizio di gestione delle chiavi selezionati si trovano nell'elenco di compatibilità rete VSAN.
* Il fornitore selezionato supporta la versione dello strumento per l'esecuzione in Azure.
* La versione di Azure dello strumento KMS è conforme a KMIP 1,1.
* Sono già stati creati un Azure Resource Manager e una rete virtuale.
* Un cloud privato CloudSimple è già stato creato.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>Portale di CloudSimple: Ottenere informazioni sul peering ExpressRoute

Per continuare l'installazione, sono necessari la chiave di autorizzazione e l'URI del circuito peer per ExpressRoute e l'accesso alla sottoscrizione di Azure. Queste informazioni sono disponibili nella pagina connessione della rete virtuale nel portale di CloudSimple. Per istruzioni, vedere [configurare una connessione di rete virtuale al cloud privato](virtual-network-connection.md). Se si verificano problemi durante il recupero delle informazioni, aprire una <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">richiesta di supporto</a>.

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Portale di Azure: Connetti la tua rete virtuale al cloud privato

1. Creare un gateway di rete virtuale per la rete virtuale seguendo le istruzioni riportate in [configurare un gateway di rete virtuale per ExpressRoute usando il portale di Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
2. Collegare la rete virtuale al circuito ExpressRoute di CloudSimple seguendo le istruzioni riportate in [connettere una rete virtuale a un circuito ExpressRoute usando il portale](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
3. Usare le informazioni sul circuito CloudSimple ExpressRoute ricevute nel messaggio di posta elettronica di benvenuto da CloudSimple per collegare la rete virtuale al circuito CloudSimple ExpressRoute in Azure.
4. Immettere la chiave di autorizzazione e l'URI del circuito peer, assegnare un nome alla connessione e fare clic su **OK**.

![Fornire l'URI del circuito peer CS durante la creazione della rete virtuale](media/vsan-azureportal01.png)

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Portale di Azure: Distribuire un cluster HyTrust per il controllo delle reti in Azure Resource Manager nella rete virtuale

Per distribuire un cluster HyTrust di controllo delle attività nel Azure Resource Manager nella rete virtuale, eseguire le attività seguenti. Per informazioni dettagliate, vedere la <a href="https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0" target="_blank">documentazione di HyTrust</a> .

1. Creare un gruppo di sicurezza di rete di Azure (NSG-HyTrust) con le regole in ingresso specificate seguendo le istruzioni riportate nella documentazione di HyTrust.
2. Generare una coppia di chiavi SSH in Azure.
3. Distribuire il nodo di controllo delle maiuscole iniziale dall'immagine di Azure Marketplace.  Usare la chiave pubblica della coppia di chiavi generata e selezionare **NSG-HyTrust** come gruppo di sicurezza di rete per il nodo di controllo delle chiavi.
4. Convertire l'indirizzo IP privato del controllo di stato in un indirizzo IP statico.
5. Eseguire la connessione SSH alla macchina virtuale di controllo usando il relativo indirizzo IP pubblico e la chiave privata della coppia di chiavi citata in precedenza.
6. Quando richiesto nella shell SSH, selezionare ```No``` per impostare il nodo come nodo di controllo delle maiuscole iniziale.
7. Aggiungere altri nodi di controllo di stato ripetendo i passaggi 3-5 di ```Yes``` questa procedura e selezionando quando viene richiesto di aggiungere a un cluster esistente.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Configurare il server KMIP

Passare a https://*Public-IP*, dove *Public-IP* è l'indirizzo IP pubblico della VM del nodo di controllo delle macchine virtuali. Seguire questa procedura dalla <a href="https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0" target="_blank">documentazione di HyTrust</a>.

1. <a href="https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2" target="_blank">Configurazione di un server KMIP</a>
2. <a href="https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3" target="_blank">Creazione di un bundle di certificati per la crittografia VMware</a>

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>interfaccia utente di vCenter: Configurare la crittografia rete VSAN per usare il cluster KMS nella rete virtuale di Azure

Seguire le istruzioni HyTrust per <a href="https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4" target="_blank">creare un cluster del servizio di gestione delle chiavi in vCenter</a>.

![Aggiungere i dettagli del cluster KMS in vCenter](media/vsan-config01.png)

In vCenter passare a **Cluster > configurare** e selezionare l'opzione **generale** per rete VSAN. Abilitare la crittografia e selezionare il cluster del servizio di gestione delle chiavi aggiunto in precedenza a vCenter.

![Abilitare la crittografia rete VSAN e configurare il cluster KMS in vCenter](media/vsan-config02.png)

## <a name="references"></a>Riferimenti

### <a name="azure"></a>Azure

[Configurare un gateway di rete virtuale per ExpressRoute usando il portale di Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Connettere una rete virtuale a un circuito ExpressRoute usando il portale](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl e Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Configurazione di un server KMPI](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Creazione di un bundle di certificati per la crittografia VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Creazione del cluster del servizio di gestione delle chiavi in vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
