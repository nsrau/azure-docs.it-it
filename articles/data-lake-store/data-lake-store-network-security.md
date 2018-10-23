---
title: Sicurezza di rete in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Informazioni sul funzionamento del firewall IP e del servizio di integrazione della rete virtuale in Azure Data Lake Storage Gen1
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
ms.openlocfilehash: 0da5962bc0b48a387ee82a1db36099682e14bca3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49168123"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1---preview"></a>Integrazione della rete virtuale per Azure Data Lake Storage Gen1 - Anteprima

Presentazione dell'integrazione della rete virtuale per Azure Data Lake Storage Gen1 (in anteprima). Il servizio di integrazione della rete virtuale consente di impedire gli accessi non autorizzati agli account Azure Data Lake Storage Gen1 bloccandoli alle proprie specifiche reti virtuali e subnet. Ora è possibile configurare l'account ADLS Gen1 in modo che accetti solo il traffico proveniente dalle reti virtuali e dalle subnet designate e bloccare l'accesso da qualsiasi altra posizione. Questo contribuisce a proteggere l'account ADLS da minacce esterne.

L'integrazione della rete virtuale per ADLS Gen1 usa la sicurezza endpoint del servizio di rete virtuale tra la rete virtuale dell'utente e il servizio Azure Active Directory per generare attestazioni di sicurezza aggiuntive nel token di accesso. Queste attestazioni vengono quindi usate per autenticare la rete virtuale nell'account ADLS Gen1 e consentire l'accesso.

