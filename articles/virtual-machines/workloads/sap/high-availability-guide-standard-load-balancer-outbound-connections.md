---
title: Connettività degli endpoint pubblici per le macchine virtuali con Load Balancer Standard di Azure in scenari a disponibilità elevata di SAP
description: Connettività degli endpoint pubblici per le macchine virtuali con Load Balancer Standard di Azure in scenari a disponibilità elevata di SAP
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
ms.date: 10/28/2019
ms.author: radeltch
ms.openlocfilehash: ae2fb4c13633fa2ac22510a98e193bd9f01efb12
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73045382"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Connettività degli endpoint pubblici per le macchine virtuali con Load Balancer Standard di Azure in scenari a disponibilità elevata di SAP

Lo scopo di questo articolo è descrivere le configurazioni che consentiranno la connettività in uscita agli endpoint pubblici. Le configurazioni sono principalmente nel contesto della disponibilità elevata con pacemaker per SUSE/RHEL.  

Se si usa pacemaker con l'agente di recinzione di Azure nella soluzione a disponibilità elevata, le macchine virtuali devono avere connettività in uscita all'API di gestione di Azure.  
L'articolo presenta diverse opzioni che consentono di selezionare l'opzione più adatta per lo scenario in uso.  

## <a name="overview"></a>Panoramica

Quando si implementa la disponibilità elevata per le soluzioni SAP tramite il clustering, è [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)uno dei componenti necessari. Azure offre due SKU del servizio di bilanciamento del carico: standard e Basic.

Azure Load Balancer standard offre alcuni vantaggi rispetto al servizio di bilanciamento del carico di base. Ad esempio, funziona nelle zone di disponibilità di Azure, offre funzionalità di monitoraggio e registrazione migliori per semplificare la risoluzione dei problemi e una latenza ridotta. La funzionalità "porte a disponibilità elevata" copre tutte le porte, ovvero non è più necessario elencare tutte le singole porte.  

Esistono alcune differenze importanti tra lo SKU Basic e lo SKU standard di Azure Load Balancer. Uno di essi è la gestione del traffico in uscita verso l'endpoint pubblico. Per il confronto completo di base rispetto al servizio di bilanciamento del carico SKU standard, vedere [Load Balancer confronto tra SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus).  
 
Quando le macchine virtuali senza indirizzi IP pubblici vengono inserite nel pool back-end del servizio di bilanciamento del carico di Azure standard (nessun indirizzo IP pubblico), non esiste alcuna connettività in uscita agli endpoint pubblici, a meno che non venga eseguita una configurazione aggiuntiva.  

Se a una macchina virtuale viene assegnato un indirizzo IP pubblico o se la macchina virtuale si trova nel pool back-end di un servizio di bilanciamento del carico con indirizzo IP pubblico, avrà connettività in uscita agli endpoint pubblici.  

I sistemi SAP spesso contengono dati aziendali riservati. Raramente è accettabile per le macchine virtuali che ospitano sistemi SAP avere indirizzi IP pubblici. Allo stesso tempo, esistono scenari che richiedono la connettività in uscita dalla macchina virtuale agli endpoint pubblici.  

Esempi di scenari, che richiedono l'accesso all'endpoint pubblico di Azure sono:  
- Uso di Azure Fence Agent come meccanismo di schermatura nei cluster Pacemaker
- Backup di Azure
- Azure Site Recovery  
- Uso del repository pubblico per l'applicazione di patch al sistema operativo
- Il flusso di dati dell'applicazione SAP può richiedere la connettività in uscita a endpoint pubblico

Se la distribuzione di SAP non richiede la connettività in uscita agli endpoint pubblici, non è necessario implementare la configurazione aggiuntiva. È sufficiente creare lo SKU standard interno Azure Load Balancer per lo scenario di disponibilità elevata, supponendo che non sia necessaria la connettività in ingresso dagli endpoint pubblici.  

> [!Note]
> Quando le macchine virtuali senza indirizzi IP pubblici vengono inserite nel pool back-end del servizio di bilanciamento del carico di Azure standard (nessun indirizzo IP pubblico), non vi sarà connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici.  
>Se le macchine virtuali hanno indirizzi IP pubblici o si trovano già nel pool back-end di Azure Load Balancer con indirizzo IP pubblico, la macchina virtuale avrà già una connettività in uscita agli endpoint pubblici.


Leggere prima i documenti seguenti:

