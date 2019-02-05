---
title: Sicurezza di rete in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Informazioni sul funzionamento dell'integrazione della rete virtuale in Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: ed2d1256508e588000970879dae7ac653797fbf9
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883314"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Integrazione della rete virtuale per Azure Data Lake Storage Gen1

Questo articolo presenta l'integrazione della rete virtuale per Azure Data Lake Storage Gen1. Con l'integrazione della rete virtuale, è possibile configurare gli account in modo che accettino solo il traffico proveniente da reti virtuali e subnet specifiche. 

Questa funzionalità consente di proteggere l'account Data Lake Storage da minacce esterne.

L'integrazione della rete virtuale per Data Lake Storage Gen1 usa la sicurezza dell'endpoint del servizio di rete virtuale tra la rete virtuale dell'utente e Azure Active Directory (Azure AD) per generare attestazioni di sicurezza aggiuntive nel token di accesso. Queste attestazioni vengono quindi usate per autenticare la rete virtuale nell'account Data Lake Storage Gen1 e consentire l'accesso.

> [!NOTE]
> L'uso di queste funzionalità non prevede costi aggiuntivi. Per l'account verrà addebitata la tariffa standard per Data Lake Storage Gen1. Per altre informazioni, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable). Per tutti gli altri servizi di Azure in uso, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/#product-picker).

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Scenari di integrazione della rete virtuale per Data Lake Storage Gen1

