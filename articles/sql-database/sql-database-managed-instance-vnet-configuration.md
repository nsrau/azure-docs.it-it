---
title: Configurazione di una rete virtuale con Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo argomento descrive le opzioni di configurazione di una rete virtuale con Istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 9d3f867dad40017e8e97ec4f5e370533b018263c
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181173"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Configurare una rete virtuale per Istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. Questa distribuzione consente di: 
- Stabilire una connessione a un'istanza gestita direttamente da una rete locale 
- Connettere un'istanza gestita a un server collegato o a un altro archivio dati locale 
- Connettere un'istanza gestita a risorse di Azure  

## <a name="plan"></a>Pianificazione

Pianificare la modalità di distribuzione di un'istanza gestita in una rete virtuale rispondendo alle domande seguenti: 
- Si prevede di distribuire una sola o più istanze gestite? 

  Il numero di istanze gestite determina le dimensioni minime della subnet da allocare per le istanze gestite. Per altre informazioni, vedere [Determinare le dimensioni della subnet per le istanze gestite](#determine-the-size-of-subnet-for-managed-instances). 
- Occorre distribuire l'istanza gestita in una rete virtuale esistente o si sta creando una nuova rete? 

   Se si prevede di usare una rete virtuale esistente, è necessario modificare la configurazione di rete in base all'istanza gestita. Per altre informazioni, vedere la sezione [Modificare la rete virtuale esistente per l'istanza gestita](#modify-an-existing-virtual-network-for-managed-instances). 

   Se si prevede di creare una nuova rete virtuale, vedere [Creare una nuova rete virtuale per l'istanza gestita](#create-a-new-virtual-network-for-a-managed-instance).

## <a name="requirements"></a>Requisiti

Per creare un'istanza gestita, creare all'interno della rete virtuale una subnet dedicata (subnet dell'istanza gestita) conforme ai requisiti seguenti:
- **Subnet dedicata**: la subnet dell'istanza gestita non deve contenere altri servizi cloud ad essa associati e non deve essere una subnet del gateway. Non sarà possibile creare un'istanza gestita in una subnet che contiene altre risorse oltre all'istanza gestita o aggiungere altre risorse nella subnet in un secondo momento.
- **Gruppo di sicurezza di rete compatibile**: un gruppo di sicurezza di rete associato a una subnet dell'istanza gestita può contenere le regole indicate nelle tabelle seguenti (Regole di sicurezza in ingresso obbligatorie e Regole di sicurezza in uscita obbligatorie) prima di qualsiasi altra regola. È possibile usare un gruppo di sicurezza di rete per controllare completamente l'accesso all'endpoint dati dell'istanza gestita filtrando il traffico sulla porta 1433. 
- **Tabella di route definita dall'utente compatibile**: la subnet dell'istanza gestita deve disporre di una tabella di route utente con **0.0.0.0/0 hop successivo su Internet** come route definita dall'utente obbligatoria assegnata. È inoltre possibile aggiungere una route definita dall'utente che indirizza il traffico con intervalli IP privati locali come destinazione tramite un gateway di rete virtuale o un'appliance di rete virtuale. 
- **DNS personalizzato facoltativo**: se per la rete virtuale sono specificate impostazioni DNS personalizzate, è necessario aggiungere all'elenco l'indirizzo IP del resolver ricorsivo di Azure (ad esempio, 168.63.129.16). Per altre informazioni, vedere [Configuring Custom DNS](sql-database-managed-instance-custom-dns.md) (Configurazione del DNS personalizzato). Il server DNS personalizzato deve essere in grado di risolvere i nomi host nei domini seguenti e nei relativi sottodomini: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com* e *microsoftonline-p.com*. 
- **Nessun endpoint di servizio**: alla subnet dell'istanza gestita non deve essere associato alcun endpoint di servizio. Verificare che l'opzione Endpoint di servizio sia disabilitata durante la creazione della rete virtuale.
- **Indirizzi IP sufficienti**: la subnet dell'istanza gestita deve avere almeno di 16 indirizzi IP (il numero minimo consigliato è 32 indirizzi IP). Per altre informazioni, vedere [Determinare le dimensioni della subnet per le istanze gestite](#determine-the-size-of-subnet-for-managed-instances).

> [!IMPORTANT]
> Se la subnet di destinazione non è compatibile con tutti i requisiti precedenti, non sarà possibile distribuire una nuova istanza gestita. Quando viene creata un'istanza gestita, un *criterio relativo alle finalità di rete* viene applicato nella subnet per impedire modifiche non conformi alla configurazione di rete. Dopo la rimozione dell'ultima istanza dalla subnet, viene rimosso anche il *criterio relativo alle finalità di rete*.

### <a name="mandatory-inbound-security-rules"></a>Regole di sicurezza in ingresso obbligatorie 

| NOME       |PORTA                        |PROTOCOLLO|ORIGINE           |DESTINAZIONE|AZIONE|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |Qualsiasi              |Qualsiasi        |CONSENTI |
|mi_subnet   |Qualsiasi                         |Qualsiasi     |MI SUBNET        |Qualsiasi        |CONSENTI |
|health_probe|Qualsiasi                         |Qualsiasi     |AzureLoadBalancer|Qualsiasi        |CONSENTI |

### <a name="mandatory-outbound-security-rules"></a>Regole di sicurezza in uscita obbligatorie 

| NOME       |PORTA          |PROTOCOLLO|ORIGINE           |DESTINAZIONE|AZIONE|
|------------|--------------|--------|-----------------|-----------|------|
|management  |80, 443, 12000|TCP     |Qualsiasi              |Qualsiasi        |CONSENTI |
|mi_subnet   |Qualsiasi           |Qualsiasi     |Qualsiasi              |MI SUBNET  |CONSENTI |

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Determinare le dimensioni della subnet per le istanze gestite

Quando si crea un'istanza gestita, Azure alloca un determinato numero di macchine virtuali in base al livello selezionato durante il provisioning. Poiché queste macchine virtuali sono associate alla subnet, richiedono indirizzi IP. Per assicurare una disponibilità elevata durante le normali operazioni e la manutenzione del servizio, Azure può allocare ulteriori macchine virtuali. Di conseguenza, il numero di indirizzi IP necessari in una subnet è maggiore del numero di istanze gestite nella subnet stessa. 

Come da progettazione, un'istanza gestita necessita di un minimo di 16 indirizzi IP in una subnet e può usare fino a 256 indirizzi IP. Di conseguenza, è possibile usare le subnet mask da /28 a /24 per definire gli intervalli IP della subnet. 

> [!IMPORTANT]
> 16 indirizzi IP sono le dimensioni minime necessarie per la subnet, con un potenziale limitato per l'ulteriore aumento del numero di istanze di Istanza gestita. È consigliabile scegliere una subnet con un prefisso /27 o inferiore. 

Se si prevede di distribuire più istanze gestite all'interno della subnet ed è necessario ottimizzare le dimensioni della subnet, usare questi parametri per formulare un calcolo: 

- Cinque indirizzi IP nella subnet sono dedicati ad Azure 
- Ogni istanza per utilizzo generico necessita di due indirizzi 
- Ogni istanza business critical richiede quattro indirizzi

**Esempio**: si prevede di avere tre istanze gestite di utilizzo generico e due di business critical. Questo significa che sono necessari 5 + 3 * 2 + 2 * 4 = 19 indirizzi IP. Poiché gli intervalli IP sono definiti in potenza di 2, è necessario l'intervallo IP di 32 (2^5) indirizzi IP. Pertanto è necessario riservare la subnet con subnet mask /27. 

> [!IMPORTANT]
> Il calcolo visualizzato in precedenza diventa obsoleto con altri miglioramenti. 

## <a name="create-a-new-virtual-network-for-a-managed-instance"></a>Creare una nuova rete virtuale per un'istanza gestita

Il modo più semplice per creare e configurare una rete virtuale è usare il modello di distribuzione Azure Resource Manager.

1. Accedere al portale di Azure.

2. Usare **Distribuisci in Azure** per distribuire una rete virtuale nel cloud di Azure:

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Questo pulsante apre un modulo che è possibile usare per configurare l'ambiente di rete in cui distribuire l'istanza gestita.

  > [!Note]
  > Questo modello di Azure Resource Manager distribuirà una rete virtuale con due subnet. Una subnet denominata **ManagedInstances** è riservata alle istanze gestite e dispone di una tabella di route preconfigurata, mentre l'altra subnet denominata **Default** viene usata per le altre risorse che devono accedere all'istanza gestita (ad esempio, macchine virtuali di Azure). È possibile rimuovere la subnet **Default** subnet se non è necessaria.

3. Configurare l'ambiente di rete. Nel modulo seguente è possibile configurare i parametri dell'ambiente di rete:

![Configurare la rete di Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

È possibile modificare facoltativamente i nomi della rete virtuale e delle subnet e gli intervalli IP associati alle risorse di rete. Facendo clic sul pulsante "Acquista", il modulo procederà a creare e configurare l'ambiente. Se non sono necessarie due subnet è possibile eliminare quella predefinita. 

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Modificare una rete virtuale esistente per le istanze gestite 

Le domande e le risposte contenute in questa sezione mostrano come aggiungere un'istanza gestita a una rete virtuale esistente. 

**Si intende usare il modello di distribuzione classico o di Resource Manager per la rete virtuale esistente?** 

È possibile creare un'istanza gestita solo in reti virtuali di Resource Manager. 

**Si vuole creare una nuova subnet per l'istanza gestita SQL o usarne una esistente?**

Se si vuole creare una nuova subnet: 

- Calcolare le dimensioni della subnet seguendo le indicazioni della sezione [Determinare le dimensioni della subnet per le istanze gestite](#determine-the-size-of-subnet-for-managed-instances).
- Seguire la procedura illustrata in [Aggiungere, modificare o eliminare una subnet di rete virtuale](../virtual-network/virtual-network-manage-subnet.md). 
- Creare una tabella di route contenente una sola voce, **0.0.0.0/0**, come hop successivo di tipo Internet e associarla alla subnet per l'istanza gestita.  

Se si desidera creare un'istanza gestita all'interno di una subnet esistente, è consigliabile usare lo script di PowerShell seguente per preparare la subnet.
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
La preparazione della subnet viene eseguita in tre semplici passaggi:

- Convalida: la rete virtuale e la subnet selezionate vengono convalidate in base ai requisiti di rete dell'istanza gestita
- Conferma: l'utente visualizza una serie di modifiche che devono essere apportate per preparare la subnet per la distribuzione dell'istanza gestita e gli viene richiesto il consenso
- Preparazione: la rete virtuale e la subnet sono configurate correttamente

**È configurato un server DNS personalizzato?** 

Se lo è, vedere [Configuring a Custom DNS](sql-database-managed-instance-custom-dns.md) (Configurazione di un DNS personalizzato). 

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md)
- Per un'esercitazione sulla creazione di una rete virtuale e di un'istanza gestita e sul ripristino di un database da un backup, vedere l'articolo su come [creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).
- Per questioni relative al DNS, vedere [Configuring a Custom DNS](sql-database-managed-instance-custom-dns.md) (Configurazione di un DNS personalizzato).