* Load Balancer Standard di Azure
  * [Panoramica di azure Load Balancer standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) -panoramica completa del servizio di bilanciamento del carico standard di Azure, principi importanti, concetti ed esercitazioni 
  * [Connessioni in uscita in Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) : scenari su come ottenere la connettività in uscita in Azure
  * [Regole in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)del servizio di bilanciamento del carico: illustra i concetti relativi alle regole in uscita del servizio di bilanciamento del carico e come creare regole in uscita
* Firewall di Azure
  * [Panoramica del firewall di Azure](https://docs.microsoft.com/azure/firewall/overview)-Panoramica del firewall di Azure
  * [Esercitazione: distribuire e configurare il firewall di Azure:](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) istruzioni su come configurare il firewall di azure tramite portale di Azure
* [Reti virtuali-regole definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) -concetti e regole di routing di Azure  
* [Tag del servizio dei gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) : come semplificare i gruppi di sicurezza di rete e la configurazione del firewall con i tag del servizio

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Load Balancer Standard di Azure esterno aggiuntivo per le connessioni in uscita a Internet

Un'opzione per ottenere la connettività in uscita agli endpoint pubblici, senza consentire la connettività in ingresso alla macchina virtuale dall'endpoint pubblico, consiste nel creare un secondo servizio di bilanciamento del carico con indirizzo IP pubblico, aggiungere le macchine virtuali al pool back-end del secondo servizio di bilanciamento del carico e definire solo [regole in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).  
Usare i [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview) per controllare gli endpoint pubblici, accessibili per le chiamate in uscita dalla macchina virtuale.  
Per altre informazioni, vedere lo scenario 2 nelle [connessioni in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)del documento.  
La configurazione avrà un aspetto simile al seguente:  

![Controllare la connettività a endpoint pubblici con gruppi di sicurezza di rete](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Considerazioni importanti

- È possibile usare un Load Balancer pubblico aggiuntivo per più macchine virtuali nella stessa subnet per ottenere la connettività in uscita a un endpoint pubblico e ottimizzare i costi  
- Usare i [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview) per controllare quali endpoint pubblici sono accessibili dalle macchine virtuali. È possibile assegnare il gruppo di sicurezza di rete alla subnet o a ogni macchina virtuale. Laddove possibile, usare i [tag di servizio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) per ridurre la complessità delle regole di sicurezza.  
- Il servizio di bilanciamento del carico standard di Azure con un indirizzo IP pubblico e regole in uscita consente l'accesso diretto all'endpoint pubblico. Se si dispone di requisiti di sicurezza aziendali per consentire il passaggio di tutto il traffico in uscita tramite una soluzione aziendale centralizzata per il controllo e la registrazione, potrebbe non essere possibile soddisfare i requisiti di questo scenario.  

>[!TIP]
>Laddove possibile, usare i [tag di servizio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) per ridurre la complessità del gruppo di sicurezza di rete. 

### <a name="deployment-steps"></a>Passaggi di distribuzione

1. Crea servizio di bilanciamento del carico  
   1. Nella [portale di Azure](https://portal.azure.com) fare clic su tutte le risorse, Aggiungi, quindi cercare **Load Balancer**  
   1. Fare clic su **Crea** 
   1. Nome Load Balancer **MyPublicILB**  
   1. Selezionare **pubblico** come tipo, **standard** come SKU  
   1. Selezionare **Crea indirizzo IP pubblico** e specificare come nome **MyPublicILBFrondEndIP**  
   1. Selezionare la zona **ridondante** come zona di disponibilità  
   1. Fare clic su verifica e crea, quindi su Crea.  
2. Creare il pool back-end **MyBackendPoolOfPublicILB** e aggiungere le macchine virtuali.  
   1. Selezionare la rete virtuale  
   1. Selezionare le macchine virtuali e i relativi indirizzi IP e aggiungerli al pool back-end  
3. [Creare regole in uscita](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule). Attualmente non è possibile creare regole in uscita dal portale di Azure. È possibile creare regole in uscita con l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).  

   ```
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Creare regole del gruppo di sicurezza di rete per limitare l'accesso a endpoint pubblici specifici. Se è presente un gruppo di sicurezza di rete esistente, è possibile modificarlo. Nell'esempio seguente viene illustrato come consentire l'accesso solo all'API di gestione di Azure: 
   1. Passare al gruppo di sicurezza di rete
   1. Fare clic su regole di sicurezza in uscita
   1. Aggiungere una regola per **negare** l'accesso in uscita a **Internet**.
   1. Aggiungere una regola per **consentire** l'accesso a **AzureCloud**, con priorità inferiore alla priorità della regola per negare l'accesso a Internet.


   Le regole di sicurezza in uscita avranno un aspetto simile al seguente: 

   ![Connessione in uscita con la seconda Load Balancer con IP pubblico](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Per altre informazioni sui gruppi di sicurezza di rete di Azure, vedere [gruppi di sicurezza ](https://docs.microsoft.com/azure/virtual-network/security-overview). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Firewall di Azure per le connessioni in uscita a Internet

Un'altra opzione per ottenere la connettività in uscita agli endpoint pubblici, senza consentire la connettività in ingresso alla macchina virtuale da endpoint pubblici, è con il firewall di Azure. Il firewall di Azure è un servizio gestito con disponibilità elevata incorporata che può estendersi a più zone di disponibilità.  
Sarà inoltre necessario distribuire il [percorso definito dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes), associato alla subnet in cui vengono distribuite le macchine virtuali e il servizio di bilanciamento del carico di Azure, che punta al firewall di Azure, per instradare il traffico attraverso il firewall di Azure.  
Per informazioni dettagliate su come distribuire il firewall di Azure, vedere [distribuire e configurare il firewall di Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal).  

L'architettura avrà un aspetto analogo al seguente:

![Connessione in uscita con il firewall di Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Considerazioni importanti

- Il firewall di Azure è un servizio cloud nativo, con disponibilità elevata incorporata e supporta la distribuzione di zona.
- Richiede una subnet aggiuntiva che deve essere denominata AzureFirewallSubnet. 
- Se si trasferiscono set di dati di grandi dimensioni in uscita della rete virtuale in cui si trovano le VM SAP, in una macchina virtuale in un'altra rete virtuale o in un endpoint pubblico, potrebbe non essere una soluzione conveniente. Uno di questi esempi è la copia di backup di grandi dimensioni nelle reti virtuali. Per informazioni dettagliate, vedere prezzi di Azure firewall.  
- Se la soluzione firewall aziendale non è un firewall di Azure e si dispone di requisiti di sicurezza per il passaggio di tutto il traffico in uscita anche se una soluzione aziendale centralizzata, questa soluzione potrebbe non essere pratica.  

>[!TIP]
>Laddove possibile, usare i [tag di servizio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) per ridurre la complessità delle regole del firewall di Azure.  

### <a name="deployment-steps"></a>Passaggi di distribuzione

1. La procedura di distribuzione presuppone che sia già stata definita la rete virtuale e la subnet per le macchine virtuali.  
2. Creare una subnet **AzureFirewallSubnet** nella stessa rete virtuale in cui vengono distribuite le macchine virtuali e i Load Balancer standard.  
   1. In portale di Azure passare alla rete virtuale: fare clic su tutte le risorse, cercare la rete virtuale, fare clic sulla rete virtuale e selezionare subnet.  
   1. Fare clic su Aggiungi subnet. Immettere **AzureFirewallSubnet** come nome. Immettere l'intervallo di indirizzi appropriato. Salvare.  
3. Creare un firewall di Azure.  
   1. In portale di Azure selezionare tutte le risorse, fare clic su Aggiungi, firewall, crea. Selezionare gruppo di risorse (selezionare lo stesso gruppo di risorse in cui si trova la rete virtuale).  
   1. Immettere il nome per la risorsa firewall di Azure. Ad esempio, **MyAzureFirewall**.  
   1. Selezionare Region (area) e selezionare almeno due zone di disponibilità, allineate con le zone di disponibilità in cui vengono distribuite le VM.  
   1. Selezionare la rete virtuale in cui vengono distribuite le macchine virtuali SAP e il servizio di bilanciamento del carico standard di Azure.  
   1. Indirizzo IP pubblico: fare clic su Crea e immettere un nome. Ad esempio **MyFirewallPublicIP**.  
4. Creare una regola del firewall di Azure per consentire la connettività in uscita agli endpoint pubblici specificati. L'esempio illustra come consentire l'accesso all'endpoint pubblico dell'API di gestione di Azure.  
   1. Selezionare regole, raccolta regole di rete, quindi fare clic su Aggiungi raccolta regole di rete.  
   1. Nome: **MyOutboundRule**, immettere Priority, selezionare azione **Consenti**.  
   1. Servizio: nome **ToAzureAPI**.  Protocollo: selezionare **any**. Indirizzo di origine: immettere l'intervallo per la subnet in cui vengono distribuite le macchine virtuali e Load Balancer Standard ad esempio: **11.97.0.0/24**. Porte di destinazione: immettere <b>*</b>.  
   1. Salva
   1. Quando si è ancora posizionati nel firewall di Azure, selezionare panoramica. Annotare l'indirizzo IP privato del firewall di Azure.  
5. Creare una route al firewall di Azure  
   1. In portale di Azure selezionare tutte le risorse, quindi fare clic su Aggiungi, tabella di route, creazione.  
   1. Immettere il nome MyRouteTable, selezionare sottoscrizione, gruppo di risorse e località (corrispondenti al percorso della rete virtuale e del firewall).  
   1. Salva  

   La regola del firewall avrà un aspetto simile al seguente: ![connessione in uscita con il firewall di Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Creare una route definita dall'utente dalla subnet delle macchine virtuali all'indirizzo IP privato di **MyAzureFirewall**.
   1. Quando si è posizionati nella tabella di route, fare clic su route. Selezionare Aggiungi. 
   1. Nome Route: ToMyAzureFirewall, prefisso Indirizzo: **0.0.0.0/0**. Tipo hop successivo: selezionare appliance virtuale. Indirizzo hop successivo: immettere l'indirizzo IP privato del firewall configurato: **11.97.1.4**.  
   1. Salva

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Uso del proxy per le chiamate pacemaker all'API di gestione di Azure

È possibile usare il proxy per consentire le chiamate pacemaker all'endpoint pubblico dell'API di gestione di Azure.  

### <a name="important-considerations"></a>Considerazioni importanti

  - Se è già presente un proxy aziendale, è possibile instradare le chiamate in uscita ai punti finali pubblici attraverso di esso. Le chiamate in uscita a endpoint pubblici passano attraverso il punto di controllo aziendale.  
  - Assicurarsi che la configurazione del proxy consenta la connettività in uscita all'API di gestione di Azure: https://management.azure.com  
  - Verificare che sia presente una route dalle macchine virtuali al proxy  
  - Il proxy gestirà solo le chiamate HTTP/HTTPS. Se è necessario eseguire le chiamate in uscita a un endpoint pubblico su protocolli diversi, ad esempio RFC, sarà necessaria una soluzione alternativa  
  - La soluzione proxy deve essere a disponibilità elevata per evitare instabilità nel cluster Pacemaker  
  - A seconda della posizione del proxy, può introdurre una latenza aggiuntiva nelle chiamate dall'agente di recinzione di Azure all'API di gestione di Azure. Se il proxy aziendale è ancora in locale, mentre il cluster Pacemaker si trova in Azure, misurare la latenza e prendere in considerazione se questa soluzione è adatta alle proprie esigenze  
  - Se non è già disponibile un proxy aziendale a disponibilità elevata, questa opzione non è consigliata perché il cliente potrebbe sostenere costi aggiuntivi e complessità. Tuttavia, se si decide di distribuire una soluzione proxy aggiuntiva, allo scopo di consentire la connettività in uscita da pacemaker all'API di gestione di Azure, assicurarsi che il proxy sia a disponibilità elevata e che la latenza dalle macchine virtuali al proxy sia bassa.  

### <a name="pacemaker-configuration-with-proxy"></a>Configurazione di pacemaker con proxy 

Nel settore sono disponibili diverse opzioni proxy. Le istruzioni dettagliate per la distribuzione del proxy esulano dall'ambito di questo documento. Nell'esempio seguente si presuppone che il proxy stia rispondendo a **MyProxyService** e ascoltando la porta **MyProxyPort**.  
Per consentire a pacemaker di comunicare con l'API di gestione di Azure, seguire questa procedura in tutti i nodi del cluster:  

1. Modificare il file di configurazione pacemaker/etc/sysconfig/pacemaker e aggiungere le righe seguenti (tutti i nodi del cluster):  
   ```
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Riavviare il servizio pacemaker in **tutti i** nodi del cluster.  
  - SUSE  
     ```
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

  - Red Hat  
     ```
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come configurare pacemaker in SUSE in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Informazioni su come configurare pacemaker su Red Hat in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
