---
title: Connettività degli endpoint pubblici per macchine virtuali di Azure & ILB standard negli scenari a disponibilità elevata SAP
description: Connettività degli endpoint pubblici per le macchine virtuali usando Load Balancer Standard di Azure negli scenari a disponibilità elevata SAP
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/16/2020
ms.author: radeltch
ms.openlocfilehash: a0dc9f673abcac549fffc7291b8ac376c297da6b
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836123"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Connettività degli endpoint pubblici per le macchine virtuali usando Load Balancer Standard di Azure negli scenari a disponibilità elevata SAP

L'obiettivo di questo articolo è quello di descrivere le configurazioni che consentiranno la connettività in uscita agli endpoint pubblici. Le configurazioni saranno principalmente nel contesto della disponibilità elevata con Pacemaker per SUSE/RHEL.  

Se si usa Pacemaker con l'agente di isolamento di Azure in una soluzione a disponibilità elevata, le macchine virtuali dovranno avere connettività in uscita all'API di gestione di Azure.  
Questo articolo presenta varie opzioni per poter scegliere quella più adatta allo scenario in uso.  

## <a name="overview"></a>Panoramica

Quando si implementa disponibilità elevata per soluzioni SAP tramite il clustering, uno dei componenti necessari è [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md). Azure offre due SKU di Load Balancer: Standard e Basic.

Azure Load Balancer Standard offre alcuni vantaggi rispetto a Load Balancer Basic. Può essere usato, ad esempio, in più zone di disponibilità di Azure, offre funzionalità di monitoraggio e registrazione migliori per semplificare la risoluzione dei problemi e presenta una latenza ridotta. La funzionalità "porte a disponibilità elevata", inoltre, copre tutte le porte, eliminando la necessità di elencare ogni singola porta.  

Esistono alcune differenze importanti tra lo SKU Basic e lo SKU Standard di Azure Load Balancer, la prima delle quali è la gestione del traffico in uscita verso l'endpoint pubblico. Per un'analisi comparativa completa tra lo SKU Basic e lo SKU Standard di Load Balancer, vedere [Confronto tra gli SKU di Load Balancer](../../../load-balancer/load-balancer-overview.md).  
 
Se vengono inserite macchine virtuali senza indirizzi IP pubblici nel pool back-end di Load Balancer Standard interno ad Azure (nessun indirizzo IP pubblico), non sarà presente alcuna connettività in uscita verso gli endpoint pubblici, a meno che non venga eseguita una configurazione aggiuntiva.  

Se a una macchina virtuale viene assegnato un indirizzo IP pubblico o se la macchina virtuale si trova nel pool back-end di un'istanza di Load Balancer con indirizzo IP pubblico, sarà presente invece connettività in uscita verso gli endpoint pubblici.  

I sistemi SAP contengono spesso dati aziendali riservati. Nella maggior parte dei casi, quindi, non è accettabile che macchine virtuali in cui sono ospitati sistemi SAP abbiano indirizzi IP pubblici. Al tempo stesso, tuttavia, esistono scenari in cui è necessaria la connettività in uscita dalla macchina virtuale verso endpoint pubblici.  

Tra gli scenari che richiedono l'accesso a un endpoint pubblico di Azure è possibile includere, ad esempio:  
- Uso dell'agente di isolamento di Azure come meccanismo di isolamento nei cluster Pacemaker
- Backup di Azure
- Azure Site Recovery  
- Uso del repository pubblico per l'applicazione di patch al sistema operativo
- Flusso di dati di un'applicazione SAP, che può richiedere la connettività in uscita a un endpoint pubblico

Se la distribuzione SAP in uso non richiede la connettività in uscita verso endpoint pubblici, non è necessario implementare alcuna configurazione aggiuntiva. È sufficiente creare un'istanza di SKU Azure Load Balancer Standard interno per lo scenario di disponibilità elevata da gestire, supponendo che non sia necessaria connettività in ingresso dagli endpoint pubblici.  

