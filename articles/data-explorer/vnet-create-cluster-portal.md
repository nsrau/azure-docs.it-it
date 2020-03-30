---
title: Creare un cluster di Azure Data Explorer & un database nella rete virtualeCreate an Azure Data Explorer cluster & DB in your virtual network
description: In questo articolo viene illustrato come creare un cluster di Azure Data Explorer nella rete virtuale.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 4dff471fa0f2194756409e01512ed223a1d46024
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241439"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Creare un cluster di Azure Data Explorer nella rete virtualeCreate an Azure Data Explorer cluster in your virtual network

Azure Data Explorer supporta la distribuzione di un cluster in una subnet nella rete virtuale (VNet). Questa funzionalità consente di accedere al cluster privatamente dalla rete virtuale di Azure o in locale, accedere alle risorse, ad esempio Hub eventi e Archiviazione all'interno della rete virtuale, e limitare il traffico in ingresso e in uscita.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Accedere al [portale](https://portal.azure.com/)di Azure .

## <a name="create-network-security-group-nsg"></a>Creare un gruppo di sicurezza di rete

I gruppi di sicurezza di rete (NSG) consentono di controllare l'accesso alla rete all'interno di una rete [virtuale.Network Security Groups (NSG)](/azure/virtual-network/security-overview) provide the ability to control network access within a VNet. È possibile accedere a Azure Data Explorer usando due endpoint HTTP (443) e TDS (1433). Le regole del gruppo di sicurezza di rete seguenti devono essere configurate per consentire l'accesso a questi endpoint per la gestione, il monitoraggio e il corretto funzionamento del cluster.

Per creare il gruppo di sicurezza di rete:

1. Nell'angolo superiore sinistro del portale, selezionare il pulsante **: Crea una risorsa.**
1. Cercare *Il gruppo di sicurezza*di rete .
1. In **Gruppo di sicurezza**di rete , nella parte inferiore della schermata, selezionare **Crea**.
1. Nella finestra **Crea gruppo** di sicurezza di rete immettere le informazioni seguenti.

   ![Crea modulo NSG](media/vnet-create-cluster-portal/network-security-group.png)

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per il cluster.|
    | Resource group | Gruppo di risorse in uso | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
    | Nome | AzureDataExplorerNsg | Scegliere un nome che identifichi il gruppo di sicurezza di rete (NSG) nel gruppo di risorse.  |
    | Region | *Stati Uniti occidentali* | Selezionare l'area più appropriata in base alle esigenze.
    | | | |

1. Selezionare **Rivedi e crea** per rivedere i dettagli del cluster e **Crea** per effettuarne il provisioning.

1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.

    ![Vai alla risorsa](media/vnet-create-cluster-portal/notification-nsg.png)

