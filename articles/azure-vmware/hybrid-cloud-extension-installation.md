---
title: Installare VMware HCX
description: Configurare la soluzione VMware HCX per il cloud privato della soluzione VMware di Azure
ms.topic: how-to
ms.date: 09/24/2020
ms.openlocfilehash: a101712f2d80e0d8e70d37bd5b7b08931f62ba3d
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356554"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Installare HCX per la soluzione Azure VMware

Questo articolo illustra le procedure per la configurazione della soluzione VMWare HCX per il cloud privato della soluzione VMWare di Azure. HCX consente la migrazione dei carichi di lavoro VMware nel cloud e in altri siti connessi tramite vari tipi di migrazione supportati da HCX predefiniti.

HCX Advanced, l'installazione predefinita, supporta fino a tre connessioni al sito (in locale o da cloud a cloud). Se sono necessarie più di tre connessioni del sito, i clienti hanno la possibilità di abilitare il componente aggiuntivo HCX Enterprise tramite il supporto, che è attualmente in fase di anteprima. HCX Enterprise comporta costi aggiuntivi per i clienti oltre la disponibilità generale, ma offre [funzionalità aggiuntive](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).


Esaminare innanzitutto [Prima di iniziare](#before-you-begin), [Requisiti della versione software](#software-version-requirements) e [Prerequisiti](#prerequisites) con attenzione. 

Quindi, verranno esaminate tutte le procedure necessarie per:

> [!div class="checklist"]
> * Distribuire il HCX OVA locale (Connector)
> * Attivare e configurare HCX
> * Configurare l'uplink di rete e la mesh del servizio
> * Completare l'installazione verificando lo stato dell'appliance

Dopo aver completato l'installazione, è possibile seguire i passaggi successivi consigliati disponibili alla fine di questo articolo.  

## <a name="before-you-begin"></a>Prima di iniziare
    
* Esaminare la [serie di esercitazioni](tutorial-network-checklist.md)di base del software definito datacenter (SDDC) di Azure VMware Solution.
* Esaminare e fare riferimento alla [documentazione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) , incluso il manuale dell'utente di HCx.
* Esaminare i documenti VMware [migrazione di macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Facoltativamente, esaminare le [considerazioni sulla distribuzione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Esaminare facoltativamente i materiali di VMware correlati in HCX, ad esempio la [serie di blog](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) su VMware vSphere in HCX. 
* Richiedere una soluzione VMware di Azure HCX Enterprise Activation tramite i canali di supporto della soluzione VMware di Azure.

Il dimensionamento dei carichi di lavoro rispetto alle risorse di calcolo e di archiviazione è un passaggio di pianificazione essenziale quando si sta preparando l'uso della soluzione HCX cloud privata della soluzione VMware di Azure. Risolvere il passaggio di ridimensionamento nell'ambito della pianificazione iniziale dell'ambiente del cloud privato. 

È anche possibile ridimensionare i carichi di lavoro completando una [valutazione della soluzione VMware di Azure](../migrate/how-to-create-azure-vmware-solution-assessment.md) nel portale di Azure migrate.

## <a name="software-version-requirements"></a>Requisiti della versione software

I componenti dell'infrastruttura devono eseguire la versione minima richiesta. 
                                                         
| Tipo di componente    | Requisiti dell'ambiente di origine    | Requisiti dell'ambiente di destinazione   |
| --- | --- | --- |
| Server vCenter   | 5.1<br/><br/>Se si usa 5,5 U1 o versione precedente, usare l'interfaccia utente HCX autonoma per le operazioni di HCX.  | 6.0 U2 e versioni successive   |
| ESXi   | 5.0    | ESXi 6.0 e versioni successive   |
| NSX    | Per l'estensione di rete HCX dei commutatori logici nell'origine: NSXv 6.2 + o NSX-T 2.4 +   | NSXv 6.2+ o NSX-T 2.4+<br/><br/>Per il routing di prossimità HCX: NSXv 6.4 + (il routing di prossimità non è supportato con NSX-T) |
| vCloud Director   | Non richiesto: nessuna interoperabilità con vCloud Director nel sito di origine | Quando si integra l'ambiente di destinazione con vCloud Director, il valore minimo è 9.1.0.2.  |

## <a name="prerequisites"></a>Prerequisiti

* Copertura globale ExpressRoute devono essere configurati tra i circuiti SDDC ExpressRoute della soluzione VMware locale e Azure.

* Tutte le porte obbligatorie devono essere aperte tra l'ambiente locale e la soluzione VMware di Azure SDDC (vedere la [documentazione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Un indirizzo IP per HCX Manager in locale e un minimo di due indirizzi IP per l'appliance Interconnect (IX) ed estensione di rete (NE).

* Le appliance HCX IX e NE locali dovrebbero essere in grado di raggiungere l'infrastruttura vCenter e ESXi.

* Per distribuire l'appliance WAN Interconnect, oltre al blocco di indirizzi di rete /22 CIDR usato per la distribuzione SDDC nella portale di Azure, HCX richiede un blocco /29. Assicurarsi di fattorizzare questo requisito nella pianificazione della rete.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Distribuire VMware HCX OVA in locale

1. Accedere alla soluzione VMware di Azure HCX Manager sulla `https://x.x.x.9` porta 443 con le credenziali utente di **cloudadmin** e quindi fare clic su **supporto**.

1. Selezionare il collegamento per il download del file OVA HCX di VMware. 

1. Accedere alla soluzione VMware di Azure SDDC vCenter e selezionare **HCX**.
   
1. Passare a vCenter locale e selezionare un modello OVF da distribuire in vCenter locale.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-template.png" alt-text="Passare quindi a vCenter locale e selezionare un modello OVF per la distribuzione in vCenter locale.":::

1. Selezionare un nome e un percorso, quindi selezionare una risorsa o un cluster in cui eseguire la distribuzione di HCX. Esaminare quindi i dettagli e le risorse necessarie.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-template.png" alt-text=" Selezionare un nome e un percorso, quindi selezionare una risorsa o un cluster in cui è necessario distribuire HCX. Esaminare quindi i dettagli e le risorse necessarie.":::

1. Esaminare le condizioni di licenza e, se si accettano, selezionare l'archivio e la rete richieste. Fare quindi clic su **Avanti**.

1. In **Customize template** (Personalizza modello) immettere tutte le informazioni necessarie. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/customize-template.png" alt-text="In Personalizza modello immettere tutte le informazioni necessarie.":::

1. Selezionare **Next** (Avanti), verificare la configurazione e selezionare **Finish** (Fine) per distribuire HCX OVA.

## <a name="activate-hcx"></a>Attivare HCX

In seguito all'installazione eseguire la procedura seguente.

1. Accedere al gestore HCX locale `https://HCXManagerIP:9443` e accedere con le credenziali nome utente **amministratore** . 

   > [!IMPORTANT]
   > Assicurarsi di includere il `9443` numero di porta con l'indirizzo IP di HCX Manager.

1. In **Licensing** (Licenze) immettere la **HCX Advanced Key** (Chiave avanzata di HCX).  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key.png" alt-text="In Licensing immettere la chiave avanzata di HCX.":::
    
    > [!NOTE]
    > HCX Manager deve avere un accesso a Internet aperto o un proxy configurato.

1. In **vCenter**, se necessario, modificare le informazioni di vCenter.

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-vcenter.png" alt-text="In vCenter, se necessario, modificare le informazioni di vCenter.":::

1. Se necessario, in **Datacenter Location** (Posizione del data center) modificare la posizione del data center.

   :::image type="content" source="media/hybrid-cloud-extension-installation/system-location.png" alt-text="In posizione data center, se necessario, modificare la posizione del Data Center.":::

## <a name="configure-hcx"></a>Configurare HCX 

1. Accedere a vCenter locale e in **Home**selezionare **HCX**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-vcenter.png" alt-text="Accedere a vCenter locale e in Home selezionare HCX.":::

1. In **infrastruttura**selezionare **associazione siti**  >  **Aggiungi associazione sito**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-pairing.png" alt-text="In infrastruttura selezionare Associazione siti > Aggiungi associazione sito.":::

1. Immettere l'URL o l'indirizzo IP di HCX remoto, la soluzione VMware di Azure cloudadmin nome utente e password e quindi selezionare **Connetti**.

   Il sistema mostra il sito connesso.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-connection.png" alt-text="Il sistema mostra il sito connesso.":::

1. In **infrastruttura**selezionare **Interconnect**  >  **multisito Servizi**  >  **rete mesh profili**  >  **Crea profilo di rete**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-network-profile.png" alt-text="In infrastruttura selezionare Interconnect > rete di servizi multisito > profili di rete > Crea profilo di rete.":::

1. Per il nuovo profilo di rete, immettere gli intervalli di indirizzi IP HCX IX e NE (per le appliance IX e NE sono necessari almeno due indirizzi IP).

   :::image type="content" source="media/hybrid-cloud-extension-installation/enter-address-ranges.png" alt-text="Per il nuovo profilo di rete, immettere gli intervalli di indirizzi IP HCX IX e NE (per le appliance IX e NE sono necessari almeno due indirizzi IP).":::
  
   > [!NOTE]
   > L'appliance Network Extension (HCX-NE) ha una relazione uno-a-uno con un commutatore virtuale distribuito.  

1. Selezionare ora **Compute profile (Profilo di calcolo)**  > **Create compute profile (Crea profilo di calcolo)** .

1. Immettere un nome per il profilo di calcolo e selezionare **Continue** (Continua).  

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-compute-profile.png" alt-text="Immettere un nome per il profilo di calcolo e selezionare continua.":::

1. Selezionare i servizi da abilitare, ad esempio migrazione, estensione di rete o ripristino di emergenza, quindi selezionare **continua**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-services.png" alt-text="Selezionare i servizi da abilitare, ad esempio migrazione, estensione di rete o ripristino di emergenza, quindi selezionare continua.":::

1. In **Select Service Resources** (Seleziona risorse del servizio) selezionare una o più risorse del servizio per le quali devono essere abilitati i servizi HCX selezionati. Selezionare **Continua**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-service-resources.png" alt-text="In selezione risorse servizio selezionare una o più risorse del servizio per le quali devono essere abilitati i servizi HCX selezionati. Selezionare continua.":::
  
   > [!NOTE]
   > Selezionare cluster specifici a cui le macchine virtuali di origine sono destinate per la migrazione con HCX.

1. Selezionare **Datastore** (Archivio dati) e quindi **Continue** (Continua). 
      
   Selezionare ogni risorsa di calcolo e di archiviazione per la distribuzione delle appliance HCX Interconnect. Quando vengono selezionate più risorse, HCX usa la prima risorsa selezionata fino a quando non ne viene esaurita la capacità.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployment-resources.png" alt-text="Selezionare ogni risorsa di calcolo e di archiviazione per la distribuzione delle appliance di interconnessione HCX. Quando vengono selezionate più risorse, HCX usa la prima risorsa selezionata fino a quando non viene esaurita la capacità.":::

1. Selezionare il profilo di rete di gestione creato in **Network Profiles** (Profili di rete) e quindi **Continue** (Continua).  
      
   Selezionare il profilo di rete mediante il quale è possibile raggiungere l'interfaccia di gestione di vCenter e gli host ESXi. Se tale profilo di rete non è già stato definito, è possibile crearlo qui.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/management-network-profile.png" alt-text="Selezionare il profilo di rete mediante il quale è possibile raggiungere l'interfaccia di gestione di vCenter e gli host ESXi. Se non è già stato definito un profilo di rete di questo tipo, è possibile crearlo qui.":::

1. Selezionare **Network Uplink** (Uplink di rete) e quindi **Continue** (Continua).
      
   Selezionare uno o più profili di rete in modo che venga soddisfatta una delle condizioni seguenti:  
   * Le appliance Interconnect nel sito remoto possono essere raggiunte tramite questa rete  
   * Le appliance sul lato remoto possono raggiungere le appliance Interconnect locali tramite questa rete.  
    
   Se si dispone di reti da punto a punto quali una connessione diretta non condivise tra più siti, è possibile ignorare questo passaggio, perché i profili di calcolo vengono condivisi con più siti. In questi casi, è possibile eseguire l'override dei profili di rete uplink e specificarli durante la creazione della mesh del servizio di interconnessione.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/uplink-network-profile.png" alt-text="Selezionare uplink di rete e selezionare continua.":::

1. Selezionare **vMotion Network Profile** (Profilo di rete vMotion) e quindi **Continue** (Continua).
      
   Selezionare il profilo di rete tramite il quale è possibile raggiungere l'interfaccia vMotion degli host ESXi. Se tale profilo di rete non è già stato definito, è possibile crearlo qui. Se non si dispone della rete vMotion, selezionare **Management Network Profile** (Profilo di rete di gestione).  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vmotion-network-profile.png" alt-text="Selezionare vMotion network profile e selezionare continue (continua).":::

1. In **Seleziona profilo di rete vSphere Replication**selezionare un profilo di rete l'interfaccia di replica vSphere degli host ESXi, quindi selezionare **continua**.
      
   Nella maggior parte dei casi questo profilo corrisponde al profilo di rete di gestione.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png" alt-text="In Seleziona profilo di rete vSphere Replication selezionare un profilo di rete l'interfaccia di replica vSphere degli host ESXi, quindi selezionare continua.":::

1. In **Seleziona opzioni distribuite per le estensioni di rete**selezionare il DVS su cui si trovano le reti le VM che verranno integrate e connesse.  Selezionare **Continua**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/distributed-switches.png" alt-text="In Seleziona opzioni distribuite per le estensioni di rete selezionare il DVS su cui si trovano le reti le VM che verranno integrate e connesse.  Selezionare continua.":::

1. Controllare le regole di connessione e selezionare **continua**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-compute-profile.png" alt-text="Controllare le regole di connessione e selezionare continua.":::

1.  Selezionare **Finish** (Fine) per creare il profilo di calcolo.

## <a name="configure-network-uplink"></a>Configurare l'uplink della rete

Configurare ora la modifica del profilo di rete nella soluzione VMware di Azure SDDC per uplink di rete.

1. Accedere a SDDC NSX-T per creare un nuovo Commuter logico o usare un commutire logico esistente che può essere usato per la rete uplink tra l'istanza locale e la soluzione VMware di Azure SDDC.

1. Creare un profilo di rete per uplink HCX in una soluzione VMware di Azure SDDC che può essere usata per la comunicazione da sito locale ad Azure per la soluzione VMware SDDC.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-profile-uplink.png" alt-text="Creare un profilo di rete per uplink HCX in una soluzione VMware di Azure SDDC che può essere usata per la comunicazione da sito locale ad Azure per la soluzione VMware SDDC.":::

1. Immettere un nome per il profilo di rete e almeno 4-5 indirizzi IP disponibili in base all'estensione di rete L2 richiesta.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-uplink-profile.png" alt-text="Immettere un nome per il profilo di rete e almeno 4-5 indirizzi IP disponibili in base all'estensione di rete L2 richiesta.":::

1. Selezionare **Crea** per completare la configurazione SDDC della soluzione VMware di Azure

## <a name="configure-service-mesh"></a>Configurare la mesh del servizio

Configurare ora il servizio mesh tra l'ambiente locale e la soluzione VMware di Azure SDDC.

1. Accedere alla soluzione VMware di Azure SDDC vCenter e selezionare **HCX**.

2. In **infrastruttura**selezionare **Interconnect**  >  **Service mesh**  >  **Crea servizio** mesh per configurare i profili di calcolo e di rete creati nei passaggi precedenti.    

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-service-mesh.png" alt-text="In infrastruttura selezionare Interconnect > Service mesh > Create Service mesh per configurare i profili di rete e di calcolo creati nei passaggi precedenti.":::

3. Selezionare siti abbinati per abilitare la funzionalità ibrida e selezionare **continua**.   

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-paired-sites.png" alt-text="Selezionare siti abbinati per abilitare la funzionalità ibrida e selezionare continua.":::

4. Selezionare i profili di calcolo di origine e remoto per abilitare i servizi di ibridazione e selezionare **continua**.
      
   Le selezioni definiscono le risorse, in cui le macchine virtuali possono utilizzare i servizi HCX.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/enable-hybridity.png" alt-text="Le selezioni definiscono le risorse, in cui le macchine virtuali possono utilizzare i servizi HCX.":::

5. Selezionare Servizi da abilitare e selezionare **continua**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-services.png" alt-text="Selezionare Servizi da abilitare e selezionare continua.":::

6. In **Advanced Configuration - Override Uplink Network profiles** (Configurazione avanzata - Override dei profilo di rete uplink) selezionare **Continue** (Continua).  
      
   I profili di rete uplink vengono usati per connettersi alla rete tramite cui è possibile raggiungere le appliance di interconnessione del sito remoto.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/override-uplink-profiles.png" alt-text="I profili di rete uplink vengono usati per connettersi alla rete tramite cui è possibile raggiungere le appliance di interconnessione del sito remoto.":::

7. Selezionare **Configure the Network Extension Appliance scale out**. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-extension-scale-out.png" alt-text="Selezionare Configure the Network Extension Appliance Scale Out.":::

8. Immettere il numero di appliance corrispondente al numero di commutatori DVS.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/appliance-scale.png" alt-text="Immettere il numero di appliance corrispondente al numero di commutatori DVS.":::

9. Selezionare **continua** per ignorare.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/traffic-engineering.png" alt-text="Selezionare continua per ignorare.":::

10. Esaminare l'anteprima della topologia e selezionare **Continue** (Continua). 

11. Immettere un nome descrittivo per il servizio mesh e selezionare **fine** per completare l'operazione.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-service-mesh.png" alt-text="Completa mesh di servizi":::

   La mesh del servizio viene distribuita e configurata.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployed-service-mesh.png" alt-text="Mesh del servizio distribuita":::

## <a name="check-appliance-status"></a>Verificare lo stato dell'appliance
Per verificare lo stato dell'appliance, selezionare **Interconnect (Interconnessione)**  > **Appliance**. 

:::image type="content" source="media/hybrid-cloud-extension-installation/appliance-status.png" alt-text="Verificare lo stato dell'appliance.":::

## <a name="next-steps"></a>Passaggi successivi

Quando lo stato del **tunnel** di interconnessione del dispositivo è **attivo** e verde, si è pronti per eseguire la migrazione e proteggere le VM della soluzione VMware di Azure con HCx. Vedere la [documentazione relativa a VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) e [Migrazione delle macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) nella documentazione tecnica di VMware.
