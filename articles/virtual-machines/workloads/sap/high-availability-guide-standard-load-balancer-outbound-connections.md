---
title: Connettività endpoint pubblici per le macchine virtuali di Azure&ILB standard negli scenari SAPPublic endpoint connectivity for Azure VMs&Standard ILB in SAP HA scenarios
description: Connettività endpoint pubblici per macchine virtuali che usano Il bilanciamento del carico standard di Azure negli scenari di disponibilità elevata SAPPublic endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios
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
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 4fd01764c183098a8bd78d502eea7ab173fa22cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293907"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Connettività endpoint pubblici per macchine virtuali che usano Il bilanciamento del carico standard di Azure negli scenari di disponibilità elevata SAPPublic endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios

L'ambito di questo articolo consiste nel descrivere le configurazioni, che consentiranno la connettività in uscita agli end point pubblici. Le configurazioni sono principalmente nel contesto di Disponibilità Elevata con Pacemaker per SUSE / RHEL.  

Se si usa Pacemaker con l'agente di azure fence nella soluzione a disponibilità elevata, le macchine virtuali devono disporre della connettività in uscita all'API di gestione di Azure.If you are using Pacemaker with Azure fence agent in your high availability solution, then the VMs must have outbound connectivity to the Azure management API.  
L'articolo presenta diverse opzioni che consentono di selezionare l'opzione più adatta per lo scenario.  

## <a name="overview"></a>Panoramica

Quando si implementa la disponibilità elevata per le soluzioni SAP tramite il clustering, uno dei componenti necessari è [Azure Load Balancer.](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) Azure offre due SKU di bilanciamento del carico: standard e di base.

Il servizio di bilanciamento del carico standard di Azure offre alcuni vantaggi rispetto al servizio di bilanciamento del carico Basic.Standard Azure load balancer offers some advantages over the Basic load balancer. Ad esempio, funziona nelle zone di disponibilità di Azure, offre migliori funzionalità di monitoraggio e registrazione per semplificare la risoluzione dei problemi e ridurre la latenza. La funzionalità "Porte HA" copre tutte le porte, ovvero non è più necessario elencare tutte le singole porte.  

Esistono alcune differenze importanti tra lo SKU di base e lo SKU standard del servizio di bilanciamento del carico di Azure.There are some important differences between the basic and the standard SKU of Azure load balancer. Uno di questi è la gestione del traffico in uscita verso l'punto pubblico. Per un confronto completo tra il bilanciamento del carico SKU Basic e quello sKU Standard, vedere Confronto tra SKU di [Load Balancer.](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)  
 
Quando le macchine virtuali senza indirizzi IP pubblici vengono inserite nel pool back-end del servizio di bilanciamento del carico standard di Azure interno (nessun indirizzo IP pubblico), non esiste alcuna connettività in uscita agli endpoint pubblici, a meno che non venga eseguita una configurazione aggiuntiva.  

Se a una macchina virtuale viene assegnato un indirizzo IP pubblico o se si trova nel pool back-end di un servizio di bilanciamento del carico con indirizzo IP pubblico, la connettività in uscita agli endpoint pubblici.  

I sistemi SAP spesso contengono dati aziendali sensibili. Raramente è accettabile che le macchine virtuali che ospitano sistemi SAP dispongano di indirizzi IP pubblici. Allo stesso tempo, esistono scenari che richiederebbero la connettività in uscita dalla macchina virtuale agli endpoint pubblici.  

Esempi di scenari che richiedono l'accesso all'end point pubblico di Azure sono:Examples of scenarios, requiring access to Azure public end point are:  
- Uso di Azure Fence Agent come meccanismo di scherma nei cluster PacemakerUsing Azure Fence Agent as a fencing mechanism in Pacemaker clusters
- Backup di Azure
- Azure Site Recovery  
- Utilizzo dell'archivio pubblico per l'applicazione di patch al sistema operativo
- Il flusso di dati dell'applicazione SAP può richiedere la connettività in uscita all'estremità pubblica

Se la distribuzione SAP non richiede la connettività in uscita agli endpoint pubblici, non è necessario implementare la configurazione aggiuntiva. È sufficiente creare Azure Load Balancer standard interno per lo scenario di disponibilità elevata, presupponendo che non sia necessaria la connettività in ingresso dagli endpoint pubblici.  