> [!Note]
> Se vengono inserite macchine virtuali senza indirizzi IP pubblici nel pool back-end di Load Balancer Standard interno ad Azure (nessun indirizzo IP pubblico), non sarà presente alcuna connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici.  
>Se invece le macchine virtuali hanno indirizzi IP pubblici o si trovano già nel pool back-end di Azure Load Balancer con indirizzo IP pubblico, sarà già presente connettività in uscita verso endpoint pubblici.


Leggere prima i documenti seguenti:

* Azure Load Balancer Standard
  * [Panoramica di Azure Load Balancer Standard](../../../load-balancer/load-balancer-overview.md) - Panoramica completa di Azure Load Balancer Standard, principi importanti, concetti ed esercitazioni. 
  * [Connessioni in uscita in Azure](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) - Scenari su come ottenere la connettività in uscita in Azure
  * [Regole in uscita di Load Balancer](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules) - Spiegazione dei concetti relativi alle regole in uscita di Load Balancer e di come creare regole in uscita
* Firewall di Azure
  * [Panoramica del Firewall di Azure](../../../firewall/overview.md) - Panoramica del Firewall di Azure
  * [Esercitazione: Distribuire e configurare il Firewall di Azure](../../../firewall/tutorial-firewall-deploy-portal.md) - Istruzioni su come configurare il Firewall di Azure tramite il portale di Azure
