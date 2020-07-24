---
title: Abilitare il ripristino di emergenza da zona a zona per le macchine virtuali di Azure
description: Questo articolo descrive quando e come usare il ripristino di emergenza da zona a zona per le macchine virtuali di Azure.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: a1952f6dccf12de4cb1571dacabecf78c65cd01b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87021648"
---
# <a name="enable-zone-to-zone-disaster-recovery-for-azure-virtual-machines"></a>Abilitare il ripristino di emergenza da zona a zona per le macchine virtuali di Azure

Questo articolo descrive come eseguire la replica, il failover e il failback di macchine virtuali di Azure da una zona di disponibilità a un'altra all'interno della stessa area di Azure.

>[!NOTE]
>
>- Il supporto per il ripristino di emergenza da zona a zona è attualmente limitato a due aree: Asia sudorientale e Regno Unito meridionale.  
>- Site Recovery non sposta o archivia i dati dei clienti all'interno dell'area in cui viene distribuito quando il cliente usa il ripristino di emergenza da zona a zona. I clienti possono selezionare un insieme di credenziali dei servizi di ripristino da un'area diversa se lo scelgono. L'insieme di credenziali di servizi di ripristino contiene metadati ma non dati effettivi del cliente.

Site Recovery servizio contribuisce alla strategia di continuità aziendale e ripristino di emergenza, mantenendo le app aziendali in esecuzione, durante le interruzioni pianificate e non pianificate. Si tratta dell'opzione di ripristino di emergenza consigliata per rendere operativo le applicazioni in caso di interruzioni a livello di area.

Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona ha uno o più data center. 

## <a name="using-availability-zones-for-disaster-recovery"></a>Uso di zone di disponibilità per il ripristino di emergenza 

In genere, zone di disponibilità vengono usati per distribuire macchine virtuali in una configurazione a disponibilità elevata. Potrebbero essere troppo vicini tra loro per fungere da soluzione di ripristino di emergenza in caso di calamità naturale.

In alcuni scenari, tuttavia, è possibile sfruttare zone di disponibilità per il ripristino di emergenza:

- Molti clienti che hanno avuto una strategia di ripristino di emergenza in metropolitana mentre ospitavano le applicazioni in locale a volte sembrano imitare questa strategia una volta migrate le applicazioni in Azure. Questi clienti riconoscono il fatto che la strategia di ripristino di emergenza in metro potrebbe non funzionare in caso di emergenza fisica su larga scala e accettare questo rischio. Per tali clienti, il ripristino di emergenza da zona a zona può essere usato come opzione di ripristino di emergenza.

- Molti altri clienti hanno un'infrastruttura di rete complessa e non vogliono ricrearla in un'area secondaria a causa dei costi e della complessità associati. Il ripristino di emergenza da zona a zona riduce la complessità poiché sfrutta i concetti di rete ridondanti in zone di disponibilità rendendo molto più semplice la configurazione. Questi clienti preferiscono la semplicità e possono anche usare zone di disponibilità per il ripristino di emergenza.

- In alcune aree che non dispongono di un'area abbinata all'interno della stessa giurisdizione legale (ad esempio, Asia sudorientale), il ripristino di emergenza da zona a zona può fungere da soluzione di ripristino di emergenza, poiché consente di garantire la conformità legale, poiché le applicazioni e i dati non superano i limiti nazionali. 

- Il ripristino di emergenza da zona a zona implica la replica dei dati tra distanze più brevi rispetto al ripristino di emergenza da Azure ad Azure e pertanto è possibile che si verifichi una latenza più bassa e, di conseguenza, RPO inferiori.

Anche se si tratta di vantaggi avanzati, esiste la possibilità che il ripristino di emergenza di zone per zone rientri a breve dei requisiti di resilienza in caso di calamità naturale a livello di area.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Rete per il ripristino di emergenza da zona a zona

Come indicato in precedenza, il ripristino di emergenza da zona a zona riduce la complessità poiché sfrutta i concetti di rete ridondanti in zone di disponibilità rendendo molto più semplice la configurazione. Il comportamento dei componenti di rete nello scenario di ripristino di emergenza da zona a zona è illustrato di seguito: 

- Rete virtuale: è possibile usare la stessa rete virtuale della rete di origine per i failover effettivi. Usare una rete virtuale diversa per la rete virtuale di origine per i failover di test.

- Subnet: il failover nella stessa subnet è supportato.

- Indirizzo IP privato: se si usano indirizzi IP statici, è possibile usare gli stessi IP nella zona di destinazione se si sceglie di configurarli in tale modalità.

- Rete accelerata: Analogamente al ripristino di emergenza da Azure ad Azure, è possibile abilitare la rete accelerata se lo SKU della VM lo supporta.

- Indirizzo IP pubblico: è possibile aggiungere un indirizzo IP pubblico standard creato in precedenza nella stessa area alla macchina virtuale di destinazione. Gli indirizzi IP pubblici di base non supportano gli scenari correlati alle zone di disponibilità.

- Bilanciamento del carico: Load Balancer standard è una risorsa a livello di area ed è quindi possibile collegare la macchina virtuale di destinazione al pool back-end dello stesso servizio di bilanciamento del carico. Non è necessario un nuovo servizio di bilanciamento del carico.

