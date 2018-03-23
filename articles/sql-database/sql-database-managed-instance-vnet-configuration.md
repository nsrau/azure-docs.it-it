---
title: Configurazione di una rete virtuale con Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo argomento descrive le opzioni di configurazione di una rete virtuale con Istanza gestita di database SQL di Azure.
services: sql-database
author: srdjan-bozovic
manager: cguyer
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 1a839a9bb2355da9451816828f6f9f0e99f43f5e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Configurare una rete virtuale per Istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure (anteprima) deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. Questa distribuzione consente di: 
- Stabilire una connessione a un'istanza gestita direttamente da una rete locale 
- Connettere un'istanza gestita a un server collegato o a un altro archivio dati locale 
- Connettere un'istanza gestita a risorse di Azure  

## <a name="plan"></a>Pianificazione

Pianificare la modalità di distribuzione di un'istanza gestita in una rete virtuale rispondendo alle domande seguenti: 
- Si prevede di distribuire una sola o più istanze gestite? 

  Il numero di istanze gestite determina le dimensioni minime della subnet da allocare per le istanze gestite. Per altre informazioni, vedere [Determinare le dimensioni della subnet per le istanze gestite](#create-a-new-virtual-network-for-managed-instances). 
- Occorre distribuire l'istanza gestita in una rete virtuale esistente o si sta creando una nuova rete? 

   Se si prevede di usare una rete virtuale esistente, è necessario modificare la configurazione di rete in base all'istanza gestita. Per altre informazioni, vedere la sezione [Modificare la rete virtuale esistente per l'istanza gestita](#modify-an-existing-virtual-network-for-managed-instances). 

   Se si prevede di creare una nuova rete virtuale, vedere [Creare una nuova rete virtuale per l'istanza gestita](#create-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Requisiti

Per la creazione di istanze gestite occorre dedicare una subnet all'interno della rete virtuale che sia conforme ai requisiti seguenti:
- **Vuota**: la subnet non deve contenere altri servizi cloud ad essa associati e non deve essere la subnet del gateway. Non sarà possibile creare l'istanza gestita in una subnet che contiene altre risorse oltre all'istanza gestita o aggiungere altre risorse all'interno della subnet in un secondo momento.
- **Nessun gruppo di sicurezza di rete**: alla subnet non deve essere associato alcun gruppo di sicurezza di rete.
- **Tabella di route specifica**: la subnet deve avere una tabella di route definita dall'utente con hop successivo di tipo Internet 0.0.0.0/0 come unica route ad essa assegnata. Per altre informazioni, vedere [Creare la tabella di route necessaria e associarla](#create-the-required-route-table-and-associate-it)
3. **DNS personalizzato facoltativo**: se per la rete virtuale sono specificate impostazioni DNS personalizzate, è necessario aggiungere l'indirizzo IP dei resolver ricorsivi di Azure (ad esempio 168.63.129.16) all'elenco. Per altre informazioni, vedere [Configuring Custom DNS](sql-database-managed-instance-custom-dns.md) (Configurazione del DNS personalizzato).
4. **Nessun endpoint di servizio**: alla subnet non deve essere associato alcun endpoint di servizio (Archiviazione o SQL). Verificare che l'opzione Endpoint di servizio sia disabilitata durante la creazione della rete virtuale.
5. **Indirizzi IP sufficienti**: la subnet deve avere un minimo di 16 indirizzi IP. Per altre informazioni, vedere [Determinare le dimensioni della subnet per le istanze gestite](#determine-the-size-of-subnet-for-managed-instances).

> [!IMPORTANT]
> Se la subnet di destinazione non è compatibile con tutti i requisiti precedenti, non sarà possibile distribuire la nuova istanza gestita. La rete virtuale e la subnet di destinazione devono essere mantenute conformi a questi requisiti dell'istanza gestita (prima e dopo la distribuzione), in quanto qualsiasi violazione può far sì che l'istanza entri in uno stato difettoso e non sia più disponibile. Per ripristinare uno stato corretto è necessario creare una nuova istanza in una rete virtuale con i criteri di rete conformi, ricreare i dati a livello di istanza e ripristinare i database. Queste procedure aumentano notevolmente i tempi di inattività delle applicazioni.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Determinare le dimensioni della subnet per le istanze gestite

Quando si crea un'istanza gestita, Azure alloca un determinato numero di macchine virtuali in base alle dimensioni del livello selezionate durante il provisioning. Poiché queste macchine virtuali sono associate alla subnet, richiedono indirizzi IP. Per assicurare una disponibilità elevata durante le normali operazioni e la manutenzione del servizio, Azure può allocare ulteriori macchine virtuali. Di conseguenza, il numero di indirizzi IP necessari in una subnet è maggiore del numero di istanze gestite nella subnet stessa. 

Come da progettazione, un'istanza gestita necessita di un minimo di 16 indirizzi IP in una subnet e può usare fino a 256 indirizzi IP. Di conseguenza, è possibile usare le subnet mask da /28 a /24 per definire gli intervalli IP della subnet. 

Se si prevede di distribuire più istanze gestite all'interno della subnet ed è necessario ottimizzare le dimensioni della subnet, usare questi parametri per formulare un calcolo: 

- Cinque indirizzi IP nella subnet sono dedicati ad Azure 
- Ogni istanza per utilizzo generico necessita di due indirizzi 

**Esempio**: si prevede di distribuire otto istanze gestite. Questo significa che sono necessari 5 + 8 * 2 = 21 indirizzi IP. Poiché gli intervalli IP sono definiti in potenza di 2, è necessario l'intervallo IP di 32 (2^5) indirizzi IP. Pertanto è necessario riservare la subnet con subnet mask /27. 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>Creare una nuova rete virtuale per le istanze gestite 

La creazione di una rete virtuale di Azure è un prerequisito per la creazione di un'istanza gestita. È possibile usare il portale di Azure, [PowerShell](../virtual-network/quick-create-powershell.md) o l'[interfaccia della riga di comando di Azure](../virtual-network/quick-create-cli.md). Le sezione seguente mostra la procedura tramite il portale di Azure. I dettagli illustrati qui sono validi per ognuno di questi metodi.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Individuare e quindi selezionare **Rete virtuale**, verificare che la modalità di distribuzione selezionata sia **Resource Manager** e infine fare clic su **Crea**.

   ![Creazione della rete virtuale](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Compilare il modulo relativo alla rete virtuale con le informazioni richieste, in modo simile allo screenshot seguente:

   ![Modulo per la creazione della rete virtuale](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Fare clic su **Crea**.

   Lo spazio indirizzi e la subnet sono specificati nella notazione CIDR. 

   > [!IMPORTANT]
   > I valori predefiniti creano una subnet che usa tutto lo spazio di indirizzi della rete virtuale. Se si sceglie questa opzione, all'interno della rete virtuale non è possibile creare altre risorse oltre all'istanza gestita. 

   L'approccio consigliato è il seguente: 
   - Calcolare le dimensioni della subnet seguendo le indicazioni della sezione [Determinare le dimensioni della subnet per le istanze gestite](#determine-the-size-of-subnet-for-managed-instances)  
   - Valutare le esigenze per il resto della rete virtuale 
   - Compilare gli intervalli di indirizzi della rete virtuale e della subnet di conseguenza 

   Verificare che l'opzione Endpoint di servizio sia impostata su **Disabilitato**. 

   ![Modulo per la creazione della rete virtuale](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>Creare la tabella di route necessaria e associarla

1. Accedere al portale di Azure  
2. Individuare e quindi selezionare **Tabella di route** e infine fare clic su **Crea** nella pagina Tabella di route.

   ![Modulo per la creazione della tabella di route](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Creare una route 0.0.0.0/0 con hop successivo di tipo Internet in modo simile agli screenshot seguenti:

   ![Aggiunta alla tabella di route](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![Route](./media/sql-database-managed-instance-tutorial/route.png)

4. Associare questa route alla subnet per l'istanza gestita, in modo simile agli screenshot seguenti:

    ![Subnet](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![Impostare la tabella di route](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![Impostare la tabella di route: salvataggio](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


Dopo aver creato la rete virtuale, è possibile creare l'istanza gestita.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Modificare una rete virtuale esistente per le istanze gestite 

Le domande e le risposte contenute in questa sezione mostrano come aggiungere un'istanza gestita a una rete virtuale esistente. 

**Si intende usare il modello di distribuzione classico o di Resource Manager per la rete virtuale esistente?** 

È possibile creare un'istanza gestita solo in reti virtuali di Resource Manager. 

**Si vuole creare una nuova subnet per l'istanza gestita SQL o usarne una esistente?**

Se si vuole creare una nuova subnet: 

- Calcolare le dimensioni della subnet seguendo le indicazioni della sezione [Determinare le dimensioni della subnet per le istanze gestite](#determine-the-size-of-subnet-for-managed-instances).
- Seguire la procedura illustrata in [Aggiungere, modificare o eliminare una subnet di rete virtuale](../virtual-network/virtual-network-manage-subnet.md). 
- Creare una tabella di route contenente una sola voce, **0.0.0.0/0**, come hop successivo di tipo Internet e associarla alla subnet per l'istanza gestita.  

Se si vuole creare un'istanza gestita all'interno di una subnet esistente: 
- Verificare che la subnet sia vuota. Un'istanza gestita non può infatti essere creata in una subnet che contiene altre risorse, inclusa la subnet del gateway. 
- Calcolare le dimensioni della subnet seguendo le indicazioni della sezione [Determinare le dimensioni della subnet per le istanze gestite](#determine-the-size-of-subnet-for-managed-instances) e verificare che siano appropriate. 
- Verificare che gli endpoint di servizio non siano abilitati nella subnet.
- Verificare che alla subnet non siano associati gruppi di sicurezza di rete. 

**È configurato un server DNS personalizzato?** 

Se lo è, vedere [Configuring a Custom DNS](sql-database-managed-instance-custom-dns.md) (Configurazione di un DNS personalizzato). 

- Creare la tabella di route necessaria e associarla seguendo le indicazioni in [Creare la tabella di route necessaria e associarla](#create-the-required-route-table-and-associate-it).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md)
- Per un'esercitazione sulla creazione di una rete virtuale e di un'istanza gestita e sul ripristino di un database da un backup, vedere l'articolo su come [creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-tutorial-portal.md).
- Per questioni relative al DNS, vedere [Configuring a Custom DNS](sql-database-managed-instance-custom-dns.md) (Configurazione di un DNS personalizzato).