* [Reti virtuali - Regole definite dall'utente](../../../virtual-network/virtual-networks-udr-overview.md#user-defined) - Concetti e regole di routing di Azure  
* [Tag del servizio Gruppi di sicurezza](../../../virtual-network/security-overview.md#service-tags) - Come semplificare i gruppi di sicurezza di rete e la configurazione del firewall con i tag del servizio

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Load Balancer Standard di Azure esterno aggiuntivo per connessioni in uscita a Internet

Per ottenere connettività in uscita verso endpoint pubblici senza consentire connettività in ingresso alle macchine virtuali da un endpoint pubblico, è possibile creare una seconda istanza di Load Balancer con indirizzo IP pubblico, aggiungere le macchine virtuali al pool back-end della seconda istanza e definire solo [regole in uscita](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules).  
Usare [Gruppi di sicurezza di rete](../../../virtual-network/security-overview.md) per controllare gli endpoint pubblici accessibili per chiamate in uscita dalla macchina virtuale.  
Per altre informazioni, vedere lo Scenario 2 nel documento [Connessioni in uscita](../../../load-balancer/load-balancer-outbound-connections.md#scenarios).  
La configurazione avrà un aspetto simile al seguente:  

![Controllo della connettività verso endpoint pubblici con i gruppi di sicurezza di rete](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Considerazioni importanti

- Per ottenere connettività in uscita a un endpoint pubblico e ottimizzare i costi, è possibile usare un'istanza di Load Balancer pubblico aggiuntiva per più macchine virtuali della stessa subnet.  
- Usare [Gruppi di sicurezza di rete](../../../virtual-network/security-overview.md) per controllare gli endpoint pubblici accessibili dalle macchine virtuali. È possibile assegnare il gruppo di sicurezza di rete alla subnet o a ogni macchina virtuale. Laddove sia possibile, usare [Tag di servizio](../../../virtual-network/security-overview.md#service-tags) per ridurre la complessità delle regole di sicurezza.  
- Load Balancer Standard di Azure con un indirizzo IP pubblico e regole in uscita consente l'accesso diretto a un endpoint pubblico. Se i requisiti di sicurezza aziendali prevedono che tutto il traffico in uscita passi attraverso una soluzione aziendale centralizzata per il controllo e la registrazione, è possibile che con questo scenario non sia possibile soddisfare tali requisiti.  

>[!TIP]
>Laddove sia possibile, usare [Tag di servizio](../../../virtual-network/security-overview.md#service-tags) per ridurre la complessità del gruppo di sicurezza di rete. 

### <a name="deployment-steps"></a>Passaggi di distribuzione

1. Crea servizio di bilanciamento del carico  
   1. Nel [portale di Azure](https://portal.azure.com) fare clic su Tutte le risorse, Aggiungi e quindi cercare **Load Balancer**  
   1. Fare clic su **Crea** 
   1. Nome di Load Balancer **MyPublicILB**  
   1. Selezionare **Pubblico** come Tipo e **Standard** come SKU  
   1. Selezionare **Crea indirizzo IP pubblico** e specificare **MyPublicILBFrondEndIP** come nome  
   1. Per Zona di disponibilità selezionare **Con ridondanza della zona**  
   1. Fare clic su Rivedi e crea e quindi su Crea  
2. Creare il pool back-end **MyBackendPoolOfPublicILB** e aggiungere le macchine virtuali.  
   1. Selezionare la rete virtuale  
   1. Selezionare le macchine virtuali e i relativi indirizzi IP e aggiungerli al pool back-end  
3. [Creare regole in uscita](../../../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard%3ftabs%3doption-1-create-load-balancer-standard#create-outbound-rule-configuration). Attualmente non è possibile creare regole in uscita dal portale di Azure, ma è possibile crearle mediante l'[interfaccia della riga di comando di Azure](../../../cloud-shell/overview.md?view=azure-cli-latest).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Creare regole del gruppo di sicurezza di rete per limitare l'accesso a specifici endpoint pubblici. Se è già presente un gruppo di sicurezza di rete, è possibile modificarlo. L'esempio seguente illustra come consentire l'accesso all'API di gestione di Azure: 
   1. Passare al gruppo di sicurezza di rete
   1. Fare clic su Regole di sicurezza in uscita
   1. Aggiungere una regola per **Negare** tutti gli accessi in uscita a **Internet**.
   1. Aggiungere una regola a **Consentire** l'accesso ad **AzureCloud**, con priorità inferiore rispetto alla priorità della regola che nega l'accesso a Internet.


   Le regole di sicurezza in uscita avranno un aspetto simile al seguente: 

   ![Connessione in uscita con la seconda istanza di Load Balancer con IP pubblico](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Per altre informazioni sui gruppi di sicurezza di rete di Azure, vedere [Gruppi di sicurezza di rete](../../../virtual-network/security-overview.md). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Firewall di Azure per le connessioni in uscita a Internet

Un'altra soluzione per ottenere connettività in uscita a endpoint pubblici, senza consentire la connettività in ingresso alle macchine virtuali da endpoint pubblici, è quella di usare il Firewall di Azure, un servizio gestito con disponibilità elevata predefinita che può estendersi a più zone di disponibilità.  
Sarà anche necessario distribuire una [route definita dall'utente](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes) associata alla subnet in cui sono distribuite le macchine virtuali e Azure Load Balancer che punti al Firewall di Azure, in modo da instradare il traffico attraverso il firewall.  
Per informazioni dettagliate su come distribuire il Firewall di Azure, vedere [Distribuire e configurare il Firewall di Azure](../../../firewall/tutorial-firewall-deploy-portal.md).  

L'architettura avrà un aspetto analogo al seguente:

![Connessione in uscita con il Firewall di Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Considerazioni importanti

- Il Firewall di Azure è un servizio cloud nativo con disponibilità elevata predefinita e supporto per la distribuzione a livello di zona.
- Richiede una subnet aggiuntiva che deve essere denominata AzureFirewallSubnet. 
- Se si trasferiscono set di dati di grandi dimensioni in uscita dalla rete virtuale in cui si trovano le macchine virtuali SAP verso una macchina virtuale che si trova in un'altra rete virtuale o in un endpoint pubblico, potrebbe non essere una soluzione conveniente. Uno scenario di questo tipo può essere, ad esempio, la copia di backup di grandi dimensioni su reti virtuali diverse. Per informazioni dettagliate, vedere Prezzi del servizio Firewall di Azure.  
- Se la soluzione firewall aziendale non è il servizio Firewall di Azure e i requisiti di sicurezza aziendali prevedono che tutto il traffico in uscita passi attraverso una soluzione aziendale centralizzata, questa soluzione potrebbe non essere pratica.  

>[!TIP]
>Laddove sia possibile, usare [Tag di servizio](../../../virtual-network/security-overview.md#service-tags) per ridurre la complessità delle regole del Firewall di Azure.  

### <a name="deployment-steps"></a>Passaggi di distribuzione

1. La procedura di distribuzione presuppone che sia già stata definita la rete virtuale e la subnet per le macchine virtuali.  
2. Creare una subnet **AzureFirewallSubnet** nella stessa rete virtuale in cui sono distribuite le macchine virtuali e Load Balancer Standard.  
   1. Nel portale di Azure passare alla rete virtuale: Fare clic su Tutte le risorse, cercare la rete virtuale, fare clic sulla rete virtuale e selezionare Subnet.  
   1. Fare clic su Aggiungi subnet. Specificare **AzureFirewallSubnet** come nome. Immettere l'intervallo di indirizzi appropriato. Salvare.  
3. Creare il servizio Firewall di Azure.  
   1. Nel portale di Azure selezionare Tutte le risorse e fare clic su Aggiungi, Firewall, Crea. Selezionare Gruppo di risorse (selezionare lo stesso gruppo di risorse in cui si trova la rete virtuale).  
   1. Immettere il nome per la risorsa Firewall di Azure, ad esempio **MyAzureFirewall**.  
   1. Selezionare Area di Azure e almeno due zone di disponibilità, allineate con le zone di disponibilità in cui sono distribuite le macchine virtuali.  
   1. Selezionare la rete virtuale in cui sono distribuite le macchine virtuali SAP e Load Balancer Standard di Azure.  
   1. Indirizzo IP pubblico: Fare clic su Crea e immettere un nome, ad esempio **MyFirewallPublicIP**.  
4. Creare una regola del Firewall di Azure per consentire la connettività in uscita verso specifici endpoint pubblici. L'esempio seguente illustra come consentire all'API di gestione di Azure di accedere a un endpoint pubblico.  
   1. Selezionare Regole, Raccolta regole di rete e fare clic su Aggiungi raccolta regole di rete.  
   1. Nome: **MyOutboundRule**, specificare la priorità e selezionare l'azione **Consenti**.  
   1. Servizio: Nome **ToAzureAPI**.  Protocollo: selezionare **Tutti**. Indirizzo di origine: immettere l'intervallo relativo alla subnet in cui sono distribuite le macchine virtuali e Load Balancer Standard, ad esempio: **11.97.0.0/24**. Porte di destinazione: digitare <b>*</b>.  
   1. Salvare
   1. Quando si è ancora all'interno del servizio Firewall di Azure, selezionare Panoramica. Prendere nota dell'indirizzo IP privato del Firewall di Azure.  
5. Creare una route al Firewall di Azure  
   1. Nel portale di Azure selezionare Tutte le risorse e fare clic su Aggiungi, Tabella di routing, Crea.  
   1. Immettere il nome MyRouteTable e selezionare la sottoscrizione, il gruppo di risorse e il percorso (corrispondente al percorso della rete virtuale e del firewall).  
   1. Salvare  

   La regola del firewall avrà un aspetto simile al seguente: ![Connessione in uscita con il Firewall di Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Creare una route definita dall'utente dalla subnet delle macchine virtuali all'indirizzo IP privato di **MyAzureFirewall**.
   1. Dalla tabella di routing fare clic su Route. Selezionare Aggiungi. 
   1. Nome della route: ToMyAzureFirewall; prefisso dell'indirizzo: **0.0.0.0/0**. Tipo hop successivo: selezionare Appliance virtuale. Indirizzo hop successivo: immettere l'indirizzo IP privato del firewall configurato: **11.97.1.4**.  
   1. Salvare

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Uso del proxy per le chiamate di Pacemaker all'API di gestione di Azure

È possibile usare il proxy per consentire chiamate di Pacemaker all'endpoint pubblico dell'API di gestione di Azure.  

### <a name="important-considerations"></a>Considerazioni importanti

  - Se è già presente un proxy aziendale, è possibile usarlo per instradare le chiamate in uscita verso gli endpoint pubblici. In questo modo, le chiamate in uscita verso endpoint pubblici passeranno attraverso il punto di controllo aziendale.  
  - Assicurarsi che la configurazione del proxy consenta la connettività in uscita all'API di gestione di Azure: `https://management.azure.com` e `https://login.microsoftonline.com`  
  - Verificare che sia presente una route dalle macchine virtuali al proxy  
  - Il proxy gestirà solo chiamate HTTP/HTTPS. Se le chiamate in uscita a endpoint pubblici dovranno essere eseguite attraverso protocolli diversi, ad esempio RFC, sarà necessaria una soluzione alternativa  
  - La soluzione proxy deve essere a disponibilità elevata per evitare instabilità nel cluster Pacemaker  
  - A seconda della posizione del proxy, è possibile che venga introdotta latenza aggiuntiva nelle chiamate dall'agente di isolamento di Azure all'API di gestione di Azure. Se il proxy aziendale è ancora in locale, mentre il cluster Pacemaker si trova in Azure, misurare la latenza e valutare se questa soluzione è adatta alle proprie esigenze  
  - Se non è già disponibile un proxy aziendale a disponibilità elevata, questa opzione non è consigliata perché il cliente potrebbe dover sostenere complessità e costi aggiuntivi. Tuttavia, se si decide di distribuire una soluzione proxy aggiuntiva per consentire la connettività in uscita da Pacemaker all'API pubblica di gestione di Azure, assicurarsi che il proxy sia a disponibilità elevata e che la latenza dalle macchine virtuali al proxy sia bassa.  

### <a name="pacemaker-configuration-with-proxy"></a>Configurazione di Pacemaker con un proxy 

Sono disponibili molte soluzioni proxy sul mercato. Istruzioni dettagliate per la distribuzione del proxy esulano quindi dall'ambito di questo documento. Nell'esempio seguente si presuppone che il proxy stia rispondendo a **MyProxyService** e ascoltando la porta **MyProxyPort**.  
Per consentire a Pacemaker di comunicare con l'API di gestione di Azure, seguire questa procedura in tutti i nodi del cluster:  

1. Modificare il file di configurazione di Pacemaker /etc/sysconfig/pacemaker e aggiungere le righe seguenti (tutti i nodi del cluster):

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Riavviare il servizio Pacemaker in **tutti** i nodi del cluster.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="other-solutions"></a>Altre soluzioni

Se il traffico in uscita viene indirizzato tramite un firewall di terze parti:

- Se si usa l'agente di recinzione di Azure, assicurarsi che la configurazione del firewall consenta la connettività in uscita all'API di gestione di Azure: `https://management.azure.com` e`https://login.microsoftonline.com`   
- Se si usa l'infrastruttura di aggiornamento cloud pubblico di SUSE per l'applicazione di aggiornamenti e patch, vedere l' [infrastruttura di aggiornamento del cloud pubblico di azure 101](https://suse.com/c/azure-public-cloud-update-infrastructure-101/)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come configurare Pacemaker in SUSE in Azure](./high-availability-guide-suse-pacemaker.md)
* [Informazioni su come configurare Pacemaker in Red Hat in Azure](./high-availability-guide-rhel-pacemaker.md)