- Gruppo di sicurezza di rete: è possibile usare gli stessi gruppi di sicurezza di rete applicati alla VM di origine.

## <a name="pre-requisites"></a>Prerequisiti

Prima di distribuire il ripristino di emergenza da zona a zona per le macchine virtuali, è importante assicurarsi che altre funzionalità abilitate nella VM siano interoperabili con il ripristino di emergenza da zona a zona.

|Funzionalità  | Informativa sul supporto  |
|---------|---------|
|Macchine virtuali classiche   |     Non supportato    |
|VM ARM    |    Funzionalità supportata    |
|Crittografia dischi di Azure V1 (doppio passaggio, con AAD)     |     Funzionalità supportata |
|Crittografia dischi di Azure V2 (Single Pass, senza AAD)    |    Funzionalità supportata    |
|Dischi non gestiti    |    Non supportato    |
|Dischi gestiti    |    Funzionalità supportata    |
|Chiavi gestite dal cliente    |    Funzionalità supportata    |
|Gruppi di selezione host di prossimità    |    Funzionalità supportata    |
|Interoperabilità di backup    |    Il backup e il ripristino a livello di file sono supportati. Backup e ripristino a livello di disco e VM e non supportati.    |
|Aggiunta/rimozione a caldo    |    È possibile aggiungere dischi dopo aver abilitato la replica da zona a zona. La rimozione dei dischi dopo l'abilitazione della replica da zona a zona non è supportata.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Configurare Site Recovery il ripristino di emergenza da zona a zona

### <a name="log-in"></a>Accesso

Accedere al portale di Azure.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Abilitare la replica per la macchina virtuale di Azure di zona

1. Nel menu del portale di Azure selezionare Macchine virtuali oppure cercare e selezionare Macchine virtuali in qualsiasi pagina. Selezionare la macchina virtuale da replicare. Per il ripristino di emergenza da zona a zona, questa macchina virtuale deve essere già presente in una zona di disponibilità.

2. In Operazioni selezionare Ripristino di emergenza.

3. Come illustrato di seguito, nella scheda nozioni di base selezionare ' Sì' per il ripristino di emergenza tra zone di disponibilità?'

    ![Pagina impostazioni di base](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Se si accettano tutte le impostazioni predefinite, fare clic su "verifica + Avvia replica" seguito da "Avvia replica".

5. Se si desidera apportare modifiche alle impostazioni di replica, fare clic su "Avanti: impostazioni avanzate".

6. Modificare le impostazioni a partire da quella predefinita, laddove appropriato. Per gli utenti del ripristino di emergenza da Azure ad Azure, questa pagina potrebbe sembrare familiare. Altre informazioni sulle opzioni presentate in questo pannello sono disponibili [qui](./azure-to-azure-tutorial-enable-replication.md)

    ![Pagina Impostazioni avanzate](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Fare clic su' avanti: verifica + Avvia replica ' e quindi su' Avvia replica '.

## <a name="faqs"></a>Domande frequenti

**1. come funzionano i prezzi per il ripristino di emergenza da zona a zona?**
Il piano tariffario per il ripristino di emergenza da zona a zona è identico a quello del ripristino di emergenza da Azure ad Azure. Per ulteriori informazioni, vedere la pagina dei prezzi [qui](https://azure.microsoft.com/pricing/details/site-recovery/) e [qui](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/). Si noti che gli addebiti in uscita che verrebbero visualizzati nel ripristino di emergenza da zona a zona sarebbero inferiori al ripristino di emergenza da area ad area.

**2. Qual è il contratto di contratto per RTO e RPO?**
Il contratto di RTO è identico a quello per Site Recovery complessiva. Si promettono RTO per un massimo di 2 ore. Non esiste alcun contratto di contratto per RPO.

**3. la capacità è garantita nella zona secondaria?**
Il team di Site Recovery e il team di gestione della capacità di Azure pianificano una capacità dell'infrastruttura sufficiente. Quando si avvia un failover, i team contribuiscono anche a garantire che le istanze di VM protette da Site Recovery verranno distribuite nell'area di destinazione.

**4. quali sistemi operativi sono supportati?**
Il ripristino di emergenza da zona a zona supporta gli stessi sistemi operativi di Azure per il ripristino di emergenza di Azure. Vedere la matrice di supporto [qui](./azure-to-azure-support-matrix.md).

**5. i gruppi di risorse di origine e di destinazione possono essere uguali?**
No, è necessario eseguire il failover in un gruppo di risorse diverso.

## <a name="next-steps"></a>Passaggi successivi

I passaggi da seguire per eseguire un'esercitazione sul ripristino di emergenza, il failover, la riprotezione e il failback sono gli stessi dei passaggi in uno scenario di ripristino di emergenza da Azure ad Azure.

Per eseguire un'esercitazione sul ripristino di emergenza, seguire i passaggi descritti [qui](./azure-to-azure-tutorial-dr-drill.md).

Per eseguire un failover e riproteggere le macchine virtuali nella zona secondaria, seguire i passaggi descritti [qui](./azure-to-azure-tutorial-failover-failback.md).

Per eseguire il failback nella zona primaria, seguire i passaggi descritti [qui](./azure-to-azure-tutorial-failback.md).
