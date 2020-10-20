---
title: Esercitazione - Distribuire e configurare VMware HCX
description: Informazioni su come distribuire e configurare una soluzione VMware HCX per il cloud privato della soluzione Azure VMware.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 58fd8b4518f60f1f736d8c19ddcf62729353f251
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057996"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Distribuire e configurare VMware HCX

Questo articolo illustra in modo dettagliato le procedure necessarie per distribuire e configurare l'istanza locale di VMware HCX Connector per il cloud privato della soluzione Azure VMware. VMware HCX consente la migrazione dei carichi di lavoro VMware alla soluzione Azure VMware e ad altri siti connessi tramite diversi tipi di migrazione. La soluzione Azure VMware ha già distribuito e configurato "Cloud Manager", quindi è necessario scaricare, attivare e configurare Connector nel proprio data center VMware locale.

VMware HCX Advanced Connector, predistribuito nella soluzione Azure VMware, supporta fino a tre connessioni al sito, da locale al cloud o da cloud a cloud. Se sono necessarie più di tre connessioni al sito, è possibile abilitare il componente aggiuntivo [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/), attualmente disponibile in *anteprima*, inviando una [richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support). VMware HCX Enterprise Edition (EE) è disponibile con la soluzione Azure VMware come funzione/servizio in *anteprima*. Durante l'*anteprima*, VMware HCX EE per la soluzione Azure VMware è una funzione/un servizio gratuito ed è soggetto alle condizioni del servizio. Dopo il passaggio del servizio VMware HCX EE alla disponibilità generale, si riceverà un preavviso di 30 giorni relativo al passaggio alla fatturazione. Sarà anche possibile disattivare/rifiutare esplicitamente il servizio.

