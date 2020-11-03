---
title: Esercitazione - Distribuire e configurare VMware HCX
description: Informazioni su come distribuire e configurare una soluzione VMware HCX per il cloud privato della soluzione Azure VMware.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 424abeef567d88f7de37f7a7a4ab7a7a8b6ef3bc
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791410"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Distribuire e configurare VMware HCX

Questo articolo illustra in modo dettagliato le procedure necessarie per distribuire e configurare l'istanza locale di VMware HCX Connector per il cloud privato della soluzione Azure VMware. Con VMware HCX è possibile eseguire la migrazione dei carichi di lavoro VMware alla soluzione Azure VMware e ad altri siti connessi tramite diversi tipi di migrazione. Poiché la soluzione Azure VMware distribuisce e configura HCX Cloud Manager, è necessario scaricare, attivare e configurare HCX Connector nel data center VMware locale.

VMware HCX Advanced Connector è predistribuito nella soluzione Azure VMware. Supporta fino a tre connessioni al sito (da locale al cloud o da cloud a cloud). Se servono più di tre connessioni al sito, inviare una [richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support) per abilitare il componente aggiuntivo [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/). Il componente aggiuntivo è attualmente disponibile in anteprima. 

>[!NOTE]
>VMware HCX Enterprise Edition è disponibile con la soluzione Azure VMware come servizio in anteprima. È gratuito e soggetto alle condizioni per un servizio in anteprima. Dopo il passaggio del servizio VMware HCX Enterprise Edition alla disponibilità generale, si riceverà un preavviso di 30 giorni relativo al passaggio alla fatturazione. Si potrà anche disattivare o rifiutare esplicitamente il servizio. Si noti che attualmente non è disponibile un semplice percorso di downgrade da HCX Enterprise a HCX Advanced e i clienti che scelgono di eseguire il downgrade dovranno ripetere la distribuzione con conseguenti tempi di inattività.

