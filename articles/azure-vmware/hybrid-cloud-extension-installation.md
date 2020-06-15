---
title: Installare Hybrid Cloud Extension (HCX)
description: Configurare la soluzione VMware Hybrid Cloud Extension (HCX) per il cloud privato della soluzione Azure VMware (AVS)
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: dc5f7f82b83c82538b2d5a7b4c87131afb3fcc20
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873659"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Installare HCX per la soluzione Azure VMware

In questo articolo vengono illustrate le procedure per configurare la soluzione VMware Hybrid Cloud Extension (HCX) per il cloud privato della soluzione Azure VMware (AVS). HCX Advanced (installazione predefinita) supporta fino a tre siti esterni, per ogni sito esterno è necessario che il programma di gestione e il connettore HCX Enterprise siano installati e attivati.
HCX consente la migrazione dei carichi di lavoro VMware nel cloud e o in altri siti connessi tramite vari tipi di migrazione predefiniti e supportati da HCX. Se sono necessari più di tre siti, i clienti possono abilitare il componente aggiuntivo HCX Enterprise tramite il supporto. HCX Enterprise comporta costi aggiuntivi per i clienti oltre la disponibilità generale, ma offre [funzionalità aggiuntive](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).

Esaminare innanzitutto [Prima di iniziare](#before-you-begin), [Requisiti della versione software](#software-version-requirements) e [Prerequisiti](#prerequisites) con attenzione. 

Vengono poi illustrate tutte le procedure necessarie per:

> [!div class="checklist"]
> * Distribuire HCX OVA locale
> * Attivare e configurare HCX
> * Configurare l'uplink di rete e la mesh del servizio
> * Completare l'installazione verificando lo stato dell'appliance

Al termine della configurazione, vengono indicati i passaggi successivi consigliati.

## <a name="before-you-begin"></a>Prima di iniziare
    
* Esaminare la [serie di esercitazioni](tutorial-network-checklist.md) di base su Software Defined Datacenter (SDDC) di AVS
* Esaminare e fare riferimento alla [documentazione su VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) che include il manuale dell'utente di HCX
* Esaminare la documentazione di VMware sulla [migrazione delle macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* Esaminare facoltativamente le [considerazioni sulla distribuzione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* Esaminare facoltativamente i materiali di VMware correlati in HCX, ad esempio la [serie di blog](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) su VMware vSphere in HCX. 
* Ordinare un'attivazione di HCX Enterprise per AVS tramite i canali di supporto di AVS.

Il dimensionamento dei carichi di lavoro rispetto alle risorse di calcolo e di archiviazione è un passaggio essenziale della pianificazione quando ci si prepara a usare la soluzione HCX per il cloud privato di AVS. Il passaggio di dimensionamento deve essere affrontato come parte della pianificazione iniziale dell'ambiente cloud privato. 

## <a name="software-version-requirements"></a>Requisiti della versione software
I componenti dell'infrastruttura devono eseguire la versione minima richiesta. 
                                                         
| Tipo di componente                                                          | Requisiti dell'ambiente di origine                                                                   | Requisiti dell'ambiente di destinazione                                                                      |
| --- | --- | --- |
| Server vCenter                                                          | 5.1<br/><br/>Se si usa la versione 5.5 U1 o precedente, usare l'interfaccia utente di HCX autonoma per le operazioni di HCX.         | 6.0 U2 e versioni successive                                                                                          |
| ESXi                                                                    | 5.0                                                                                               | ESXi 6.0 e versioni successive                                                                                        |
| NSX                                                                     | Per HCX Network Extension dei commutatori logici nell'origine: NSXv 6.2+ o NSX-T 2.4+              | NSXv 6.2+ o NSX-T 2.4+<br/><br/Per il routing di prossimità di HCX: NSXv 6.4 + (routing di prossimità non supportato con NSX-T) |
| vCloud Director                                                         | Non richiesto: nessuna interoperabilità con vCloud Director nel sito di origine | Quando l'ambiente di destinazione è integrato con vCloud Director, la versione minima è 9.1.0.2.              |

## <a name="prerequisites"></a>Prerequisiti

* Copertura globale deve essere configurato tra i circuiti locali e AVS SDDC ER.

* Tutte le porte obbligatorie devono essere aperte tra i circuiti locali e AVS SDDC (vedere la [documentazione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Un indirizzo IP per HCX Manager in locale e un minimo di due indirizzi IP per l'appliance Interconnect (IX) ed Network Extension (NE).

* Le appliance HCX IX e NE locali dovrebbero essere in grado di raggiungere l'infrastruttura vCenter e ESXi.

* Per distribuire l'appliance WAN Interconnect, oltre al blocco di indirizzi di rete /22 CIDR usato per la distribuzione SDDC nella portale di Azure, HCX richiede un blocco /29. È necessario fattorizzarlo nella pianificazione di rete.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Distribuire VMware HCX OVA in locale

1. Accedere ad AVS SDDC vCenter e selezionare **HCX**.

    ![Selezionare HCX in AVS vCenter](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. Per scaricare il file VMware HCX OVA, selezionare **Administration (Amministrazione)**  > **System Updates (Aggiornamenti del sistema)** .

    ![Ottenere gli aggiornamenti del sistema](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Selezionare un modello OVF per la distribuzione in vCenter locale.  

    ![Selezionare il modello OVF](./media/hybrid-cloud-extension-installation/select-template.png)

1. Selezionare un nome e un percorso, quindi selezionare una risorsa o un cluster in cui eseguire la distribuzione di HCX. Esaminare quindi i dettagli e le risorse necessarie.  

    ![Esaminare i dettagli del modello](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Esaminare le condizioni di licenza e, se si accettano, selezionare l'archivio e la rete richieste. Fare quindi clic su **Avanti**.

1. In **Customize template** (Personalizza modello) immettere tutte le informazioni necessarie. 

    ![Personalizzare il modello](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Selezionare **Next** (Avanti), verificare la configurazione e selezionare **Finish** (Fine) per distribuire HCX OVA.

## <a name="activate-hcx"></a>Attivare HCX

In seguito all'installazione eseguire la procedura seguente.

1. Aprire HCX Manager in `https://HCXManagerIP:9443` e accedere con il nome utente e la password. 

1. In **Licensing** (Licenze) immettere la **HCX Advanced Key** (Chiave avanzata di HCX).  

    ![Immettere la chiave di HCX](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX Manager deve avere un accesso a Internet aperto o un proxy configurato.

1. Configurare vCenter.

    ![Configurare vCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. Se necessario, in **Datacenter Location** (Posizione del data center) modificare la posizione del data center.

    ![Posizione del database](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>Configurare HCX 

1. Accedere a vCenter locale, quindi selezionare **Home** > **HCX**.

    ![HCX in VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. Selezionare **Infrastructure (Infrastruttura)**  > **Site Pairing (Associazione sito)**  > **Add a site pairing (Aggiungi un'associazione del sito)** .

    ![Aggiungere un'associazione del sito](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Immettere l'**URL HCX remoto**, il **nome utente** e la **password**. Selezionare **Connetti**.

   Il sistema mostra il sito connesso.
   
    ![Connessione del sito](./media/hybrid-cloud-extension-installation/site-connection.png)

1. Selezionare **Interconnect(Interconnessione)**  > **Multi-Site Service Mesh (Mesh del servizio multisito)**  > **Network Profiles (Profili di rete)**  > **Create Network Profile (Crea profilo di rete)** .

    ![Creare un profilo di rete](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Immettere gli intervalli di indirizzi IP di HCX IX e NE; sono necessari almeno 2 indirizzi IP per le appliance IX e NE.
    
   ![Immettere gli intervalli di indirizzi IP](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > L'appliance Network Extension (HCX-NE) ha una relazione uno-a-uno con un commutatore virtuale distribuito.  

1. Selezionare ora **Compute profile (Profilo di calcolo)**  > **Create compute profile (Crea profilo di calcolo)** .

1. Immettere un nome per il profilo di calcolo e selezionare **Continue** (Continua).  

    ![Creare un profilo di calcolo](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Selezionare i servizi da abilitare, ad esempio la migrazione, Network Extension (Estensione di rete) o Disaster Recovery (Ripristino di emergenza). Selezionare **Continua**.

    ![Selezionare i servizi](./media/hybrid-cloud-extension-installation/select-services.png)

1. In **Select Service Resources** (Seleziona risorse del servizio) selezionare una o più risorse del servizio per le quali devono essere abilitati i servizi HCX selezionati. Selezionare **Continua**.
    
   ![Selezionare le risorse del servizio](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > Selezionare cluster specifici a cui le macchine virtuali di origine sono destinate per la migrazione con HCX.

1. Selezionare **Datastore** (Archivio dati) e quindi **Continue** (Continua). 
      
    Selezionare ogni risorsa di calcolo e di archiviazione per la distribuzione delle appliance HCX Interconnect. Quando vengono selezionate più risorse, HCX usa la prima risorsa selezionata fino a quando non ne viene esaurita la capacità.  
    
    ![Selezionare le risorse di distribuzione](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. Selezionare il profilo di rete di gestione creato in **Network Profiles** (Profili di rete) e quindi **Continue** (Continua).  
      
    Selezionare il profilo di rete mediante il quale è possibile raggiungere l'interfaccia di gestione di vCenter e gli host ESXi. Se tale profilo di rete non è già stato definito, è possibile crearlo qui.  
    
    ![Selezionare il profilo di rete di gestione](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. Selezionare **Network Uplink** (Uplink di rete) e quindi **Continue** (Continua).
      
    Selezionare uno o più profili di rete in modo che si verifichi una delle condizioni seguenti:  
    * Le appliance Interconnect nel sito remoto possono essere raggiunte tramite questa rete  
    * Le appliance sul lato remoto possono raggiungere le appliance Interconnect locali tramite questa rete.  
    
    Se si dispone di reti da punto a punto quali una connessione diretta non condivise tra più siti, è possibile ignorare questo passaggio, perché i profili di calcolo vengono condivisi con più siti. In questi casi, è possibile eseguire l'override dei profili di rete uplink e specificarli durante la creazione della mesh del servizio di interconnessione.  
    
    ![Selezionare il profilo di rete uplink](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Selezionare **vMotion Network Profile** (Profilo di rete vMotion) e quindi **Continue** (Continua).
      
    Selezionare il profilo di rete tramite il quale è possibile raggiungere l'interfaccia vMotion degli host ESXi. Se tale profilo di rete non è già stato definito, è possibile crearlo qui. Se non si dispone della rete vMotion, selezionare **Management Network Profile** (Profilo di rete di gestione).  
    
    ![Selezionare il profilo di rete vMotion](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. Selezionare **vSphere Replication Network Profile** (Profilo di rete della replica di vSphere) e quindi **Continue** (Continua).
      
    Selezionare un profilo di rete tramite il quale è possibile raggiungere l'interfaccia della replica di vSphere degli host ESXi. Nella maggior parte dei casi questo profilo corrisponde al profilo di rete di gestione.  
    
    ![Selezionare il profilo di rete della replica di vSphere](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. Selezionare **Distributed Switches for Network Extensions** (Commutatori distribuiti per le estensioni di rete) e quindi **Continue** (Continua).  
      
    Selezionare i commutatori virtuali distribuiti su cui si trovano le reti a cui sono connesse le macchine virtuali di cui verrà eseguita la migrazione.

    ![Selezionare i commutatori virtuali distribuiti](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Esaminare le regole di connessione e selezionare **Continue** (Continua). Selezionare **Finish** (Fine) per creare il profilo di calcolo.  

    ![Creare un profilo di calcolo](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

## <a name="configure-network-uplink"></a>Configurare l'uplink della rete

Configurare ora la modifica del profilo di rete in AVS SDDC per l'uplink della rete.

1. Accedere a SDDC NSX-T per creare un nuovo commutatore logico oppure usare un commutatore logico esistente che può essere usato per l'uplink della rete tra l'ambiente locale e AVS SDDC.

1. Creare un profilo di rete per l'uplink di HCX in AVS SDDC che può essere usato per la comunicazione da locale ad AVS SDDC.  
    
   ![Creare un profilo di rete per l'uplink](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. Immettere un nome per il profilo di rete e almeno 4-5 indirizzi IP disponibili in base all'estensione di rete L2 richiesta.  
    
   ![Configurare un profilo di rete per l'uplink](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. Selezionare **Create** (Crea) per completare la configurazione di AVS SDDC

## <a name="configure-service-mesh"></a>Configurare la mesh del servizio

Configurare ora la mesh del servizio tra locale e AVS SDDC.

1. Accedere ad AVS SDDC vCenter e selezionare **HCX**.

1. Selezionare **Infrastructure (Infrastruttura)**  > **Interconnect (Interconnesione)**  > **Service Mesh (Mesh del servizio)**  > **Create Service Mesh (Crea mesh del servizio)** .  Configurare i profili di rete e di calcolo creati nei passaggi precedenti.    
      
    ![Configurare la mesh del servizio](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

1. Selezionare **Create Service Mesh** (Crea mesh del servizio) e quindi **Continue** (Continua).  
      
    Selezionare i siti associati tra cui abilitare la mobilità ibrida.  
    
    ![Selezionare i siti associati](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

1. Selezionare **Compute profile** (Profilo di calcolo) e quindi **Continue** (Continua).
      
    Selezionare un profilo di calcolo per il sito di origine e uno per il sito remoto per abilitare i servizi di ibridità. Le selezioni definiranno le risorse in cui le macchine virtuali saranno in grado di usare i servizi HCX.  
      
    ![Abilitare i servizi di ibridità](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

1. Selezionare i servizi da abilitare per HCX e selezionare **Continue** (Continua).  
      
    ![Selezionare i servizi HCX](./media/hybrid-cloud-extension-installation/hcx-services.png)

1. In **Advanced Configuration - Override Uplink Network profiles** (Configurazione avanzata - Override dei profilo di rete uplink) selezionare **Continue** (Continua).  
      
    I profili di rete uplink vengono usati per connettersi alla rete tramite cui è possibile raggiungere le appliance di interconnessione del sito remoto.  
      
    ![Override dei profili di uplink](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

1. In **Advanced Configuration – Network Extension Appliance Scale Out** (Configurazione avanzata - Aumentare le istanze dell'appliance dell'estensione di rete) selezionare **Configure the Network Extension Appliance Scale Out** (Configurare l'aumento delle istanze dell'appliance dell'estensione di rete). 
      
    ![Aumento delle istanze dell'estensione di rete](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

1. Immettere il numero di appliance corrispondente al numero di commutatori DVS.  
      
    ![Configurare il numero di appliance](./media/hybrid-cloud-extension-installation/appliance-scale.png)

1. In **Advanced Configuration - Traffic Engineering** (Configurazione avanzata - Progettazione del traffico) selezionare **Continue** (Continua).  
      
    ![Configurare la progettazione del traffico](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

1. Esaminare l'anteprima della topologia e selezionare **Continue** (Continua). Immettere quindi un nome descrittivo per la mesh del servizio e selezionare **Finish** (Fine) per completare l'operazione.  
      
    ![Completare la mesh del servizio](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    La mesh del servizio viene distribuita e configurata.  
      
    ![Mesh del servizio distribuita](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Verificare lo stato dell'appliance
Per verificare lo stato dell'appliance, selezionare **Interconnect (Interconnessione)**  > **Appliance**. 
      
![Stato dell'appliance](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Passaggi successivi

Quando **Tunnel Status** (Stato del tunnel) di interconnessione dell'appliance è **UP** (ATTIVO) e verde, è possibile eseguire la migrazione e proteggere le macchine virtuali AVS usando HCX. Vedere la [documentazione relativa a VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) e [Migrazione delle macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) nella documentazione tecnica di VMware.
