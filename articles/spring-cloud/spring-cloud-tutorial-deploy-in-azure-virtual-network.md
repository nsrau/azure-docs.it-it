---
title: 'Esercitazione: Distribuire Azure Spring Cloud nella rete virtuale'
description: Distribuire Azure Spring Cloud nella rete virtuale (v-net injection).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: a7905ae0fdbd797d9b544cb71f44b82af1295246
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688454"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Esercitazione: Distribuire Azure Spring Cloud nella rete virtuale (VNet injection)

Questa esercitazione illustra come distribuire un'istanza del servizio Azure Spring Cloud nella rete virtuale, una funzionalità anche nota come VNet injection.  

La distribuzione rende possibili gli scenari seguenti:

* Isolamento delle app di Azure Spring Cloud e del runtime del servizio da Internet nella rete aziendale
* Interazione di Azure Spring Cloud con i sistemi nei data center locali e/o con i servizi di Azure in altre reti virtuali
* Possibilità per i clienti di controllare le comunicazioni di rete in ingresso e in uscita per Azure Spring Cloud

## <a name="prerequisites"></a>Prerequisiti
È necessario registrare il provider di risorse cloud di Azure Spring Cloud `Microsoft.AppPlatform` seguendo le istruzioni riportate in [Registrare il provider di risorse nel portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) oppure eseguendo il comando az seguente dell'interfaccia della riga di comando:

```azurecli
az provider register --namespace Microsoft.AppPlatform
```
## <a name="virtual-network-requirements"></a>Requisiti della rete virtuale
La rete virtuale in cui viene distribuita l'istanza del servizio Azure Spring Cloud deve soddisfare i requisiti seguenti:

* **Località**: la rete virtuale deve risiedere nella stessa località dell'istanza del servizio Azure Spring Cloud.
* **Sottoscrizione** la rete virtuale deve essere inclusa nella stessa sottoscrizione dell'istanza del servizio Azure Spring Cloud.
* **Subnet**: la rete virtuale deve includere due subnet dedicate a un'istanza del servizio Azure Spring Cloud: 
    * Una per il runtime del servizio
    * Una per le applicazioni di microservizi Spring Boot. 
    * Deve esistere una relazione uno-a-uno tra queste subnet e un'istanza del servizio Azure Spring Cloud. Non è possibile condividere più istanze del servizio in una singola subnet. È necessario usare nuove subnet per ogni istanza del servizio distribuita.
* **Spazio degli indirizzi**: un blocco CIDR fino a /28 per la subnet del runtime del servizio e un altro blocco CIDR fino a /24 per la subnet delle applicazioni di microservizi Spring Boot.
* **Tabella di route**: alle subnet non deve essere associata una tabella di route esistente.

Le procedure seguenti descrivono la configurazione della rete virtuale in cui contenere l'istanza di Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Creare una rete virtuale
Se è già disponibile una rete virtuale in cui ospitare l'istanza del servizio Azure Spring Cloud, ignorare i passaggi 1, 2 e 3. È possibile iniziare dal passaggio 4 per preparare le subnet per la rete virtuale.

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. In Azure Marketplace selezionare **Rete** > **Rete virtuale**.

1. Nella finestra di dialogo **Crea rete virtuale** immettere o selezionare le informazioni seguenti:

    |Impostazione          |valore                                             |
    |-----------------|--------------------------------------------------|
    |Subscription     |Selezionare la propria sottoscrizione.                         |
    |Resource group   |Selezionare il gruppo di risorse o crearne uno nuovo.  |
    |Name             |Immettere *azure-spring-cloud-vnet*                   |
    |Location         |Selezionare **Stati Uniti orientali**.                                |

1. Fare clic su **Avanti: Indirizzi IP >** . 
 
1. Per Spazio indirizzi IPv4, digitare 10.1.0.0/16.

1. Selezionare **Aggiungi subnet**, quindi immettere *service-runtime-subnet* per **Nome subnet** e 10.1.0.0/24 per **Intervallo di indirizzi della subnet**. Fare quindi clic su **Aggiungi**.