> [!Note]
> Quando le macchine virtuali senza indirizzi IP pubblici vengono inserite nel pool back-end del servizio di bilanciamento del carico standard di Azure interno (nessun indirizzo IP pubblico), non sarà disponibile alcuna connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing agli endpoint pubblici.  
>Se le macchine virtuali hanno indirizzi IP pubblici o sono già nel pool back-end del servizio di bilanciamento del carico di Azure con indirizzo IP pubblico, la macchina virtuale avrà già la connettività in uscita agli endpoint pubblici.


Leggere prima i seguenti articoli:

* Servizio di bilanciamento del carico standard di AzureAzure Standard Load Balancer
  * [Panoramica di Azure Standard Load Balancer:](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) panoramica completa del bilanciamento del carico standard di Azure, principi importanti, concetti ed esercitazioniAzure Standard Load Balancer overview - comprehensive overview of Azure Standard Load balancer, important principles, concepts, and tutorials 
  * [Connessioni in uscita in Azure:](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) scenari su come ottenere la connettività in uscita in AzureOutbound connections in Azure - scenarios on how to achieve outbound connectivity in Azure
  * [Regole in uscita del servizio](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)di bilanciamento del carico: illustra i concetti delle regole in uscita del servizio di bilanciamento del carico e come creare le regole in uscita
