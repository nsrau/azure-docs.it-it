---
title: Installare Hybrid Cloud Extension (HCX)
description: Configurare la soluzione VMware Hybrid Cloud Extension (HCX) per il cloud privato della soluzione Azure VMware (AVS)
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: b897a44fb6811c4e3564c59a8ab2c064506f0a4f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539160"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Installare HCX per la soluzione Azure VMware

Questo articolo illustra le procedure per la configurazione della soluzione VMWare Hybrid Cloud Extension (HCX) per il cloud privato della soluzione VMWare di Azure (AVS). HCX consente la migrazione dei carichi di lavoro VMware nel cloud e in altri siti connessi tramite vari tipi di migrazione supportati da HCX predefiniti.

HCX Advanced, l'installazione predefinita, supporta fino a tre vCenter. Se sono necessari più di tre, i clienti hanno la possibilità di abilitare il componente aggiuntivo HCX Enterprise tramite il supporto. L'installazione di HCX Enterprise comporta addebiti aggiuntivi per i clienti dopo la disponibilità generale (GA), ma fornisce [funzionalità aggiuntive](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).


Esaminare innanzitutto [Prima di iniziare](#before-you-begin), [Requisiti della versione software](#software-version-requirements) e [Prerequisiti](#prerequisites) con attenzione. 

Vengono poi illustrate tutte le procedure necessarie per:

> [!div class="checklist"]
> * Distribuire HCX OVA locale
> * Attivare e configurare HCX
> * Configurare l'uplink di rete e la mesh del servizio
> * Completare l'installazione verificando lo stato dell'appliance

Dopo aver completato l'installazione, è possibile seguire i passaggi successivi consigliati disponibili alla fine di questo articolo.  

## <a name="before-you-begin"></a>Prima di iniziare
    
* Esaminare la [serie di esercitazioni](tutorial-network-checklist.md) di base su Software Defined Datacenter (SDDC) di AVS
* Esaminare e fare riferimento alla [documentazione su VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) che include il manuale dell'utente di HCX
* Esaminare la documentazione di VMware sulla [migrazione delle macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* Esaminare facoltativamente le [considerazioni sulla distribuzione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* Esaminare facoltativamente i materiali di VMware correlati in HCX, ad esempio la [serie di blog](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) su VMware vSphere in HCX. 
* Ordinare un'attivazione di HCX Enterprise per AVS tramite i canali di supporto di AVS.

Il dimensionamento dei carichi di lavoro rispetto alle risorse di calcolo e di archiviazione è un passaggio essenziale della pianificazione quando ci si prepara a usare la soluzione HCX per il cloud privato di AVS. Risolvere il passaggio di ridimensionamento nell'ambito della pianificazione iniziale dell'ambiente del cloud privato.   

## <a name="software-version-requirements"></a>Requisiti della versione software
I componenti dell'infrastruttura devono eseguire la versione minima richiesta. 
                                                         
| Tipo di componente    | Requisiti dell'ambiente di origine    | Requisiti dell'ambiente di destinazione   |
| --- | --- | --- |
| Server vCenter   | 5.1<br/><br/>Se si usa la versione 5.5 U1 o precedente, usare l'interfaccia utente di HCX autonoma per le operazioni di HCX.  | 6.0 U2 e versioni successive   |
| ESXi   | 5.0    | ESXi 6.0 e versioni successive   |
| NSX    | Per HCX Network Extension dei commutatori logici nell'origine: NSXv 6.2+ o NSX-T 2.4+   | NSXv 6.2+ o NSX-T 2.4+<br/><br/>Per HCX prossimità routing: NSXv 6.4 + (routing di prossimità non supportato con NSX-T) |
| vCloud Director   | Non richiesto: nessuna interoperabilità con vCloud Director nel sito di origine | Quando si integra l'ambiente di destinazione con vCloud Director, il valore minimo è 9.1.0.2.  |

## <a name="prerequisites"></a>Prerequisiti

* La copertura globale deve essere configurata tra circuiti locali e AVS SDDC ER.

* Tutte le porte obbligatorie devono essere aperte tra i circuiti locali e AVS SDDC (vedere la [documentazione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Un indirizzo IP per HCX Manager in locale e un minimo di due indirizzi IP per l'appliance Interconnect (IX) ed estensione di rete (NE).

* Le appliance HCX IX e NE locali dovrebbero essere in grado di raggiungere l'infrastruttura vCenter e ESXi.

* Per distribuire l'appliance WAN Interconnect, oltre al blocco di indirizzi di rete /22 CIDR usato per la distribuzione SDDC nella portale di Azure, HCX richiede un blocco /29. Assicurarsi di fattorizzare questo requisito nella pianificazione della rete.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Distribuire VMware HCX OVA in locale

1. Accedere ad AVS SDDC vCenter e selezionare **HCX**.

    ![Selezionare HCX in AVS vCenter](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. In **Amministrazione**selezionare **aggiornamenti del sistema** e quindi fare clic sul **collegamento Richiedi download** per scaricare il file OVA VMware HCx.

    ![Ottenere gli aggiornamenti del sistema](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Passare quindi a vCenter locale e selezionare un modello OVF per la distribuzione in vCenter locale.  

    ![Selezionare il modello OVF](./media/hybrid-cloud-extension-installation/select-template.png)

1. Selezionare un nome e un percorso, quindi selezionare una risorsa o un cluster in cui eseguire la distribuzione di HCX. Esaminare quindi i dettagli e le risorse necessarie.  

    ![Esaminare i dettagli del modello](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Esaminare le condizioni di licenza e, se si accettano, selezionare l'archivio e la rete richieste. Fare quindi clic su **Avanti**.

1. In **Customize template** (Personalizza modello) immettere tutte le informazioni necessarie. 

    ![Personalizzare il modello](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Selezionare **Next** (Avanti), verificare la configurazione e selezionare **Finish** (Fine) per distribuire HCX OVA.

## <a name="activate-hcx"></a>Attivare HCX

In seguito all'installazione eseguire la procedura seguente.

1. Accedere al gestore HCX locale `https://HCXManagerIP:9443` e accedere con il nome utente e la password. 

   > [!IMPORTANT]
   > Assicurarsi di includere il `9443` numero di porta con l'indirizzo IP di HCX Manager.

1. In **Licensing** (Licenze) immettere la **HCX Advanced Key** (Chiave avanzata di HCX).  

    ![Immettere la chiave di HCX](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX Manager deve avere un accesso a Internet aperto o un proxy configurato.

1. In **vCenter**, se necessario, modificare le informazioni di vCenter.

    ![Configurare vCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. Se necessario, in **Datacenter Location** (Posizione del data center) modificare la posizione del data center.

    ![Posizione del database](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>Configurare HCX 

1. Accedere a vCenter locale e in **Home**selezionare **HCX**.

    ![HCX in VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. In **infrastruttura**selezionare **associazione siti**  >  **Aggiungi associazione sito**.

    ![Aggiungere un'associazione del sito](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Immettere l'URL o l'indirizzo IP di HCX remoto, AVS cloudadmin nome utente e password e quindi selezionare **Connetti**.

   Il sistema mostra il sito connesso.
   
    ![Connessione del sito](./media/hybrid-cloud-extension-installation/site-connection.png)

1. In **infrastruttura**selezionare **Interconnect**  >  **multisito Servizi**  >  **rete mesh profili**  >  **Crea profilo di rete**.

    ![Creare un profilo di rete](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Per il nuovo profilo di rete, immettere gli intervalli di indirizzi IP HCX IX e NE (per le appliance IX e NE sono necessari almeno due indirizzi IP).
    
   ![Immettere gli intervalli di indirizzi IP](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > L'appliance Network Extension (HCX-NE) ha una relazione uno-a-uno con un commutatore virtuale distribuito.  

1. Selezionare ora **Compute profile (Profilo di calcolo)**  > **Create compute profile (Crea profilo di calcolo)** .

1. Immettere un nome per il profilo di calcolo e selezionare **Continue** (Continua).  

    ![Creare un profilo di calcolo](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Selezionare i servizi da abilitare, ad esempio migrazione, estensione di rete o ripristino di emergenza, quindi selezionare **continua**.

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
      
    Selezionare uno o più profili di rete in modo che venga soddisfatta una delle condizioni seguenti:  
    * Le appliance Interconnect nel sito remoto possono essere raggiunte tramite questa rete  
    * Le appliance sul lato remoto possono raggiungere le appliance Interconnect locali tramite questa rete.  
    
    Se si dispone di reti da punto a punto quali una connessione diretta non condivise tra più siti, è possibile ignorare questo passaggio, perché i profili di calcolo vengono condivisi con più siti. In questi casi, è possibile eseguire l'override dei profili di rete uplink e specificarli durante la creazione della mesh del servizio di interconnessione.  
    
    ![Selezionare il profilo di rete uplink](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Selezionare **vMotion Network Profile** (Profilo di rete vMotion) e quindi **Continue** (Continua).
      
   Selezionare il profilo di rete tramite il quale è possibile raggiungere l'interfaccia vMotion degli host ESXi. Se tale profilo di rete non è già stato definito, è possibile crearlo qui. Se non si dispone della rete vMotion, selezionare **Management Network Profile** (Profilo di rete di gestione).  
    
   ![Selezionare il profilo di rete vMotion](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. In **Seleziona profilo di rete vSphere Replication**selezionare un profilo di rete l'interfaccia di replica vSphere degli host ESXi, quindi selezionare **continua**.
      
   Nella maggior parte dei casi questo profilo corrisponde al profilo di rete di gestione.  
    
   ![Selezionare il profilo di rete della replica di vSphere](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. In **Seleziona opzioni distribuite per le estensioni di rete**selezionare il DVS su cui si trovano le reti le VM che verranno integrate e connesse.  Seleziona **Continua**.  
      
    ![Selezionare i commutatori virtuali distribuiti](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Controllare le regole di connessione e selezionare **continua**.  

    ![Creare un profilo di calcolo](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

1.  Selezionare **Finish** (Fine) per creare il profilo di calcolo.

## <a name="configure-network-uplink"></a>Configurare l'uplink della rete

Configurare ora la modifica del profilo di rete in AVS SDDC per l'uplink della rete.

1. Accedere a SDDC NSX-T per creare un nuovo Commuter logico o usare un commutire logico esistente che può essere usato per uplink di rete tra l'ambiente locale e AVS SDDC.

1. Creare un profilo di rete per uplink HCX in AVS SDDC che può essere usato per la comunicazione da sito locale a AVS SDDC.  
    
   ![Creare un profilo di rete per l'uplink](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. Immettere un nome per il profilo di rete e almeno 4-5 indirizzi IP disponibili in base all'estensione di rete L2 richiesta.  
    
   ![Configurare un profilo di rete per l'uplink](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. Selezionare **Create** (Crea) per completare la configurazione di AVS SDDC

## <a name="configure-service-mesh"></a>Configurare la mesh del servizio

Configurare ora la mesh del servizio tra locale e AVS SDDC.

1. Accedere ad AVS SDDC vCenter e selezionare **HCX**.

2. In **infrastruttura**selezionare **Interconnect**  >  **Service mesh**  >  **Crea servizio** mesh per configurare i profili di calcolo e di rete creati nei passaggi precedenti.    
      
    ![Configurare la mesh del servizio](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

3. Selezionare siti abbinati per abilitare la funzionalità ibrida e selezionare **continua**.   
    
    ![Selezionare i siti associati](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

4. Selezionare i profili di calcolo di origine e remoto per abilitare i servizi di ibridazione e selezionare **continua**.
      
    Le selezioni definiscono le risorse, in cui le macchine virtuali possono utilizzare i servizi HCX.  
      
    ![Abilitare i servizi di ibridità](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

5. Selezionare Servizi da abilitare e selezionare **continua**.  
      
    ![Selezionare i servizi HCX](./media/hybrid-cloud-extension-installation/hcx-services.png)

6. In **Advanced Configuration - Override Uplink Network profiles** (Configurazione avanzata - Override dei profilo di rete uplink) selezionare **Continue** (Continua).  
      
    I profili di rete uplink vengono usati per connettersi alla rete tramite cui è possibile raggiungere le appliance di interconnessione del sito remoto.  
      
    ![Override dei profili di uplink](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

7. Selezionare **Configure the Network Extension Appliance scale out**. 
      
    ![Aumento delle istanze dell'estensione di rete](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

8. Immettere il numero di appliance corrispondente al numero di commutatori DVS.  
      
    ![Configurare il numero di appliance](./media/hybrid-cloud-extension-installation/appliance-scale.png)

9. Selezionare **continua** per ignorare.  
      
    ![Configurare la progettazione del traffico](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

10. Esaminare l'anteprima della topologia e selezionare **Continue** (Continua). 

11. Immettere un nome descrittivo per il servizio mesh e selezionare **fine** per completare l'operazione.  
      
    ![Completare la mesh del servizio](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    La mesh del servizio viene distribuita e configurata.  
      
    ![Mesh del servizio distribuita](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Verificare lo stato dell'appliance
Per verificare lo stato dell'appliance, selezionare **Interconnect (Interconnessione)**  > **Appliance**. 
      
![Stato dell'appliance](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Passaggi successivi

Quando **Tunnel Status** (Stato del tunnel) di interconnessione dell'appliance è **UP** (ATTIVO) e verde, è possibile eseguire la migrazione e proteggere le macchine virtuali AVS usando HCX. Vedere la [documentazione relativa a VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) e [Migrazione delle macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) nella documentazione tecnica di VMware.