1. Selezionare **Aggiungi subnet**, quindi immettere *apps-subnet* per **Nome subnet** e 10.1.1.0/24 per **Intervallo di indirizzi della subnet**.  Scegliere **Aggiungi**.

1. Fare clic su **Rivedi e crea**. Lasciare tutte le altre impostazioni predefinite e fare clic su **Crea**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Concedere al servizio l'autorizzazione per la rete virtuale

Selezionare la rete virtuale *azure-spring-cloud-vnet* creata in precedenza.

1. Selezionare **Controllo di accesso (IAM)** e quindi **Aggiungi > Aggiungi un'assegnazione di ruolo**.

    ![Controllo di accesso per la rete virtuale](./media/spring-cloud-v-net-injection/access-control.png)

2. Nella finestra di dialogo **Aggiungi un'assegnazione di ruolo** immettere o selezionare queste informazioni:

    |Impostazione  |valore                                             |
    |---------|--------------------------------------------------|
    |Ruolo     |Selezionare **Proprietario**                                  |
    |Select   |Immettere *Provider di risorse Azure Spring Cloud*      |

    Selezionare quindi *Provider di risorse Azure Spring Cloud* e fare clic su **Salva**.

    ![Concedere al provider di risorse Azure Spring Cloud l'accesso alla rete virtuale](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

È anche possibile ottenere questo risultato eseguendo il comando az seguente dell'interfaccia della riga di comando

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>Distribuire l'istanza del servizio Azure Spring Cloud nella rete virtuale

1. Aprire il portale di Azure all'indirizzo https://ms.portal.azure.com.

1. Nella casella di ricerca superiore immettere **Azure Spring Cloud** e selezionare **Azure Spring Cloud** nei risultati.

1. Nella pagina **Azure Spring Cloud** fare clic su **+ Aggiungi**.

1. Compilare il modulo nella pagina di **creazione** di Azure Spring Cloud. 

1. Selezionare lo stesso gruppo di risorse e la stessa area della rete virtuale.

1. Per **Nome** in **Dettagli servizio** selezionare *azure-spring-cloud-vnet*.

1. Selezionare la scheda **Rete** e quindi le opzioni seguenti:

    |Impostazione                                |valore                                             |
    |---------------------------------------|--------------------------------------------------|
    |Distribuisci nella rete virtuale personale     |Selezionare **Sì**                                    |
    |Rete virtuale                        |Selezionare *azure-spring-cloud-vnet*                  |
    |Subnet del runtime servizio                 |Selezionare *service-runtime-subnet*                   |
    |Subnet delle app per microservizi Spring Boot   |Selezionare *app-subnet*                              |

    ![Scheda per la creazione della rete](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Fare clic su **Rivedi e crea**.

1. Verificare le specifiche, quindi fare clic su **rea**.

Dopo la distribuzione, nella sottoscrizione verranno creati due gruppi di risorse aggiuntivi in cui ospitare le risorse di rete per l'istanza del servizio Azure Spring Cloud.  Passare a **Home**, quindi selezionare **Gruppi di risorse** tra le voci del menu superiore per trovare i nuovi gruppi di risorse seguenti.

Il gruppo di risorse denominato *azure-spring-cloud-service-runtime_{nome istanza servizio}_{area istanza servizio}* contiene le risorse di rete per il runtime del servizio dell'istanza del servizio.

  ![Runtime del servizio](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Il gruppo di risorse denominato *azure-spring-cloud-service-runtime_{nome istanza servizio}_{area istanza servizio}* contiene le risorse di rete per le applicazioni di microservizi Spring Boot dell'istanza del servizio.

  ![Gruppo di risorse di app](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Queste risorse di rete sono connesse alla rete virtuale creata in precedenza.

  ![Rete virtuale con il dispositivo connesso](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > I gruppi di risorse sono completamente gestiti dal servizio Azure Spring Cloud. NON eliminare o modificare manualmente le risorse al loro interno.

## <a name="next-steps"></a>Passaggi successivi

[Distribuire l'applicazione in Azure Spring Cloud nella rete virtuale](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Vedere anche

- [Risoluzione dei problemi di Azure Spring Cloud nella rete virtuale](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Responsabilità del cliente per l'esecuzione di Azure Spring Cloud nella rete virtuale](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)