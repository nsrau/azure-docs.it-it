---
title: Creare un cluster di Azure Esplora dati & database nella rete virtuale
description: Questo articolo illustra come creare un cluster di Azure Esplora dati nella rete virtuale.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 99c32122fc48aaea7428fa559d7289713849f34e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548891"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Creare un cluster di Azure Esplora dati nella rete virtuale

Azure Esplora dati supporta la distribuzione di un cluster in una subnet nella rete virtuale (VNet). Questa funzionalità consente di accedere al cluster privatamente dalla rete virtuale di Azure o in locale, accedere a risorse quali hub eventi e archiviazione all'interno della rete virtuale e limitare il traffico in ingresso e in uscita.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-network-security-group-nsg"></a>Crea gruppo di sicurezza di rete (NSG)

I [gruppi di sicurezza di rete (NSG)](/azure/virtual-network/security-overview) offrono la possibilità di controllare l'accesso alla rete all'interno di una VNet. È possibile accedere ad Azure Esplora dati usando due endpoint HTTPs (443) e TDS (1433). Per consentire l'accesso a questi endpoint per la gestione, il monitoraggio e il corretto funzionamento del cluster, è necessario configurare le regole di NSG seguenti.

Per creare il gruppo di sicurezza di rete:

1. Selezionare il pulsante **+ Crea una risorsa** nell'angolo superiore sinistro del portale.
1. Cercare il *gruppo di sicurezza di rete*.
1. In **gruppo di sicurezza di rete**fare clic su **Crea**nella parte inferiore della schermata.
1. Nella finestra **Crea gruppo di sicurezza di rete** inserire le informazioni seguenti.

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

1. Nella scheda **regole di sicurezza in ingresso** selezionare **+ Aggiungi**.
1. Nella finestra **Aggiungi regola di sicurezza in ingresso** immettere le informazioni seguenti.

    ![Crea il form della regola in ingresso NSG](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Impostazione** | **Valore consigliato** 
    |---|---|
    | Origine | ServiceTag
    | Tag del servizio di origine | AzureDataExplorerManagement
    | Intervalli di porte di origine | *
    | Destination | VirtualNetwork
    | Intervalli di porte di destinazione | *
    | Protocollo | TCP
    | Azione | Allow
    | Priorità | 100
    | Nome | AllowAzureDataExplorerManagement
    | | |
    
1. Ripetere i due passaggi precedenti per tutte le dipendenze in ingresso e in uscita in base alle [dipendenze per la distribuzione di VNet](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment). In alternativa, le regole in uscita possono essere sostituite con una singola regola per consentire *Internet* per le porte 443 e 80.
    
    Le regole NSG per le dipendenze in ingresso e in uscita dovrebbero essere simili alle seguenti:

    ![Regole NSG](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Creare indirizzi IP pubblici

Per creare gli indirizzi IP pubblici della query (motore):

1. Selezionare il pulsante **+ Crea una risorsa** nell'angolo superiore sinistro del portale.
1. Cercare il *gruppo di sicurezza di rete*.
1. In **indirizzo IP pubblico**fare clic su **Crea**nella parte inferiore della schermata.
1. Nel riquadro **Crea indirizzo IP pubblico** completare le informazioni riportate di seguito.
   
   ![Crea modulo IP pubblico](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Versione indirizzo IP | IPv4 | Selezionare la versione dell'indirizzo IP. È supportato solo IPv4.|
    | Sku | Standard | È necessario l'endpoint URI **standard** for query (motore). |
    | Nome | motore-PIP | Scegliere un nome che identifichi l'indirizzo IP pubblico nel gruppo di risorse.
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure che si vuole usare per l'indirizzo IP pubblico.|
    | Resource group | Gruppo di risorse in uso | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
    | Location | *Stati Uniti occidentali* | Selezionare l'area più appropriata in base alle esigenze.
    | | | |

1. Selezionare **Crea** per creare l'indirizzo IP pubblico.

1. Per creare l'indirizzo IP pubblico di inserimento (Gestione dati), seguire le stesse istruzioni e selezionare 
    * **SKU**: di base
    * **Assegnazione di indirizzi IP**: statica

## <a name="create-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnet

Per creare la rete virtuale e la subnet:

1. Selezionare il pulsante **+ Crea una risorsa** nell'angolo superiore sinistro del portale.
1. Cercare *rete virtuale*.
1. In **rete virtuale**fare clic su **Crea**nella parte inferiore della schermata.
1. Nella finestra **Crea rete virtuale** completare le informazioni riportate di seguito.

   ![Crea modulo rete virtuale](media/vnet-create-cluster-portal/vnet-blade.png)

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per il cluster.|
    | Resource group | Gruppo di risorse in uso | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
    | Nome | AzureDataExplorerVnet | Scegliere un nome che identifichi la rete virtuale nel gruppo di risorse.
    | Region | *Stati Uniti occidentali* | Selezionare l'area più appropriata in base alle esigenze.
    | | | |

    > [!NOTE]
    > Per i carichi di lavoro di produzione, pianificare le dimensioni [della subnet in base alle dimensioni della subnet del piano nella VNet](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet)

1. Selezionare **Rivedi e crea** per rivedere i dettagli del cluster e **Crea** per effettuarne il provisioning.

1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.
1. Passare al pannello **subnet** e selezionare la subnet **predefinita** .
    
    ![Pannello subnet](media/vnet-create-cluster-portal/subnets.png)

1. Nella finestra subnet **predefinita** :
    1. Selezionare il **gruppo di sicurezza di rete** dal menu a discesa. 
    1. Selezionare il nome del gruppo di sicurezza di rete, in questo caso **AzureDataExplorerNsg**. 
    1. **Salva**

    ![Configura subnet](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Creare un cluster

Creare un cluster di Esplora dati di Azure con un set definito di risorse di calcolo e di archiviazione in un gruppo di risorse di Azure, come descritto in [creare un cluster](create-cluster-database-portal.md#create-a-cluster).

1. Prima di completare la creazione del cluster, nella finestra **Crea un cluster di Azure Esplora dati** selezionare la scheda **rete** per specificare i dettagli della rete virtuale usando le risorse create nelle schede precedenti:

   ![Creazione del modulo VNET del cluster](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure che si vuole usare per le risorse di rete.|
    | Rete virtuale | AzureDataExplorerVnet | Scegliere la rete virtuale creata nei passaggi precedenti.
    | Subnet | default | Scegliere la subnet creata nei passaggi precedenti.
    | Eseguire query su IP pubblico | motore-PIP | Scegliere la query IP pubblico creata nei passaggi precedenti.
    | IP pubblico di inserimento dati | DM-PIP | Scegliere l'indirizzo IP pubblico di inserimento creato nei passaggi precedenti.
    | | | |

1. Selezionare **Verifica + crea** per creare il cluster.
1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.

Per distribuire il cluster di Esplora dati di Azure nella rete virtuale, usare il [cluster di distribuzione di azure Esplora dati nel](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) modello di Azure Resource Manager di VNet.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire Esplora dati di Azure nella rete virtuale](vnet-create-cluster-portal.md)