Con l'integrazione della rete virtuale per Data Lake Storage Gen1, è possibile limitare l'accesso al proprio account Data Lake Storage Gen1 da reti virtuali e subnet specifiche. Dopo che l'account è bloccato a una specifica subnet di rete virtuale, le altre reti virtuali o macchine virtuali in Azure non possono accedervi. A livello funzionale, l'integrazione della rete virtuale per Data Lake Storage Gen1 abilita lo stesso scenario degli [endpoint del servizio di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Alcune differenze importanti sono descritte in dettaglio nelle sezioni successive. 

![Diagramma dello scenario di integrazione della rete virtuale per Data Lake Storage Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> Le regole del firewall per gli indirizzi IP esistenti possono essere usate in aggiunta alle regole della rete virtuale per consentire l'accesso anche dalle reti locali. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Routing ottimale con l'integrazione della rete virtuale per Data Lake Storage Gen1

Un vantaggio importante offerto dagli endpoint del servizio di rete virtuale è il [routing ottimale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits) dalla rete virtuale. È possibile applicare la stessa ottimizzazione delle route agli account Data Lake Storage Gen1. Usare le [route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) seguenti dalla rete virtuale all'account Data Lake Storage Gen1.

**Indirizzo IP pubblico di Data Lake Storage**: usare l'indirizzo IP pubblico per gli account Data Lake Storage Gen1 di destinazione. Per identificare gli indirizzi IP dell'account Data Lake Storage Gen1, [risolvere i nomi DNS](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) degli account in uso. Creare una voce separata per ogni indirizzo.

    ```azurecli
    # Create a route table for your resource group.
    az network route-table create --resource-group $RgName --name $RouteTableName
    
    # Create route table rules for Data Lake Storage public IP addresses.
    # There's one rule per Data Lake Storage public IP address. 
    az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet
    
    # Update the virtual network, and apply the newly created route table to it.
    az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
    ```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>Esfiltrazione dei dati dalla rete virtuale del cliente

Oltre a proteggere gli account Data Lake Storage per l'accesso dalla rete virtuale, potrebbe essere utile assicurarsi che non avvenga l'esfiltrazione in un account non autorizzato.

Usare una soluzione firewall nella rete virtuale per filtrare il traffico in uscita in base all'URL dell'account di destinazione. Consentire l'accesso solo agli account Data Lake Storage Gen1 approvati.

Alcune opzioni disponibili sono:
- [Firewall di Azure](https://docs.microsoft.com/azure/firewall/overview): [distribuire e configurare un'istanza di Firewall di Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) per la rete virtuale. Proteggere il traffico Data Lake Storage in uscita bloccandolo all'URL dell'account noto e approvato.
- Firewall dell'[appliance virtuale di rete](https://azure.microsoft.com/solutions/network-appliances/): l'amministratore potrebbe consentire solo l'uso di alcuni fornitori firewall commerciali. Usare una soluzione firewall di tipo appliance virtuale di rete disponibile in Azure Marketplace per eseguire la stessa funzione.

> [!NOTE]
> L'uso di firewall nel percorso dati introduce un hop aggiuntivo in tale percorso. Può inoltre incidere sulle prestazioni di rete per lo scambio di dati end-to-end. Anche la velocità effettiva disponibile e la latenza della connessione potrebbero risentirne. 

## <a name="limitations"></a>Limitazioni

- I cluster HDInsight creati prima che fosse disponibile il supporto dell'integrazione della rete virtuale per Data Lake Storage Gen1 devono essere ricreati per supportare questa nuova funzionalità.
 
- Quando si crea un nuovo cluster HDInsight e si seleziona un account Data Lake Storage Gen1 con l'integrazione della rete virtuale abilitata, il processo ha esito negativo. Per prima cosa, disabilitare la regola della rete virtuale. In alternativa, nel pannello **Firewall e reti virtuali** dell'account Data Lake Storage selezionare l'opzione per **consentire l'accesso da tutte le reti e i servizi**. Creare quindi il cluster HDInsight prima di riabilitare la regola della rete virtuale o di deselezionare **Consentire l'accesso da tutte le reti e servizi**. Per altre informazioni, vedere la sezione [Eccezioni](##Exceptions).

- L'integrazione della rete virtuale per Data Lake Storage Gen1 non funziona con le [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
  
- I dati di file e cartelle contenuti in un account Data Lake Storage Gen1 abilitato per le reti virtuali non sono accessibili dal portale. Questa limitazione include l'accesso da una macchina virtuale che si trova all'interno della rete virtuale e le attività come l'uso di Esplora dati. Le attività di gestione dell'account continuano a funzionare. I dati di file e cartelle contenuti in un account Data Lake Storage abilitato per le reti virtuali sono accessibili tramite tutte le risorse diverse dal portale. Queste risorse includono SDK, script di PowerShell e altri servizi di Azure, se non originati dal portale. 

## <a name="configuration"></a>Configurazione

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>Passaggio 1: Configurare la rete virtuale per l'uso dell'endpoint di servizio Azure AD

1.  Passare al portale di Azure ed eseguire l'accesso all'account.
 
2.  [Creare una nuova rete virtuale ](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)nella sottoscrizione. In alternativa, è possibile accedere a una rete virtuale esistente. La rete virtuale deve trovarsi nella stessa area dell'account Data Lake Storage Gen1.
 
3.  Nel pannello **Rete virtuale** selezionare **Endpoint servizio**.
 
4.  Selezionare **Aggiungi** per aggiungere un nuovo endpoint di servizio.

    ![Aggiungere un endpoint del servizio di rete virtuale](media/data-lake-store-network-security/config-vnet-1.png)

5.  Selezionare **Microsoft.AzureActiveDirectory** come servizio per l'endpoint.

     ![Selezionare l'endpoint di servizio Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)

6.  Selezionare le subnet per cui si vuole consentire la connettività. Selezionare **Aggiungi**.

    ![Selezionare la subnet](media/data-lake-store-network-security/config-vnet-3.png)

7.  Per l'aggiunta dell'endpoint di servizio possono essere necessari fino a 15 minuti. Dopo essere stato aggiunto, viene visualizzato nell'elenco. Verificare che compaia e che tutti i dettagli corrispondano alla configurazione.
 
    ![Aggiunta corretta dell'endpoint di servizio](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>Passaggio 2: Configurare la rete virtuale o la subnet consentita per l'account Data Lake Storage Gen1

1.  Dopo aver configurato la rete virtuale, [creare un nuovo account Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) nella sottoscrizione. In alternativa, è possibile passare a un account Data Lake Storage Gen1 esistente. L'account Data Lake Storage Gen1 deve trovarsi nella stessa area della rete virtuale.
 
2.  Selezionare **Firewall e reti virtuali**.

    > [!NOTE]
    > Se **Firewall e reti virtuali** non compare nelle impostazioni, disconnettersi dal portale. Chiudere il browser e cancellare la cache del browser. Riavviare il computer e riprovare.

       ![Aggiungere una regola della rete virtuale per l'account Data Lake Storage](media/data-lake-store-network-security/config-adls-1.png)

3.  Selezionare **Reti selezionate**.
 
4.  Selezionare **Aggiungi rete virtuale esistente**.

    ![Aggiungi rete virtuale esistente](media/data-lake-store-network-security/config-adls-2.png)

5.  Selezionare le reti virtuali e le subnet per cui consentire la connettività. Selezionare **Aggiungi**.

    ![Scegliere la rete virtuale e le subnet](media/data-lake-store-network-security/config-adls-3.png)

6.  Assicurarsi che le reti virtuali e le subnet compaiano correttamente nell'elenco. Selezionare **Salva**.

    ![Salvare la nuova regola](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > Dopo il salvataggio, potrebbero essere necessari fino a 5 minuti prima che le impostazioni diventino effettive.

7.  [Facoltativo] Nella sezione **Firewall** della pagina **Firewall e reti virtuali** è possibile consentire la connettività da indirizzi IP specifici. 

## <a name="exceptions"></a>Eccezioni
È possibile abilitare la connettività da macchine virtuali e servizi di Azure all'esterno delle reti virtuali selezionate. Nell'area **Eccezioni** del pannello **Firewall e reti virtuali** selezionare una delle due opzioni seguenti:
 
- **Consente a tutti i servizi di Azure di accedere a questo account Data Lake Storage Gen1**. Questa opzione consente ai servizi di Azure come Azure Data Factory, Hub eventi di Azure e tutte le macchine virtuali di Azure di comunicare con l'account Data Lake Storage.

- **Consente ad Azure Data Lake Analytics di accedere a questo account Data Lake Storage Gen1**. Questa opzione consente la connettività di Data Lake Analytics a questo account Data Lake Storage. 

  ![Eccezioni di Firewall e reti virtuali](media/data-lake-store-network-security/firewall-exceptions.png)

È consigliabile mantenere queste eccezioni disattivate. Attivarle solo se è necessaria la connettività da questi altri servizi dall'esterno della rete virtuale.