> [!NOTE]
> Questa tecnologia è in anteprima e l'uso in ambiente di produzione non è consigliabile.
>
> L'uso di queste funzionalità non prevede costi aggiuntivi. All'account verrà addebitata la tariffa standard per ADLS Gen1 ([prezzi](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)) e tutti i servizi di Azure in uso ([prezzi](https://azure.microsoft.com/pricing/#product-picker)).

## <a name="scenarios-for-vnet-integration-for-adls-gen1"></a>Scenari di integrazione rete virtuale per ADLS Gen1

L'integrazione della rete virtuale per ADLS Gen1 consente di limitare l'accesso al proprio account ADLS Gen1 da subnet e reti virtuali designate.  Una volta bloccato l'account a una specifica subnet di rete virtuale, le altre reti virtuali o macchine virtuali in Azure non potranno accedervi.  A livello funzionale, l'integrazione della rete virtuale per ADLS Gen1 abilita lo stesso scenario degli [endpoint del servizio Rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview).  Esistono alcune differenze importanti, descritte in dettaglio nelle sezioni successive. 

![Diagramma dello scenario per l'integrazione della rete virtuale di ADLS Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> Le regole del firewall per gli indirizzi IP esistenti possono essere usate in aggiunta alle regole della rete virtuale per consentire l'accesso anche dalle reti locali. 

## <a name="optimal-routing-with-adls-gen1-vnet-integration"></a>Routing ottimale con l'integrazione della rete virtuale per ADLS Gen1

Un vantaggio importante offerto dagli endpoint del servizio Rete virtuale è il [routing ottimale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits) dalla rete virtuale.  Per applicare la stessa ottimizzazione delle route agli account ADLS Gen1, usare le [route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) seguenti dalla rete virtuale all'account Azure Data Lake Store Gen1:

- **Indirizzo IP pubblico di ADLS**: usare l'indirizzo IP pubblico per gli account ADLS Gen1 di destinazione.  Gli indirizzi IP degli account ADLS Gen1 sono identificabili [risolvendo i nomi DNS](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) degli account in uso.  Creare una voce separata per ogni indirizzo.

```azurecli
# Create a Route table for your resource group
az network route-table create --resource-group $RgName --name $RouteTableName

# Create Route Table Rules for ADLS Public IP Addresses
# There will be one rule per ADLS Public IP Addresses 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the VNet and apply the newly created Route Table to it
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-vnet"></a>Esfiltrazione dei dati dalla rete virtuale del cliente

Oltre a proteggere gli account ADLS per l'accesso da Rete virtuale, può essere utile assicurarsi che non avvenga l'esfiltrazione in un account non autorizzato.

È consigliabile usare una soluzione firewall nella rete virtuale per filtrare il traffico in uscita in base all'URL dell'account di destinazione e consentire l'accesso solo agli account ADLS Gen1 autorizzati.

Alcune opzioni disponibili sono:
- [Firewall di Azure](https://docs.microsoft.com/azure/firewall/overview): è possibile [distribuire e configurare un'istanza di Firewall di Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) per la rete virtuale e proteggere il traffico ADLS in uscita bloccandolo all'URL dell'account noto e autorizzato.
- [Appliance virtuale di rete](https://azure.microsoft.com/solutions/network-appliances/) firewall: se l'amministratore autorizza solo l'uso di alcuni fornitori di firewall commerciali, è possibile usare una soluzione firewall di tipo appliance virtuale di rete disponibile in Azure Marketplace per eseguire la stessa funzione.

> [!NOTE]
> L'uso di firewall nel percorso dati introdurrà un hop aggiuntivo in tale percorso e può incidere sulle prestazioni di rete per lo scambio di dati end-to-end, incluse la velocità effettiva disponibile e la latenza della connessione. 

## <a name="limitations"></a>Limitazioni
1.  I cluster HDInsight devono essere creati di nuovo dopo l'aggiunta alla versione di anteprima.  Per supportare questa nuova funzionalità, è necessario ricreare i cluster creati prima che fosse disponibile il supporto dell'integrazione della rete virtuale per ADLS Gen1. 
2.  Quando si crea un nuovo cluster HDInsight, la selezione di un account ADLS Gen1 con integrazione della rete virtuale abilitata causerà l'esito negativo del processo. È prima necessario disabilitare la regola della rete virtuale oppure è possibile **consentire l'accesso da tutte le reti e i servizi** tramite il pannello **Firewall e reti virtuali** dell'account ADLS.  Vedere la sezione [Eccezioni](##Exceptions) per altre informazioni.
3.  L'anteprima del servizio di integrazione della rete virtuale per ADLS Gen1 non funziona con le [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
4.  I dati di file e cartelle contenuti in un account ADLS Gen1 abilitato per le reti virtuali non sono accessibili dal portale.  Questo include l'accesso da una macchina virtuale che si trova all'interno della rete virtuale e le attività come l'uso di Esplora dati.  Le attività di gestione dell'account continuano a funzionare.  I dati di file e cartelle contenuti in un account ADLS abilitato per le reti virtuali sono accessibili tramite tutte le risorse diverse dal portale: SDK, script di PowerShell, altri servizi di Azure (se non originati dal portale) e così via. 

## <a name="configuration"></a>Configurazione

### <a name="step1-configure-your-vnet-to-use-aad-service-endpoint"></a>Passaggio 1: Configurare la rete virtuale per l'uso dell'endpoint di servizio AAD
1.  Passare al portale di Azure ed eseguire l'accesso all'account. 
2.  [Creare una nuova rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) nella sottoscrizione o passare a una rete virtuale esistente.  La rete virtuale deve trovarsi nella stessa area dell'account ADLS Gen1. 
3.  Nel pannello Rete virtuale selezionare **Endpoint servizio**. 
4.  Fare clic su **Aggiungi** per aggiungere un nuovo endpoint di servizio.
![Aggiunta di un endpoint di servizio di rete virtuale](media/data-lake-store-network-security/config-vnet-1.png)
5.  Scegliere **Microsoft.AzureActiveDirectory** come servizio per l'endpoint.
![Selezione dell'endpoint di servizio Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)
6.  Scegliere le subnet per cui si vuole consentire la connettività, fare clic su **Aggiungi**.
![Selezionare la subnet](media/data-lake-store-network-security/config-vnet-3.png)
7.  Per l'aggiunta dell'endpoint di servizio possono essere necessari fino a 15 minuti. Una volta aggiunto, verrà visualizzato nell'elenco. Verificare che compaia e che tutti i dettagli corrispondano alla configurazione. 
![Aggiunta corretta dell'endpoint di servizio](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-vnetsubnet-for-your-adls-gen1-account"></a>Passaggio 2: Configurare la rete virtuale/subnet consentita per l'account ADLS Gen1
1.  Dopo aver configurato la rete virtuale, [creare un nuovo account Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) nella sottoscrizione o passare a un account ADLS Gen1 esistente. L'account ADLS Gen1 deve trovarsi nella stessa area della rete virtuale. 
2.  Scegliere **Firewall e reti virtuali**.

  > [!NOTE]
  > Se **Firewall e reti virtuali** non compare nelle impostazioni, disconnettersi del portale. Chiudere il browser. Cancellare la cache del browser. Riavviare il computer e riprovare.

  ![Aggiunta di una regola della rete virtuale all'account ADLS](media/data-lake-store-network-security/config-adls-1.png)
3.  Scegliere **Reti selezionate**. 
4.  Fare clic su **Aggiungi rete virtuale esistente**.
  ![Aggiunta di una rete virtuale esistente](media/data-lake-store-network-security/config-adls-2.png)
5.  Scegliere le reti virtuali e le subnet per cui consentire la connettività, quindi fare clic su **Aggiungi**.
  ![Scelta di rete virtuale e subnet](media/data-lake-store-network-security/config-adls-3.png)
6.  Assicurarsi che le reti virtuali e le subnet compaiano correttamente nell'elenco e **salvare**.
  ![Salvataggio della nuova regola](media/data-lake-store-network-security/config-adls-4.png)

  > [!NOTE]
  > Dopo il salvataggio, possono essere necessari fino a 5 minuti prima che le impostazioni diventino effettive.

7.  [Facoltativo] Oltre che da reti virtuali e subnet, si può consentire la connettività da indirizzi IP specifici nella sezione **Firewall** nella stessa pagina. 

## <a name="exceptions"></a>Eccezioni
Nell'area Eccezioni del pannello **Firewall e reti virtuali** sono presenti due caselle di controllo che consentono la connettività da un set di servizi e macchine virtuali in Azure.
![Eccezioni di Firewall e reti virtuali](media/data-lake-store-network-security/firewall-exceptions.png)
- **Allow all Azure services to access this Data Lake Storage Gen1 account** (Consenti a tutti i servizi di Azure di accedere a questo account Data Lake Storage Gen1) consente a tutti i servizi di Azure, come Azure Data Factory, Hub eventi, tutte le macchine virtuali di Azure e così via, di comunicare con l'account ADLS.

- **Allow Azure Data Lake Analytics to access this Data Lake Storage Gen1 account** (Consenti ad Azure Data Lake Analytics di accedere a questo account Data Lake Storage Gen1) consente la connettività del servizio Azure Data Lake Analytics con questo account ADLS. 

È consigliabile mantenere queste eccezioni disattivate e attivarle solo se è necessaria la connettività da questi altri servizi dall'esterno della rete virtuale.