* Firewall di Azure
  * [Panoramica di Firewall](https://docs.microsoft.com/azure/firewall/overview)di Azure - Panoramica di Firewall di AzureAzure Firewall Overview - overview of Azure Firewall
  * [Esercitazione: Distribuire e configurare Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) di Azure- istruzioni su come configurare Firewall di Azure tramite il portale di AzureTutorial: Deploy and configure Azure Firewall - instructions on how to configure Azure Firewall via Azure portal
* [Reti virtuali - Regole definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) - Concetti e regole di routing di AzureVirtual Networks -User defined rules - Azure routing concepts and rules  
* [Tag dei](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) servizi dei gruppi di sicurezza: come semplificare la configurazione dei gruppi di sicurezza di rete e del firewall con i tag di servizioSecurity Groups Service Tags - how to simplify your Network Security Groups and Firewall configuration with service tags

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Servizio di bilanciamento del carico azure Standard esterno aggiuntivo per le connessioni in uscita a InternetAdditional external Azure Standard Load Balancer for outbound connections to internet

Un'opzione per ottenere la connettività in uscita agli endpoint pubblici, senza consentire la connettività in ingresso alla macchina virtuale dall'endpoint pubblico, consiste nel creare un secondo servizio di bilanciamento del carico con indirizzo IP pubblico, aggiungere le macchine virtuali al pool back-end del secondo servizio di bilanciamento del carico e definire solo [le regole in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).  
Usare Gruppi di sicurezza di [rete](https://docs.microsoft.com/azure/virtual-network/security-overview) per controllare gli endpoint pubblici accessibili per le chiamate in uscita dalla macchina virtuale.  
Per ulteriori informazioni, vedere Scenario 2 nel documento [Connessioni in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios).  
La configurazione sarebbe simile alla seguente:The configuration would look like:  

![Controllare la connettività agli endpoint pubblici con i gruppi di sicurezza di reteControl connectivity to public end points with Network Security Groups](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Considerazioni importanti

- È possibile usare un servizio di bilanciamento del carico pubblico aggiuntivo per più macchine virtuali nella stessa subnet per ottenere la connettività in uscita all'obiettivo pubblico e ottimizzare i costiYou can use one additional Public Load Balancer for multiple VMs in the same subnet to achieve outbound connectivity to public end point and optimize cost  
- Usare [i gruppi](https://docs.microsoft.com/azure/virtual-network/security-overview) di sicurezza di rete per controllare quali endpoint pubblici sono accessibili dalle macchine virtuali. È possibile assegnare il gruppo di sicurezza di rete alla subnet o a ogni macchina virtuale. Se possibile, utilizzare [i tag Service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) per ridurre la complessità delle regole di sicurezza.  
- Il servizio di bilanciamento del carico standard di Azure con indirizzo IP pubblico e regole in uscita consente l'accesso diretto all'punto pubblico. Se si dispone di requisiti di sicurezza aziendali per avere tutto il traffico in uscita passare tramite soluzione aziendale centralizzata per il controllo e la registrazione, potrebbe non essere in grado di soddisfare il requisito con questo scenario.  

>[!TIP]
>Se possibile, utilizzare [i tag Service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) per ridurre la complessità del gruppo di sicurezza di rete . 

### <a name="deployment-steps"></a>Passaggi di distribuzione

1. Crea servizio di bilanciamento del carico  
   1. Nel [portale di Azure](https://portal.azure.com) fare clic su Tutte le risorse, Aggiungi, quindi cercare **Load Balancer**  
   1. Fare clic su **Crea** 
   1. Nome del servizio di bilanciamento del carico **MyPublicILB**  
   1. Selezionare Pubblico come tipo, Standard come SKUSelect **Public** as a Type, **Standard** as SKU  
   1. Selezionare **Crea indirizzo IP pubblico** e specificare come nome **MyPublicILBFrondEndIP**  
   1. Selezionare **la zona ridondante** come zona di disponibilitàSelect zone Redundant as Availability zone  
   1. Fai clic su Rivedi e crea, quindi fai clic su Crea  
2. Creare il pool di back-end **MyBackendPoolOfPublicILB** e aggiungere le macchine virtuali.  
   1. Selezionare la rete virtuale  
   1. Selezionare le macchine virtuali e i relativi indirizzi IP e aggiungerli al pool back-end  
3. [Creare regole in uscita](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule). Attualmente non è possibile creare regole in uscita dal portale di Azure.Currently it is not possible to create outbound rules from the Azure portal. È possibile creare regole in uscita con [l'interfaccia della riga di comando](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)di Azure.  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Creare regole del gruppo Sicurezza di rete per limitare l'accesso a punti di fine pubblici specifici. Se è presente un gruppo di sicurezza di rete esistente, è possibile modificarlo. The example below shows how to enable access to the Azure management API: 
   1. Passare al gruppo di sicurezza di rete
   1. Fare clic su Regole di sicurezza in uscita
   1. Aggiungere una regola a **Nega** tutti gli accessi in uscita a **Internet**.
   1. Aggiungere una regola a **Consenti** accesso ad **AzureCloud**, con priorità inferiore alla priorità della regola per negare l'accesso a Internet.


   Le regole di sicurezza in uscita sono simili alle regole di sicurezza:The outbound security rules would look like: 

   ![Connessione in uscita con Second Load Balancer con IP pubblicoOutbound connection with Second Load Balancer with public IP](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Per altre informazioni sui gruppi di sicurezza di rete di Azure, vedere Gruppi di [sicurezza. ](https://docs.microsoft.com/azure/virtual-network/security-overview) 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Firewall di Azure per le connessioni in uscita a InternetAzure Firewall for outbound connections to internet

Un'altra opzione per ottenere la connettività in uscita agli endpoint pubblici, senza consentire la connettività in ingresso alla macchina virtuale da endpoint pubblici, è con Firewall di Azure.Another option to achieve outbound connectivity to public end points, without allowing inbound connectivity to the VM from public end points, is with Azure Firewall. Firewall di Azure è un servizio gestito, con disponibilità elevata integrata e può estendersi su più zone di disponibilità.  
Sarà inoltre necessario distribuire Route definita dall'utente, associata alla subnet in cui vengono distribuite le macchine virtuali e il servizio di bilanciamento del carico di Azure, che puntano al firewall di Azure, per instradare il traffico attraverso il firewall di Azure.You will also need to deploy [User Defined Route](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes), associated with subnet where VMs and the Azure load balancer are deployed, pointing to the Azure firewall, to route through the Azure Firewall.  
Per informazioni dettagliate su come distribuire Firewall di Azure, vedere [Distribuire e configurare Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)di Azure.  

L'architettura avrà un aspetto analogo al seguente:

![Connessione in uscita con Firewall di AzureOutbound connection with Azure Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Considerazioni importanti

- Il firewall di Azure è un servizio nativo cloud, con disponibilità elevata integrata e supporta la distribuzione zonale.
- Richiede una subnet aggiuntiva che deve essere denominata AzureFirewallSubnet.Requires additional subnet that must be named AzureFirewallSubnet. 
- Se il trasferimento di set di dati di grandi dimensioni in uscita dalla rete virtuale in cui si trovano le macchine virtuali SAP, a una macchina virtuale in un'altra rete virtuale o all'asta pubblico, potrebbe non essere una soluzione conveniente. Un esempio di questo tipo è la copia di backup di grandi dimensioni tra reti virtuali. Per informazioni dettagliate, vedere Prezzi di Firewall di Azure.For details see Azure Firewall pricing.  
- Se la soluzione Firewall aziendale non è Firewall di Azure e si dispone di requisiti di sicurezza per il passaggio di tutto il traffico in uscita tramite la soluzione aziendale centralizzata, questa soluzione potrebbe non essere pratica.  

>[!TIP]
>Se possibile, usare [i tag del servizio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) per ridurre la complessità delle regole di Firewall di Azure.Where possible, use Service tags to reduce the complexity of the Azure Firewall rules.  

### <a name="deployment-steps"></a>Passaggi di distribuzione

1. I passaggi di distribuzione presuppongono che la rete virtuale e la subnet siano già definite per le macchine virtuali.  
2. Creare Subnet AzureFirewallSubnet nella stessa rete virtuale, in cui vengono distribuiti vmS e Load Balancer Standard.Create Subnet **AzureFirewallSubnet** in the same Virtual Network, where the VMS and the Standard Load Balancer are deployed.  
   1. Nel portale di Azure passare alla rete virtuale: fare clic su Tutte le risorse, Cercare la rete virtuale, fare clic sulla rete virtuale, selezionare Subnet.In Azure portal, Navigate to the Virtual Network: Click All Resources, Search for the Virtual Network, click on the Virtual Network, Select Subnets.  
   1. Fare clic su Aggiungi subnet. Immettere **AzureFirewallSubnet** come nome. Immettere l'intervallo di indirizzi appropriato. Salvare.  
3. Creare firewall di Azure.Create Azure Firewall.  
   1. Nel portale di Azure selezionare Tutte le risorse fare clic su Aggiungi, Firewall, Crea.In Azure portal select All resources, click Add, Firewall, Create. Selezionare Gruppo di risorse (selezionare lo stesso gruppo di risorse, in cui si trova la rete virtuale).  
   1. Immettere il nome per la risorsa Firewall di Azure.Enter name for the Azure Firewall resource. Ad esempio, **MyAzureFirewall**.  
   1. Selezionare Area e selezionare almeno due zone di disponibilità, allineate con le zone di disponibilità in cui sono distribuite le macchine virtuali.  
   1. Selezionare la rete virtuale in cui vengono distribuite le macchine virtuali SAP e il servizio di bilanciamento del carico Standard di Azure.Select your Virtual Network, where the SAP VMs and Azure Standard Load balancer are deployed.  
   1. Indirizzo IP pubblico: fare clic su crea e immettere un nome. Ad esempio **MyFirewallPublicIP**.  
4. Creare la regola del firewall di Azure per consentire la connettività in uscita agli endpoint pubblici specificati. L'esempio mostra come consentire l'accesso all'endpoint pubblico dell'API di gestione di Azure.The example shows how to allow access to the Azure Management API public endpoint.  
   1. Selezionare Regole, Raccolta regole di rete, quindi fare clic su Aggiungi raccolta regole di rete.  
   1. Nome: **MyOutboundRule**, immettere Priorità, Selezionare Azione **Consenti**.  
   1. Servizio: nome **ToAzureAPI**.  Protocollo: selezionare **Qualsiasi**. Indirizzo di origine: immettere l'intervallo per la subnet, in cui vengono distribuite le macchine virtuali e il servizio di bilanciamento del carico Standard: **11.97.0.0/24**. Porte di <b>*</b>destinazione: immettere .  
   1. Salvare
   1. Quando si è ancora posizionati sul firewall di Azure, selezionare Panoramica.As you are still positioned on the Azure Firewall, Select Overview. Prendere nota dell'indirizzo IP privato del firewall di Azure.Note down the Private IP Address of the Azure Firewall.  
5. Creare una route a Firewall di AzureCreate route to Azure Firewall  
   1. Nel portale di Azure selezionare Tutte le risorse, quindi fare clic su Aggiungi, Route Table, Crea.In Azure portal select All resources, then click Add, Route Table, Create.  
   1. Immettere Nome MyRouteTable, selezionare Sottoscrizione, Gruppo di risorse e Percorso (corrispondente al percorso della rete virtuale e al firewall).  
   1. Salvare  

   La regola del firewall ![sarà simile a: Connessione in uscita con Firewall di AzureThe firewall rule would look like: Outbound connection with Azure Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Creare una route definita dall'utente dalla subnet delle macchine virtuali all'IP privato di **MyAzureFirewall**.
   1. Quando si è posizionati nella tabella cicli di lavorazione, fare clic su Cicli di lavorazione. Selezionare Aggiungi. 
   1. Nome route: ToMyAzureFirewall, Prefisso indirizzo: **0.0.0.0/0**. Tipo di hop successivo: selezionare Virtual Appliance.Next hop type: Select Virtual Appliance. Indirizzo hop successivo: immettere l'indirizzo IP privato del firewall configurato: **11.97.1.4**.  
   1. Salvare

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Uso del proxy per le chiamate Pacemaker all'API di gestione di AzureUsing Proxy for Pacemaker calls to Azure Management API

È possibile usare il proxy per consentire le chiamate Pacemaker all'endpoint pubblico dell'API di gestione di Azure.You could use proxy to allow Pacemaker calls to the Azure management API public end point.  

### <a name="important-considerations"></a>Considerazioni importanti

  - Se è già presente un proxy aziendale, è possibile instradare le chiamate in uscita agli endpoint pubblici. Le chiamate in uscita agli endpoint pubblici passeranno attraverso il punto di controllo aziendale.  
  - Verificare che la configurazione del proxy consenta la connettività in uscita all'API di gestione di Azure:Make sure the proxy configuration allows outbound connectivity to Azure management API:`https://management.azure.com`  
  - Assicurarsi che sia presente una route dalle macchine virtuali al proxy  
  - Proxy gestirà solo le chiamate HTTP/HTTPS. Se è necessario effettuare chiamate in uscita verso l'estremità pubblica su protocolli diversi (ad esempio RFC), sarà necessaria una soluzione alternativa  
  - La soluzione Proxy deve essere altamente disponibile, per evitare instabilità nel cluster Pacemaker  
  - A seconda del percorso del proxy, può introdurre latenza aggiuntiva nelle chiamate dall'agente di Azure Fence all'API di gestione di Azure.Depending on the location of the proxy, it may introduce additional latency in the calls from the Azure Fence Agent to the Azure Management API. Se il proxy aziendale è ancora in locale, mentre il cluster Pacemaker è in Azure, misurare la latenza e considerare se questa soluzione è adatta alle proprie esigenze  
  - Se non è già disponibile il proxy aziendale, non è consigliabile utilizzare questa opzione in quanto il cliente incorrerebbe in costi e complessità aggiuntivi. Tuttavia, se si decide di distribuire una soluzione proxy aggiuntiva, allo scopo di consentire la connettività in uscita da Pacemaker all'API pubblica di Gestione di Azure, assicurarsi che il proxy sia a disponibilità elevata e che la latenza delle macchine virtuali al proxy sia bassa.  

### <a name="pacemaker-configuration-with-proxy"></a>Configurazione Pacemaker con Proxy 

Ci sono molte diverse opzioni Proxy disponibili nel settore. Le istruzioni dettagliate per la distribuzione del proxy non rientrano nell'ambito di questo documento. Nell'esempio seguente si presuppone che il proxy risponda a **MyProxyService** e ascolti la porta **MyProxyPort**.  
Per consentire al pacemaker di comunicare con l'API di gestione di Azure, eseguire i passaggi seguenti in tutti i nodi del cluster:To allow pacemaker to communicate with the Azure management API, perform the following steps on all cluster nodes:  

1. Modificare il file di configurazione pacemaker /etc/sysconfig/pacemaker e aggiungere le seguenti righe (tutti i nodi del cluster):

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Riavviare il servizio pacemaker in **tutti i** nodi del cluster.  
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

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come configurare Pacemaker in SUSE in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Scopri come configurare Pacemaker su Red Hat in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