1. Nella scheda Regole di **sicurezza in ingresso** , selezionare **Aggiungi**.
1. Nella finestra **Aggiungi regola di sicurezza in ingresso** immettere le informazioni seguenti.

    ![Crea modulo regola in ingresso gruppo di sicurezza di gruppo](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Impostazione** | **Valore consigliato** 
    |---|---|
    | Source (Sorgente) | ServiceTag (tag servizio)
    | Tag del servizio di origine | Gestione Di AzureDataExplorerManagement
    | Intervalli di porte di origine | *
    | Destination | VirtualNetwork
    | Intervalli di porte di destinazione | *
    | Protocollo | TCP
    | Azione | Allow
    | Priorità | 100
    | Nome | AllowAzureDataExplorerManagement
    | | |
    
1. Ripetere i due passaggi precedenti per tutte le dipendenze in ingresso e in uscita in base alle dipendenze per la [distribuzione della rete virtuale.](/azure/data-explorer/vnet-deloyment#dependencies-for-vnet-deployment) In alternativa, le regole in uscita possono essere sostituite con una singola regola per consentire *Internet* per le porte 443 e 80.
    
    Le regole del gruppo di sicurezza di gruppo per le dipendenze in ingresso e in uscita dovrebbero essere simili alle:The NSG rules for inbound and outbound dependencies should look like this:

    ![Regole NSG](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Creare indirizzi IP pubblici

Per creare gli indirizzi IP pubblici della query (Motore):

1. Nell'angolo superiore sinistro del portale, selezionare il pulsante **: Crea una risorsa.**
1. Cercare *Il gruppo di sicurezza*di rete .
1. In **Indirizzo IP pubblico**, nella parte inferiore della schermata, selezionare **Crea**.
1. Nel riquadro **Crea indirizzo IP pubblico** completare le informazioni seguenti.
   
   ![Crea modulo IP pubblico](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Versione indirizzo IP | IPv4 | Selezionare la versione IP. Supportiamo solo IPv4.|
    | Sku | Standard | È necessario **Standard** per l'endpoint URI di query (motore). |
    | Nome | motore-pip | Scegliere un nome che identifichi l'indirizzo IP pubblico nel gruppo di risorse.
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure che si vuole usare per l'indirizzo IP pubblico.|
    | Resource group | Gruppo di risorse in uso | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
    | Location | *Stati Uniti occidentali* | Selezionare l'area più appropriata in base alle esigenze.
    | | | |

1. Selezionare **Crea** per creare l'indirizzo IP pubblico.

1. Per creare l'indirizzo IP pubblico di inserimento (Gestione dati) seguire le stesse istruzioni e selezionare 
    * **Sku**: Di base
    * **Assegnazione indirizzo IP**: Statica

## <a name="create-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnetCreate Virtual Network and subnet

Per creare la rete virtuale e la subnet:

1. Nell'angolo superiore sinistro del portale, selezionare il pulsante **: Crea una risorsa.**
1. Cercare *Rete virtuale*.
1. In **Rete virtuale**, nella parte inferiore della schermata, selezionare **Crea**.
1. Nella finestra **Crea rete virtuale** completare le informazioni seguenti.

   ![Crea modulo di rete virtuale](media/vnet-create-cluster-portal/vnet-blade.png)

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per il cluster.|
    | Resource group | Gruppo di risorse in uso | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
    | Nome | AzureDataExplorerVnet | Scegliere un nome che identifichi la rete virtuale nel gruppo di risorse.
    | Region | *Stati Uniti occidentali* | Selezionare l'area più appropriata in base alle esigenze.
    | | | |

    > [!NOTE]
    > Per i carichi di lavoro di produzione, pianificare le dimensioni della subnet in base alle dimensioni della [subnet del piano nella rete virtualeFor production workloads,](/azure/data-explorer/vnet-deloyment#plan-subnet-size-in-your-vnet) plan your subnet size according to plan subnet size in your VNet

1. Selezionare **Rivedi e crea** per rivedere i dettagli del cluster e **Crea** per effettuarne il provisioning.

1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.
1. Passare al pannello **Subnet** e selezionare la subnet **predefinita.**
    
    ![Lama Subnet](media/vnet-create-cluster-portal/subnets.png)

1. Nella finestra della subnet **predefinita:In** your default subnet window:
    1. Selezionare il gruppo di sicurezza di **rete** dal menu a discesa. 
    1. Selezionare il nome del gruppo di sicurezza di rete, in questo caso **AzureDataExplorerNsg**. 
    1. **Salva**

    ![Configurare la subnet](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Creare un cluster

Creare un cluster di Azure Data Explorer con un set definito di risorse di elaborazione e archiviazione in un gruppo di risorse di Azure come descritto in [Creare un cluster.](create-cluster-database-portal.md#create-a-cluster)

1. Prima di finalizzare la creazione del cluster, nella finestra Crea un cluster di Azure Data Explorer selezionare la scheda Rete per fornire i dettagli della rete virtuale usando le risorse create nelle schede precedenti:Before finalizing cluster **creation,** in the Create an Azure Data Explorer cluster window, select the **Network** tab to provide virtual network details using the resources created in the previous tabs:

   ![Crea modulo vnet cluster](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure che si vuole usare per le risorse di rete.|
    | Rete virtuale | AzureDataExplorerVnet | Scegliere la rete virtuale creata nei passaggi precedenti.
    | Subnet | default | Scegliere la subnet creata nei passaggi precedenti.
    | Query IP pubblico | motore-pip | Scegliere l'IP pubblico della query creato nei passaggi precedenti.
    | Inserimento dati IP pubblico | dm-pip | Scegliere l'IP pubblico di inserimento creato nei passaggi precedenti.
    | | | |

1. Selezionare **Revisione e creazione** per creare il cluster.
1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.

Per distribuire il cluster di Azure Data Explorer nella rete virtuale, usare il cluster Deploy Azure Data Explorer nella rete virtuale di Azure Resource Manager.To deploy your Azure Data Explorer cluster into your virtual network, use the [Deploy Azure Data Explorer cluster into your VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure Resource Manager template.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire Esplora dati di Azure nella rete virtualeDeploy Azure Data Explorer into your Virtual Network](vnet-create-cluster-portal.md)