Prima di iniziare, esaminare attentamente le sezioni [Prima di iniziare](#before-you-begin), [Requisiti della versione software](#software-version-requirements) e [Prerequisiti](#prerequisites). 

Vengono poi illustrate tutte le procedure necessarie per:

> [!div class="checklist"]
> * Distribuire il file OVA di VMware HCX Connector in locale
> * Attivare VMware HCX Connector
> * Associare l'istanza locale di HCX Connector all'appliance HCX Cloud Manager della soluzione Azure VMware
> * Configurare l'interconnessione (profilo di rete, profilo di calcolo e mesh del servizio)
> * Completare la configurazione controllando lo stato dell'appliance e verificando che la migrazione sia possibile

Al termine sarà possibile seguire i passaggi successivi consigliati alla fine dell'articolo.  

## <a name="before-you-begin"></a>Prima di iniziare
   
* Esaminare la [serie di esercitazioni](tutorial-network-checklist.md) di base sul data center software-defined (SDDC, Software Defined Datacenter) della soluzione Azure VMware.
* Esaminare e fare riferimento alla [documentazione su VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) che include il manuale dell'utente di HCX.
* Esaminare la documentazione di VMware sulla [migrazione delle macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Esaminare facoltativamente le [considerazioni sulla distribuzione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Esaminare facoltativamente i materiali di VMware correlati in HCX, ad esempio la [serie di blog](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) su VMware vSphere in HCX. 
* Richiedere facoltativamente un'attivazione di HCX Enterprise per la soluzione Azure VMware tramite i canali di supporto della soluzione Azure VMware.
* Facoltativamente, [verificare le porte di rete necessarie per HCX](https://ports.vmware.com/home/VMware-HCX).
* Mentre l'appliance HCX CLoud Manager della soluzione Azure VMware è preconfigurata dalla subnet /22 fornita per il cloud privato della soluzione Azure VMware, l'istanza locale di HCX Connector richiede l'allocazione di intervalli di rete dalla rete locale del cliente. Questi intervalli di rete sono descritti più avanti nel documento.

Il dimensionamento dei carichi di lavoro rispetto alle risorse di calcolo e di archiviazione è un passaggio essenziale per la pianificazione. È necessario eseguire il passaggio di dimensionamento come parte della pianificazione iniziale dell'ambiente del cloud privato. 

È anche possibile dimensionare i carichi di lavoro completando una [Valutazione della soluzione Azure VMware](../migrate/how-to-create-azure-vmware-solution-assessment.md) nel portale di Azure Migrate.

## <a name="software-version-requirements"></a>Requisiti della versione software

I componenti dell'infrastruttura devono eseguire la versione minima richiesta. 
                                                         
| Tipo di componente    | Requisiti dell'ambiente di origine    | Requisiti dell'ambiente di destinazione   |
| --- | --- | --- |
| Server vCenter   | 5.1<br/><br/>Se si usa la versione 5.5 U1 o precedente, usare l'interfaccia utente di HCX autonoma per le operazioni di HCX.  | 6.0 U2 e versioni successive   |
| ESXi   | 5.0    | ESXi 6.0 e versioni successive   |
| NSX    | Per HCX Network Extension dei commutatori logici nell'origine: NSXv 6.2+ o NSX-T 2.4+   | NSXv 6.2+ o NSX-T 2.4+<br/><br/>Per HCX Proximity Routing: NSXv 6.4 + (Proximity Routing non è supportato con NSX-T) |
| vCloud Director   | Non richiesto: nessuna interoperabilità con vCloud Director nel sito di origine | Quando si integra l'ambiente di destinazione con vCloud Director, la versione minima è 9.1.0.2.  |

## <a name="prerequisites"></a>Prerequisiti

### <a name="network-and-ports"></a>Rete e porte

* Servizio [Copertura globale ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) configurato tra i circuiti locali e i circuiti ExpressRoute del data center software-defined per la soluzione Azure VMware.

* È necessario che tutte le porte obbligatorie siano aperte per la comunicazione tra componenti locali e tra componenti locali e il data center software-defined della soluzione Azure VMware. Vedere la [documentazione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html).


### <a name="ip-addresses"></a>Indirizzi IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Distribuire il file OVA di VMware HCX Connector in locale

>[!NOTE]
>Prima di distribuire l'appliance virtuale nell'istanza locale di vCenter, è necessario scaricare il file OVA di VMware HCX Connector. 

1. Aprire una finestra del browser, accedere a HCX Manager della soluzione Azure VMware in `https://x.x.x.9` sulla porta 443 con le credenziali dell'utente **cloudadmin** e quindi passare a **Support** (Supporto).

   >[!TIP]
   >Prendere nota dell'IP di HCX Cloud Manager nella soluzione Azure VMware. Per identificare l'indirizzo IP di HCX Cloud Manager, nel pannello della soluzione Azure VMware passare a **Manage (Gestisci)**  > **Connectivity (Connettività)** e quindi selezionare la scheda **HCX**. 
   >
   >La password di vCenter è stata definita durante la configurazione del cloud privato.

1. Selezionare il collegamento **download** per scaricare il file OVA di VMware HCX Connector.

1. Passare all'istanza locale di vCenter e selezionare un modello OVF, ovvero il file OVA scaricato, per distribuire HCX Connector nell'istanza locale di vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selezionare un nome e una posizione, una risorsa o un cluster in cui distribuire HCX Connector e quindi esaminare i dettagli e le risorse necessarie.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Esaminare le condizioni di licenza e, in caso di accettazione, selezionare le risorse di archiviazione e di rete necessarie e infine selezionare**Next** (Avanti).

1. In **Customize template** (Personalizza modello) immettere tutte le informazioni necessarie. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Selezionare **Next** (Avanti), verificare la configurazione e quindi selezionare **Finish** (Fine) per distribuire il file OVA di HCX Connector.
     
   >[!NOTE]
   >In genere, il servizio VMware HCX Connector che si sta distribuendo ora viene distribuito nella rete di gestione del cluster.  
   
   > [!IMPORTANT]
   > Al termine della distribuzione potrebbe essere necessario accendere manualmente l'appliance virtuale.
   > Attendere 10-15 minuti dopo l'accensione dell'appliance HCX prima di procedere al passaggio successivo.

Per una panoramica completa di questo passaggio, guardare il video [Soluzione Azure VMware - Distribuzione dell'appliance VMware HCX](https://www.youtube.com/embed/BwSnQeefnso). 


## <a name="activate-vmware-hcx"></a>Attivare VMware HCX

Dopo aver distribuito il file OVA di VMware HCX Connector in locale e aver avviato l'appliance, è possibile attivarla, ma prima occorre recuperare un codice di licenza dal portale della soluzione Azure VMware in Azure.

1. Nel portale della soluzione Azure VMware passare a **Manage (Gestisci)**  > **Connectivity (Connettività)** , selezionare la scheda **HCX** e quindi selezionare **Add** (Aggiungi).

1. Accedere all'istanza locale di VMware HCX Manager in `https://HCXManagerIP:9443` con le credenziali dell'utente **admin**. 

   > [!IMPORTANT]
   > Assicurarsi di includere il numero di porta `9443` con l'indirizzo IP di VMware HCX Manager.

1. In **Licensing** (Licenze) immettere la **HCX Advanced Key** (Chiave avanzata di HCX).  
   
    > [!NOTE]
    > VMware HCX Manager deve avere un accesso a Internet aperto o un proxy configurato.

1. In **Datacenter Location** (Posizione del data center) specificare la posizione più vicina in cui si installa VMware HCX Manager in locale.

1. Modificare il **System Name** (Nome di sistema) accettare l'impostazione predefinita.
   
1. È possibile completare la procedura in un secondo momento o continuare. Selezionare l'opzione **Yes, Continue** (Sì, continua) per continuare.
    
1. In **Connect your vCenter** (Connettere vCenter) specificare il nome di dominio completo e l'indirizzo IP del server vCenter e le credenziali appropriate e quindi selezionare **Continue** (Continua).
   
1. In **Configure SSO/PSC** (Configura SSO/PSC) specificare il nome di dominio completo o l'indirizzo IP di PSC e quindi selezionare **Continue** (Continua).
   
   >[!NOTE]
   >Corrisponde in genere al nome di dominio completo/IP di vCenter.

1. Verificare che tutti gli input siano corretti e selezionare **Restart** (Riavvia).
    
   > [!NOTE]
   > Si noterà un ritardo dopo il riavvio prima che venga richiesto il passaggio successivo.
   >
   >Dopo il riavvio dei servizi è essenziale che vCenter venga mostrato in verde nella schermata visualizzata. vCenter e SSO hanno i parametri di configurazione appropriati, che dovrebbero corrispondere a quelli della schermata precedente.

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Per una panoramica completa di questo passaggio, guardare il video [Soluzione Azure VMware - Attivazione di VMware HCX](https://www.youtube.com/embed/BkAV_TNYxdE).


## <a name="configure-vmware-hcx-connector"></a>Configurare VMware HCX Connector

È ora possibile aggiungere un'associazione del sito, creare un profilo di rete e di calcolo e abilitare servizi, tra cui la migrazione, l'estensione di rete o il ripristino di emergenza. 

### <a name="add-a-site-pairing"></a>Aggiungere un'associazione del sito

È possibile connettere (associare) VMware HCX Cloud Manager nella soluzione Azure VMware con VMware HCX Connector nel data center. 

1. Accedere all'istanza locale di vCenter e in **Home** selezionare **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. In **Infrastructure** (Infrastruttura) selezionare **Site Pairing** (Associazione del sito) e quindi selezionare l'opzione **Connect To Remote Site** (Connetti al sito remoto) al centro della schermata. 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Immettere un valore per **Remote HCX URL or IP address** (URL o indirizzo IP di HCX remoto) di cui si è preso nota, il nome utente cloudadmin@vsphere.local e la **password** della soluzione Azure VMware e quindi selezionare **Connect** (Connetti).

   > [!NOTE]
   > Il valore **Remote HCX URL** (URL HCX remoto) corrisponde all'IP di HCX Cloud Manager del cloud privato della soluzione Azure VMware, ovvero in genere l'indirizzo ".9" della rete di gestione.  Se ad esempio vCenter è 192.168.4.2, l'URL di HCX sarà 192.168.4.9.
   >
   > Il valore per **password** corrisponderà alla password usata per accedere a vCenter. Questa password è stata definita nella schermata di distribuzione iniziale.

   Viene visualizzata una schermata che mostra la connessione (associazione) tra HCX Cloud Manager nella soluzione Azure VMware e l'istanza locale di HCX Connector.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter.":::

Per una panoramica completa di questo passaggio, guardare il video [Soluzione Azure VMware - Associazione del sito di VMware HCX](https://www.youtube.com/embed/sKizDCRHOko).



### <a name="create-network-profiles"></a>Creare profili di rete

VMware HCX distribuisce un subset di appliance virtuali (automatizzate) che richiedono più segmenti IP.  Quando si creano i profili di rete, si definiscono i segmenti IP, che sono stati identificati durante la [fase di preparazione e pianificazione pre-distribuzione dei segmenti di rete di VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Verranno creati quattro profili di rete:

   - Gestione
   - vMotion
   - Replica
   - Uplink

1. In **Infrastructure** (Infrastruttura) selezionare **Interconnect (Interconnessione)**  > **Multi-Site Service Mesh (Mesh del servizio multisito)**  > **Network Profiles (Profili di rete)**  > **Create Network Profile (Crea profilo di rete)** .

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Per ogni profilo di rete selezionare la rete e il gruppo di porte, specificare un nome, creare il pool IP per tale segmento specifico e quindi selezionare **Create** (Crea). 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter.":::

Per una panoramica completa di questo passaggio, guardare il video [Soluzione Azure VMware - Creare un profilo di rete di VMware HCX](https://www.youtube.com/embed/NhyEcLco4JY).


### <a name="create-compute-profile"></a>Creare un profilo di calcolo

1. Selezionare **Compute Profiles (Profili di calcolo)**  > **Create Compute Profile (Crea profilo di calcolo)** .

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Immettere un nome per il profilo e selezionare **Continue** (Continua).  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selezionare i servizi da abilitare, tra cui la migrazione, l'estensione di rete o il ripristino di emergenza, e quindi selezionare **Continue** (Continua).
  
   > [!NOTE]
   > Non verranno in genere apportate modifiche.

1. In **Select Service Resources** (Seleziona risorse del servizio) selezionare una o più risorse del servizio (cluster) da abilitare per i servizi VMware HCX selezionati.  

1. Quando i cluster vengono visualizzati nel data center locale, selezionare **Continue** (Continua).

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Da **Select Datastore** (Seleziona archivio dati) selezionare la risorsa di archiviazione dell'archivio dati per la distribuzione delle appliance VMware HCX Interconnect e quindi selezionare **Continue** (Continua).

   Quando vengono selezionate più risorse, VMware HCX usa la prima risorsa selezionata fino a quando non ne viene esaurita la capacità.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Selezionare **Management Network Profile** (Profilo di rete di gestione), ovvero il profilo di rete di gestione creato nei passaggi precedenti, e quindi selezionare **Continue** (Continua).  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Il profilo di rete di gestione consente a una o più appliance VMware HCX di comunicare con vCenter e di raggiungere gli host ESXi.

1. Selezionare **Uplink Network Profile** (Profilo di rete uplink), ovvero il profilo di rete uplink creato nei passaggi precedenti, e quindi selezionare **Continue** (Continua).

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Selezionare **vMotion Network Profile** (Profilo di rete vMotion), ovvero il profilo di rete di vMotion creato nei passaggi precedenti, e quindi selezionare **Continue** (Continua).

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Scegliere **vSphere Replication Network Profile** (Profilo di rete di replica vSphere), ovvero il profilo di rete di replica creato nei passaggi precedenti, e quindi scegliere **Continue** (Continua).

   Nella maggior parte dei casi questo profilo di rete di replica corrisponde al profilo di rete di gestione.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Selezionare **Distributed Switches for Network Extensions** (Commutatori distribuiti per le estensioni di rete), ovvero i commutatori virtuali distribuiti che contengono le macchine virtuali di cui deve essere eseguita la migrazione alla soluzione Azure VMware in una rete estesa di livello 2, e quindi selezionare **Continue** (Continua).

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Esaminare le regole di connessione e selezionare **Continue** (Continua).  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selezionare **Finish** (Fine) per creare il profilo di calcolo.

   Verrà visualizzata una schermata simile alla seguente.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Per una panoramica completa di questo passaggio, guardare il video [Soluzione Azure VMware - Creare un profilo di calcolo di VMware HCX](https://www.youtube.com/embed/qASXi5xrFzM).




### <a name="create-service-mesh"></a>Creare la mesh del servizio

È ora necessario configurare la mesh del servizio tra l'istanza locale e il data center software-defined della soluzione Azure VMware.

1. In **Infrastructure** (Infrastruttura) selezionare **Interconnect (Interconnessione)**  > **Service Mesh (Mesh del servizio)**  > **Create Service Mesh (Crea mesh del servizio)** .    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Esaminare i siti già popolati e quindi selezionare **Continue** (Continua). 

   >[!NOTE]
   >Se questa è la prima configurazione di mesh del servizio, non sarà necessario modificare questa schermata.  

1. Selezionare l'elenco a discesa per i profili di calcolo di origine e remoti e quindi selezionare **Continue** (Continua).  

   Le selezioni definiscono le risorse in cui le VM possono utilizzare i servizi di VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Esaminare i servizi che verranno abilitati e quindi selezionare **Continue** (Continua).  

1. In **Advanced Configuration - Override Uplink Network profiles** (Configurazione avanzata - Override dei profilo di rete uplink) selezionare **Continue** (Continua).  I profili di rete uplink si connettono alla rete tramite cui è possibile raggiungere le appliance di interconnessione del sito remoto.  
  
1. In **Advanced Configuration - Network Extension Appliance Scale Out** (Configurazione avanzata - Aumento appliance estensione di rete) esaminare le opzioni disponibili e selezionare **Continue** (Continua). 

1. In **Advanced Configuration - Traffic Engineering** (Configurazione avanzata - Progettazione traffico) esaminare le opzioni e apportare eventuali modifiche necessarie, quindi selezionare **Continue** (Continua).

1. Esaminare l'anteprima della topologia e selezionare **Continue** (Continua).

1. Immettere un nome descrittivo per la mesh del servizio e selezionare **Finish** (Fine) per completare l'operazione.  

1. Selezionare **View Tasks** (Visualizza attività) per monitorare la distribuzione. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter.":::

   Al termine della distribuzione della mesh del servizio, il servizio verrà visualizzato in verde.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verificare l'integrità della mesh del servizio controllando lo stato dell'appliance, quindi selezionare **Interconnect (Interconnessione)**  > **Appliances (Appliance)** .

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Per una panoramica completa di questo passaggio, guardare il video [Soluzione Azure VMware - Creare una mesh del servizio di VMware HCX](https://www.youtube.com/embed/FyZ0d3P_T24).



### <a name="optional-create-a-network-extension"></a>(Facoltativo) Creare un'estensione di rete

Se si vogliono estendere reti dall'ambiente locale alla soluzione Azure VMware, seguire questa procedura per estendere tali reti.

1. In **Services** (Servizi) selezionare **Network Extension** (Estensione di rete) e quindi selezionare **Create a Network Extension** (Crea un'estensione di rete).

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selezionare ogni rete da estendere alla soluzione Azure VMware e quindi selezionare **Next** (Avanti).

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter.":::

1. Immettere l'IP del gateway locale per ogni rete da estendere e quindi selezionare **Submit** (Invia). 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter.":::

   Per completare l'estensione della rete sono necessari alcuni minuti. Al termine dell'operazione, lo stato cambierà in **extension complete** (estensione completata).

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Passare all'istanza locale di vCenter e selezionare un modello OVF da distribuire nell'istanza locale di vCenter." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Per una panoramica completa di questo passaggio, guardare il video [Soluzione Azure VMware - Estensione di rete di VMware HCX](https://www.youtube.com/embed/cNlp0f_tTr0).


## <a name="next-steps"></a>Passaggi successivi

Se è stato raggiunto questo punto della procedura e lo stato del tunnel di interconnessione dell'appliance è **UP** (ATTIVO) e verde, è possibile eseguire la migrazione e proteggere le VM della soluzione Azure VMware usando VMware HCX.  La soluzione Azure VMware supporta le migrazioni dei carichi di lavoro, con o senza un'estensione di rete.  È comunque possibile eseguire migrazioni dei carichi di lavoro nell'ambiente vSphere, creare reti in locale e distribuire VM in tali reti.  Per altri dettagli sull'uso di HCX, vedere la [documentazione relativa a VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) e [Migrazione delle macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) nella documentazione tecnica di VMware.