Esaminare innanzitutto con attenzione le sezioni [Prima di iniziare](#before-you-begin), [Requisiti della versione software](#software-version-requirements) e [Prerequisiti](#prerequisites) di questo articolo. 

Vengono poi illustrate tutte le procedure necessarie per:

> [!div class="checklist"]
> * Distribuire il file OVA di VMware HCX Connector in locale.
> * Attivare VMware HCX Connector.
> * Associare l'istanza locale di HCX Connector all'appliance HCX Cloud Manager della soluzione Azure VMware.
> * Configurare l'interconnessione (profilo di rete, profilo di calcolo e mesh di servizi).
> * Completare la configurazione controllando lo stato dell'appliance e verificando che la migrazione sia possibile.

Al termine, sarà possibile seguire i passaggi successivi consigliati alla fine dell'articolo.  

## <a name="before-you-begin"></a>Prima di iniziare
   
* Esaminare la [serie di esercitazioni](tutorial-network-checklist.md) di base sul data center software-defined della soluzione Azure VMware.
* Esaminare e fare riferimento alla [documentazione su VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) che include il manuale dell'utente di HCX.
* Vedere la documentazione di VMware sulla [migrazione delle macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Esaminare facoltativamente le [considerazioni sulla distribuzione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Esaminare facoltativamente i materiali di VMware correlati in HCX, ad esempio la [serie di blog](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) su VMware vSphere. 
* Richiedere facoltativamente un'attivazione di HCX Enterprise per la soluzione Azure VMware tramite i canali di supporto della soluzione Azure VMware.
* Facoltativamente, [verificare le porte di rete necessarie per HCX](https://ports.vmware.com/home/VMware-HCX).
* Benché l'appliance HCX Cloud Manager della soluzione Azure VMware sia preconfigurata dalla rete /22 fornita per il cloud privato della soluzione Azure VMware, l'istanza locale di HCX Connector richiede l'allocazione di intervalli di rete dalla rete locale. Tali reti e intervalli sono illustrati più avanti in questo articolo.

Il dimensionamento dei carichi di lavoro rispetto alle risorse di calcolo e di archiviazione è un passaggio essenziale per la pianificazione. Eseguire la procedura di dimensionamento nell'ambito della pianificazione iniziale di un ambiente cloud privato. 

È possibile dimensionare i carichi di lavoro completando una [valutazione della soluzione Azure VMware](../migrate/how-to-create-azure-vmware-solution-assessment.md) nel portale di Azure Migrate.

## <a name="software-version-requirements"></a>Requisiti della versione software

I componenti dell'infrastruttura devono eseguire la versione minima richiesta. 
                                                         
| Tipo di componente    | Requisiti dell'ambiente di origine    | Requisiti dell'ambiente di destinazione   |
| --- | --- | --- |
| Server vCenter   | 5.1<br/><br/>Se si usa la versione 5.5 U1 o precedente, usare l'interfaccia utente di HCX autonoma per le operazioni di HCX.  | 6.0 U2 e versioni successive   |
| ESXi   | 5.0    | ESXi 6.0 e versioni successive   |
| NSX    | Per l'estensione di rete HCX dei commutatori logici nell'origine: NSXv 6.2+ o NSX-T 2.4+.   | NSXv 6.2+ o NSX-T 2.4+<br/><br/>Per HCX Proximity Routing: NSXv 6.4+. Il routing di prossimità non è supportato con NSX-T. |
| vCloud Director   | Non obbligatorio. Nessuna interoperabilità con vCloud Director nel sito di origine. | In caso di integrazione dell'ambiente di destinazione con vCloud Director, la versione minima è 9.1.0.2.  |

## <a name="prerequisites"></a>Prerequisiti

### <a name="network-and-ports"></a>Rete e porte

* Configurare il servizio [Copertura globale ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) tra i circuiti locali e i circuiti ExpressRoute del data center software-defined per la soluzione Azure VMware.

* È necessario che [tutte le porte obbligatorie](https://ports.vmware.com/home/VMware-HCX) siano aperte per la comunicazione tra i componenti locali e il data center software-defined della soluzione Azure VMware.

Per altre informazioni, vedere la [documentazione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html).


### <a name="ip-addresses"></a>Indirizzi IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Distribuire il file OVA di VMware HCX Connector in locale

> [!NOTE]
> Prima di distribuire l'appliance virtuale nell'istanza locale di vCenter, occorre scaricare il file OVA di VMware HCX Connector. 

1. Aprire una finestra del browser, accedere a HCX Cloud Manager della soluzione Azure VMware in `https://x.x.x.9` sulla porta 443 con le credenziali dell'utente **cloudadmin** e quindi passare a **Support** (Supporto).

   > [!TIP]
   > Prendere nota dell'indirizzo IP di HCX Cloud Manager nella soluzione Azure VMware. Per identificare l'indirizzo IP, nel riquadro della soluzione Azure VMware passare a **Manage (Gestisci)**  > **Connectivity (Connettività)** e quindi selezionare la scheda **HCX**. 
   >
   >La password di vCenter è stata definita durante la configurazione del cloud privato.

1. Selezionare il collegamento **download** per scaricare il file OVA di VMware HCX Connector.

1. Passare all'istanza locale di vCenter. Selezionare un modello OVF, ovvero il file OVA scaricato, per distribuire HCX Connector nell'istanza locale di vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Screenshot che mostra come passare a un modello OVF." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selezionare un nome e un percorso, quindi selezionare una risorsa o un cluster in cui distribuire HCX Connector. Quindi, esaminare i dettagli e le risorse necessarie.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Screenshot della sezione di revisione dei dettagli del modello." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Esaminare le condizioni di licenza. Se si accettano, selezionare le risorse di archiviazione e di rete necessarie e infine selezionare **Next** (Avanti).

1. In **Customize template** (Personalizza modello) immettere tutte le informazioni necessarie. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Screenshot delle caselle relative alla personalizzazione di un modello." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Selezionare **Next** (Avanti), verificare la configurazione, quindi selezionare **Finish** (Fine) per distribuire il file OVA di HCX Connector.
     
   > [!NOTE]
   > In genere, il servizio VMware HCX Connector che si sta distribuendo ora viene distribuito nella rete di gestione del cluster.  
   
   > [!IMPORTANT]
   > Potrebbe essere necessario accendere manualmente l'appliance virtuale.  In tal caso, attendere 10-15 minuti prima di procedere con il passaggio successivo.

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Distribuzione dell'appliance HCX](https://www.youtube.com/embed/BwSnQeefnso). 


## <a name="activate-vmware-hcx"></a>Attivare VMware HCX

Dopo aver distribuito il file OVA di VMware HCX Connector in locale e aver avviato l'appliance, è possibile eseguire l'attivazione. Prima di tutto occorre ottenere un codice di licenza dal portale della soluzione Azure VMware.

1. Nel portale della soluzione Azure VMware passare a **Manage (Gestisci)**  > **Connectivity (Connettività)** , selezionare la scheda **HCX** e infine selezionare **Add** (Aggiungi).

1. Usare le credenziali di **amministratore** per accedere all'istanza locale di VMware HCX Manager all'indirizzo `https://HCXManagerIP:9443`. 

   > [!IMPORTANT]
   > Assicurarsi di includere il numero di porta `9443` con l'indirizzo IP di VMware HCX Manager.

1. In **Licensing** (Licenze) immettere la chiave per **HCX Advanced Key** (Chiave avanzata di HCX).  
   
    > [!NOTE]
    > VMware HCX Manager deve avere un accesso a Internet aperto o un proxy configurato.

1. In **Datacenter Location** (Località del data center) specificare la località più vicina in cui installare VMware HCX Manager in locale.

1. In **System Name** (Nome di sistema) modificare il nome o accettare l'impostazione predefinita.
   
1. Selezionare **Yes, Continue** (Sì, continua).
    
1. In **Connect your vCenter** (Connettere vCenter) specificare il nome di dominio completo e l'indirizzo IP del server vCenter e le credenziali appropriate e quindi selezionare **Continue** (Continua).
   
1. In **Configure SSO/PSC** (Configura SSO/PSC) specificare il nome di dominio completo o l'indirizzo IP di Platform Services Controller e quindi selezionare **Continue** (Continua).
   
   >[!NOTE]
   >In genere questa voce corrisponde al nome di dominio completo o all'indirizzo IP di vCenter.

1. Verificare che tutti gli input siano corretti e quindi selezionare **Restart** (Riavvia).
    
   > [!NOTE]
   > Si noterà un ritardo dopo il riavvio prima che venga richiesto il passaggio successivo.

Dopo il riavvio dei servizi è essenziale che vCenter venga mostrato in verde nella schermata visualizzata. vCenter e SSO devono avere i parametri di configurazione appropriati, che dovrebbero corrispondere a quelli della schermata precedente.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Screenshot del dashboard con lo stato di vCenter verde." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Attivare HCX](https://www.youtube.com/embed/BkAV_TNYxdE).


## <a name="configure-the-vmware-hcx-connector"></a>Configurare VMware HCX Connector

A questo punto occorre aggiungere un'associazione del sito, creare un profilo di rete e di calcolo e abilitare servizi come la migrazione, l'estensione di rete o il ripristino di emergenza. 

### <a name="add-a-site-pairing"></a>Aggiungere un'associazione del sito

È possibile connettere (associare) VMware HCX Cloud Manager nella soluzione Azure VMware con VMware HCX Connector nel data center. 

1. Accedere all'istanza locale di vCenter e in **Home** selezionare **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Screenshot del client vCenter con HCX selezionato tra i collegamenti." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. In **Infrastructure** (Infrastruttura) selezionare **Site Pairing** (Associazione del sito), quindi selezionare l'opzione **Connect To Remote Site** (Connetti al sito remoto) al centro della schermata. 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Screenshot delle opzioni da selezionare per creare un sito remoto." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Immettere l'URL o l'indirizzo IP di HCX remoto annotato in precedenza, il nome utente cloudadmin@vsphere.local e la password della soluzione Azure VMware. Selezionare **Connetti**.

   > [!NOTE]
   > Per stabilire correttamente un'associazione del sito, HCX Connector deve essere in grado di eseguire il routing all'indirizzo IP di HCX Cloud Manager sulla porta 443.
   >
   > La password è la stessa usata per accedere a vCenter. Questa password è stata definita nella schermata di distribuzione iniziale.

   Viene visualizzata una schermata che mostra la connessione (associazione) tra l'appliance HCX Cloud Manager nella soluzione Azure VMware e l'istanza locale di HCX Connector.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Screenshot che mostra l'associazione tra HCX Cloud Manager nella soluzione Azure VMware e HCX Connector.":::

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Associazione del sito di VMware HCX](https://www.youtube.com/embed/sKizDCRHOko).



### <a name="create-network-profiles"></a>Creare profili di rete

VMware HCX distribuisce un subset di appliance virtuali (automatizzate) che richiedono più segmenti IP. Quando si creano i profili di rete, si definiscono i segmenti IP che sono stati identificati durante la [fase di preparazione e pianificazione pre-distribuzione dei segmenti di rete di VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Si creeranno quattro profili di rete:

   - Gestione
   - vMotion
   - Replica
   - Uplink

1. In **Infrastructure** (Infrastruttura) selezionare **Interconnect (Interconnessione)**  > **Multi-Site Service Mesh (Mesh del servizio multisito)**  > **Network Profiles (Profili di rete)**  > **Create Network Profile (Crea profilo di rete)** .

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Screenshot delle opzioni da selezionare per iniziare a creare un profilo di rete." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Per ogni profilo di rete selezionare la rete e il gruppo di porte, specificare un nome e creare il pool IP per tale segmento. Selezionare quindi **Crea**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Screenshot dei dettagli di un nuovo profilo di rete.":::

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Profilo di rete HCX](https://www.youtube.com/embed/NhyEcLco4JY).


### <a name="create-a-compute-profile"></a>Creare un profilo di calcolo

1. Selezionare **Compute Profiles (Profili di calcolo)**  > **Create Compute Profile (Crea profilo di calcolo)** .

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Screenshot che mostra le opzioni da selezionare per iniziare a creare un profilo di calcolo." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Immettere un nome per il profilo e selezionare **Continue** (Continua).  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Screenshot che mostra il nome di un profilo di calcolo e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selezionare i servizi da abilitare, come la migrazione, l'estensione di rete o il ripristino di emergenza, e quindi selezionare **Continue** (Continua).
  
   > [!NOTE]
   > In genere, non viene apportata alcuna modifica in questo passaggio.

1. In **Select Service Resources** (Seleziona risorse del servizio) selezionare una o più risorse del servizio (cluster) per abilitare i servizi VMware HCX selezionati.  

1. Quando i cluster vengono visualizzati nel data center locale, selezionare **Continue** (Continua).

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Screenshot che mostra le risorse di servizio selezionate e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. In **Select Datastore** (Seleziona archivio dati) selezionare la risorsa di archiviazione dell'archivio dati per la distribuzione delle appliance VMware HCX Interconnect. Selezionare quindi **Continua**.

   Quando vengono selezionate più risorse, VMware HCX usa la prima risorsa selezionata fino a quando non ne viene esaurita la capacità.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Screenshot che mostra una risorsa di archiviazione dati selezionata e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. In **Select Management Network Profile** (Seleziona il profilo di rete di gestione) selezionare il profilo di rete di gestione creato nei passaggi precedenti. Selezionare quindi **Continua**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Screenshot che mostra la selezione di un profilo di rete di gestione e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Il profilo di rete di gestione consente alle appliance VMware HCX di comunicare con vCenter e di raggiungere gli host ESXi.

1. In **Select Uplink Network Profile** (Seleziona profilo di rete uplink) selezionare il profilo di rete uplink creato nei passaggi precedenti. Selezionare quindi **Continua**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Screenshot che mostra la selezione di un profilo di rete uplink e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. In **Select vMotion Network Profile** (Seleziona profilo di rete vMotion) selezionare il profilo di rete vMotion creato nei passaggi precedenti. Selezionare quindi **Continua**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Screenshot che mostra la selezione di un profilo di rete vMotion e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. In **Select vSphere Replication Network Profile** (Seleziona profilo di rete di replica vSphere) selezionare il profilo di rete di replica creato nei passaggi precedenti. Selezionare quindi **Continua**.

   Nella maggior parte dei casi il profilo di rete di replica corrisponde al profilo di rete di gestione.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Screenshot che mostra la selezione di un profilo di rete di replica e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. In **Select Distributed Switches for Network Extensions** (Seleziona commutatori distribuiti per le estensioni di rete) selezionare i commutatori virtuali distribuiti che contengono le macchine virtuali di cui deve essere eseguita la migrazione alla soluzione Azure VMware in una rete estesa di livello 2. Selezionare quindi **Continua**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Screenshot che mostra la selezione di commutatori virtuali distribuiti e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Esaminare le regole di connessione e selezionare **Continue** (Continua).  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Screenshot che mostra le regole di connessione e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selezionare **Finish** (Fine) per creare il profilo di calcolo.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Screenshot che mostra le informazioni sul profilo di calcolo." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Profilo di calcolo](https://www.youtube.com/embed/qASXi5xrFzM).

### <a name="create-a-service-mesh"></a>Creare una rete mesh di servizi

A questo punto occorre configurare una rete mesh di servizi tra l'istanza locale e il data center software-defined della soluzione Azure VMware.

   > [!NOTE]
   > Per stabilire correttamente una rete mesh di servizi con la soluzione Azure VMware:
   >
   > Devono essere aperte le porte UDP 500/4500 tra gli indirizzi del profilo di rete uplink definiti dall'istanza locale di HCX Connector e gli indirizzi del profilo di rete uplink di HCX Cloud Manager della soluzione Azure VMware.
   >
   > Vedere l'elenco delle [porte HCX necessarie](https://ports.vmware.com/home/VMware-HCX).

1. In **Infrastructure** (Infrastruttura) selezionare **Interconnect (Interconnessione)**  > **Service Mesh (Mesh del servizio)**  > **Create Service Mesh (Crea mesh del servizio)** .    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Screenshot delle opzioni da selezionare per iniziare a creare una rete mesh di servizi." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Esaminare i siti già popolati e quindi selezionare **Continue** (Continua). 

   >[!NOTE]
   >Se questa è la prima configurazione di mesh del servizio, non sarà necessario modificare questa schermata.  

1. Selezionare i profili di calcolo di origine e remoti dall'elenco a discesa e quindi selezionare **Continue** (Continua).  

   Le selezioni definiscono le risorse in cui le VM possono utilizzare i servizi di VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Screenshot che mostra la selezione del profilo di calcolo di origine." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Screenshot che mostra la selezione del profilo di calcolo remoto." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Verificare i servizi che verranno abilitati e quindi selezionare **Continue** (Continua).  

1. In **Advanced Configuration - Override Uplink Network profiles** (Configurazione avanzata - Override dei profili di rete uplink) selezionare **Continue** (Continua).  

   I profili di rete uplink si connettono alla rete attraverso la quale è possibile raggiungere le appliance di interconnessione del sito remoto.  
  
1. In **Advanced Configuration - Network Extension Appliance Scale Out** (Configurazione avanzata - Aumento appliance estensione di rete) esaminare le opzioni disponibili e selezionare **Continue** (Continua). 

1. In **Advanced Configuration - Traffic Engineering** (Configurazione avanzata - Progettazione traffico) rivedere le opzioni e apportare eventuali modifiche necessarie, quindi selezionare **Continue** (Continua).

1. Esaminare l'anteprima della topologia e selezionare **Continue** (Continua).

1. Immettere un nome descrittivo per la mesh del servizio e selezionare **Finish** (Fine) per completare l'operazione.  

1. Selezionare **View Tasks** (Visualizza attività) per monitorare la distribuzione. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Screenshot che mostra il pulsante per la visualizzazione delle attività.":::

   Al termine della distribuzione della rete mesh di servizi, i servizi verranno visualizzati in verde.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Screenshot che mostra gli indicatori verdi per i servizi." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verificare l'integrità della rete mesh di servizi controllando lo stato dell'appliance. 
1. Selezionare **Interconnect (Interconnessione)**  > **Appliances (Appliance)** .

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Screenshot che mostra le opzioni da selezionare per verificare lo stato dell'appliance." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Mesh di servizi](https://www.youtube.com/embed/FyZ0d3P_T24).



### <a name="optional-create-a-network-extension"></a>(Facoltativo) Creare un'estensione di rete

Se si vogliono estendere reti dall'ambiente locale alla soluzione Azure VMware, seguire questa procedura:

1. In **Services** (Servizi) selezionare **Network Extension** (Estensione di rete) e quindi selezionare **Create a Network Extension** (Crea un'estensione di rete).

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Screenshot che mostra le opzioni da selezionare per iniziare a creare un'estensione di rete." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selezionare ogni rete da estendere alla soluzione Azure VMware e quindi selezionare **Next** (Avanti).

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Screenshot che mostra la selezione di una rete.":::

1. Immettere l'IP del gateway locale per ogni rete da estendere e quindi selezionare **Submit** (Invia). 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Screenshot che mostra un indirizzo IP del gateway.":::

   Per completare l'estensione della rete sono necessari alcuni minuti. Al termine, lo stato cambierà in **Extension complete** (Estensione completata).

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Screenshot che mostra lo stato completato dell'estensione." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Per una panoramica completa di questo passaggio, guardare il video [Soluzione Azure VMware: Estensione di rete](https://www.youtube.com/embed/cNlp0f_tTr0).


## <a name="next-steps"></a>Passaggi successivi

Se è stato raggiunto questo punto della procedura e lo stato del tunnel di interconnessione dell'appliance è **UP** (Attivo) e verde, è possibile eseguire la migrazione e proteggere le VM della soluzione Azure VMware con VMware HCX. La soluzione Azure VMware supporta le migrazioni dei carichi di lavoro con o senza un'estensione di rete. È comunque possibile eseguire la migrazione dei carichi di lavoro nell'ambiente vSphere, creare reti in locale e distribuire VM in tali reti.  

Per altre informazioni sull'uso di HCX, vedere la documentazione tecnica di VMware:

* [Documentazione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Migrazione delle macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Porte HCX necessarie](https://ports.vmware.com/home/VMware-HCX)
