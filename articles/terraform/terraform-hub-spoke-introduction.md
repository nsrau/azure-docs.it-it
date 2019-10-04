---
title: Creare una topologia di rete ibrida hub-spoke con Terraform in Azure
description: Esercitazione che illustra come creare un'intera architettura di riferimento di rete ibrida in Azure usando Terraform
services: terraform
ms.service: azure
keywords: terraform, hub-spoke, reti, reti ibride, devops, macchina virtuale, azure, peering reti virtuali, appliance virtuale di rete
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 5c2a61dd9da6d233a4b1410042f2125a1c300758
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173444"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-with-terraform-in-azure"></a>Esercitazione: Creare una topologia di rete ibrida hub-spoke con Terraform in Azure

Questa serie di esercitazioni illustra come usare Terraform per implementare in Azure una [topologia di rete hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

Una topologia hub-spoke consente di isolare i carichi di lavoro e allo stesso tempo di condividere i servizi comuni. Questi servizi includono l'identità e la sicurezza. L'hub è una rete virtuale che funge da punto centrale di connessione a una rete locale. Gli spoke sono reti virtuali che eseguono il peering con l'hub. I servizi condivisi vengono distribuiti nell'hub, mentre i singoli carichi di lavoro vengono distribuiti all'interno delle reti spoke.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Usare HCL (HashiCorp Language) per disporre le risorse dell'architettura di riferimento di rete ibrida hub-spoke
> * Usare Terraform per creare risorse per l'appliance di rete hub
> * Usare Terraform per creare in Azure una rete hub che funga da punto comune per tutte le risorse
> * Usare Terraform per creare i singoli carichi di lavoro come reti virtuali spoke in Azure
> * Usare Terraform per stabilire gateway e connessioni tra la rete locale e la rete di Azure
> * Usare Terraform per creare peering reti virtuali alle reti spoke

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: se ancora non si dispone di una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

- **Installare e configurare Terraform**: per eseguire il provisioning di macchine virtuali e altra infrastruttura in Azure, [installare e configurare Terraform](/azure/virtual-machines/linux/terraform-install-configure).

## <a name="hub-and-spoke-topology-architecture"></a>Architettura della topologia hub-spoke

Nella topologia hub-spoke l'hub è una rete virtuale. Tale rete virtuale funge da punto centrale di connettività alla rete locale. Gli spoke sono le reti virtuali peer con l'hub, e possono essere usati per isolare i carichi di lavoro. Flussi di traffico tra il data center locale e l'hub attraverso una connessione di gateway VPN o ExpressRoute. L'immagine seguente illustra i componenti in una topologia hub-spoke:

![Architettura della topologia hub-spoke in Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Vantaggi della topologia hub-spoke

Una topologia di rete hub-spoke consente di isolare i carichi di lavoro e allo stesso tempo di condividere i servizi comuni. Questi servizi includono l'identità e la sicurezza. L'hub è una rete virtuale che funge da punto centrale di connessione a una rete locale. Gli spoke sono reti virtuali che eseguono il peering con l'hub. I servizi condivisi vengono distribuiti nell'hub, mentre i singoli carichi di lavoro vengono distribuiti all'interno delle reti spoke. Di seguito sono descritti alcuni dei vantaggi offerti dalla topologia di rete hub-spoke:

- **Risparmio sui costi** tramite la centralizzazione dei servizi in un'unica posizione che può essere condivisa da più carichi di lavoro. Questi carichi di lavoro includono appliance virtuali di rete e server DNS.
- **Superamento dei limiti delle sottoscrizioni** eseguendo il peering delle reti virtuali da sottoscrizioni diverse all'hub centrale.
- **Separazione dei compiti** tra IT centrale (SecOPs, InfraOps) e carichi di lavoro (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Usi tipici dell'architettura hub-spoke

Di seguito sono descritti alcuni degli usi tipici di un'architettura hub-spoke:

- Molti clienti hanno carichi di lavoro che vengono distribuiti in ambienti diversi. Si tratta, ad esempio, di ambienti di sviluppo, testing e produzione. In molti casi questi carichi di lavoro devono condividere servizi quali DNS, IDS, NTP o Active Directory Domain Services. Questi servizi condivisi possono essere collocati nella rete virtuale hub. In questo modo ogni ambiente viene distribuito a uno spoke per mantenere l'isolamento.
- Carichi di lavoro che non richiedono connettività uno con l'altro, ma richiedono l'accesso ai servizi condivisi.
- Aziende che richiedono un controllo centralizzato sugli aspetti correlati alla sicurezza.
- Aziende che richiedono una gestione separata per i carichi di lavoro in ogni spoke.

## <a name="preview-the-demo-components"></a>Anteprima dei componenti per la demo

Mentre si eseguono le singole esercitazioni di questa serie, si usano vari componenti definiti in script Terraform distinti. L'architettura creata e distribuita per la demo è costituita dai componenti seguenti:

- **Rete locale**. Una rete LAN privata in esecuzione con un'organizzazione. Per l'architettura di riferimento hub-spoke, viene usata una rete virtuale in Azure per simulare una rete locale.

- **Dispositivo VPN**. Un servizio o un dispositivo VPN offre connettività esterna alla rete locale. Il dispositivo VPN può essere un'appliance hardware o una soluzione software. 

- **Rete virtuale dell'hub**. L'hub è il punto centrale di connettività alla rete locale e la posizione in cui vengono ospitati i servizi. Questi servizi possono essere usati dai diversi carichi di lavoro ospitati nelle reti virtuali spoke.

- **Subnet del gateway**: I gateway di rete virtuale vengono mantenuti nella stessa subnet.

- **Reti virtuali spoke**. Gli spoke possono essere usati per isolare i carichi di lavoro nelle reti virtuali corrispondenti, gestite separatamente rispetto agli altri spoke. Ogni carico di lavoro può includere più livelli, con più subnet connesse tramite i servizi di bilanciamento del carico di Azure. 

- **Peering reti virtuali**. È possibile connettere due reti virtuali tramite una connessione peering. Le connessioni peering sono connessioni non transitive a bassa latenza tra reti virtuali. Dopo il peering, le reti virtuali si scambiano traffico tramite il backbone di Azure, senza che sia necessario un router. In una topologia di rete hub-spoke viene usato il peering reti virtuali per connettere l'hub a ogni spoke. È possibile eseguire il peering di reti virtuali nella stessa area o in aree differenti.

## <a name="create-the-directory-structure"></a>Creare la struttura di directory

Creare la directory che deve contenere i file di configurazione Terraform per la demo.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Aprire [Azure Cloud Shell](/azure/cloud-shell/overview). Se in precedenza non è stato selezionato un ambiente, selezionare **Bash** come ambiente.

    ![Prompt di Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Passare alla directory `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crea una directory denominata `hub-spoke`.

    ```bash
    mkdir hub-spoke
    ```

1. Passare alla nuova directory:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>Dichiarare il provider di Azure

Creare il file di configurazione Terraform che dichiara il provider di Azure.

1. In Cloud Shell aprire un nuovo file denominato `main.tf`.

    ```bash
    code main.tf
    ```

1. Incollare il codice seguente nell'editor:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Salvare il file e uscire dall'editor.

## <a name="create-the-variables-file"></a>Creare il file delle variabili

Creare il file di configurazione Terraform per le variabili comuni usate nei diversi script.

1. In Cloud Shell aprire un nuovo file denominato `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Incollare il codice seguente nell'editor:

    ```hcl
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. Salvare il file e uscire dall'editor.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Creare una rete virtuale locale con Terraform in Azure](./terraform-hub-spoke-on-prem.md)
