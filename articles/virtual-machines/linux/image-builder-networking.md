---
title: Opzioni di rete del servizio Azure Image Builder
description: Informazioni sulle opzioni di rete durante la distribuzione del servizio Generatore di immagini di VM di Azure
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 531c423e42338b72b41c54466d5bfe8a89cd3c45
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969638"
---
# <a name="azure-image-builder-service-networking-options"></a>Opzioni di rete del servizio Azure Image Builder

È necessario scegliere di distribuire il generatore di immagini di Azure con o senza un VNET esistente.

## <a name="deploy-without-specifying-an-existing-vnet"></a>Distribuire senza specificare un VNET esistente

Se non si specifica un VNET esistente, Azure Image Builder crea una VNET e una subnet nel gruppo di risorse di gestione temporanea. Una risorsa IP pubblico viene usata con un gruppo di sicurezza di rete per limitare il traffico in ingresso al servizio Generatore di immagini di Azure. L'IP pubblico facilita il canale per i comandi di Azure Image Builder durante la compilazione dell'immagine. Al termine della compilazione, vengono eliminati la macchina virtuale, l'indirizzo IP pubblico, i dischi e la VNET. Per usare questa opzione, non specificare alcuna proprietà VNET.

## <a name="deploy-using-an-existing-vnet"></a>Eseguire la distribuzione usando un VNET esistente

Se si specifica un VNET e una subnet, Azure Image Builder distribuisce la macchina virtuale di compilazione nel VNET selezionato. È possibile accedere alle risorse accessibili nella VNET. È anche possibile creare un VNET silo non connesso ad altri VNET. Se si specifica un VNET, Azure Image Builder non usa un indirizzo IP pubblico. La comunicazione dal servizio Generatore di immagini di Azure alla VM di compilazione usa la tecnologia di collegamento privato di Azure.

Per ulteriori informazioni, vedere uno degli esempi seguenti:

* [Usare Azure Image Builder per macchine virtuali Windows consentendo l'accesso a una VNET di Azure esistente](../windows/image-builder-vnet.md)
* [Usare il generatore di immagini di Azure per macchine virtuali Linux consentendo l'accesso a una VNET di Azure esistente](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>Che cos'è Collegamento privato di Azure?

Il collegamento privato di Azure fornisce la connettività privata da una rete virtuale a una piattaforma distribuita come servizio (PaaS) di Azure, di proprietà del cliente o di servizi partner Microsoft. Semplifica l'architettura di rete e protegge la connessione tra gli endpoint in Azure eliminando l'esposizione dei dati alla rete Internet pubblica. Per ulteriori informazioni, vedere la [documentazione del collegamento privato](../../private-link/index.yml).

### <a name="required-permissions-for-an-existing-vnet"></a>Autorizzazioni necessarie per un VNET esistente

Il generatore di immagini di Azure richiede autorizzazioni specifiche per l'uso di un VNET esistente. Per altre informazioni, vedere [configurare le autorizzazioni del servizio Azure Image Builder usando l'interfaccia](image-builder-permissions-cli.md) della riga di comando di Azure o [configurare le autorizzazioni del servizio Azure Image Builder con PowerShell](image-builder-permissions-powershell.md).

### <a name="what-is-deployed-during-an-image-build"></a>Cosa viene distribuito durante una compilazione di immagini?

Se si usa un VNET esistente, Azure Image Builder distribuisce una VM aggiuntiva (macchina virtuale proxy) e un Azure Load Balancer (ALB) connesso al collegamento privato. Il traffico dal servizio AIB passa attraverso il collegamento privato al ALB. Il ALB comunica alla VM proxy usando la porta 60001 per il sistema operativo Linux o 60000 per il sistema operativo Windows. Il proxy trasmette i comandi alla macchina virtuale di compilazione usando la porta 22 per il sistema operativo Linux o 5986 per il sistema operativo Windows.

> [!NOTE]
> Il VNET deve trovarsi nella stessa area dell'area del servizio Generatore di immagini di Azure.
> 

### <a name="why-deploy-a-proxy-vm"></a>Vantaggi della distribuzione di una VM proxy

Quando una macchina virtuale senza IP pubblico è dietro una Load Balancer interna, non ha accesso a Internet. Il Azure Load Balancer usato per VNET è Internal. La VM proxy consente l'accesso a Internet per la macchina virtuale di compilazione durante le compilazioni. I gruppi di sicurezza di rete associati possono essere usati per limitare l'accesso alla macchina virtuale di compilazione.

Le dimensioni della VM del proxy distribuito sono A1_v2 standard oltre alla macchina virtuale di compilazione. La VM proxy viene usata per inviare comandi tra il servizio Generatore di immagini di Azure e la macchina virtuale di compilazione. Non è possibile modificare le proprietà della VM del proxy, incluse le dimensioni o il sistema operativo. Non è possibile modificare le proprietà della VM del proxy per le compilazioni di immagini Windows e Linux.

### <a name="image-template-parameters-to-support-vnet"></a>Parametri del modello di immagine per supportare VNET
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| Impostazione | Descrizione |
|---------|---------|
| name | Opzionale Nome di una rete virtuale già esistente. |
| subnetName | Nome della subnet all'interno della rete virtuale specificata. Deve essere specificato se e solo se il *nome* è specificato. |
| resourceGroupName | Nome del gruppo di risorse che contiene la rete virtuale specificata. Deve essere specificato se e solo se il *nome* è specificato. |

Il servizio di collegamento privato richiede un indirizzo IP dalla subnet e dal VNET specificati. Attualmente, Azure non supporta i criteri di rete in questi IP. Di conseguenza, è necessario disabilitare i criteri di rete nella subnet. Per ulteriori informazioni, vedere la [documentazione del collegamento privato](../../private-link/index.yml).

### <a name="checklist-for-using-your-vnet"></a>Elenco di controllo per l'uso di VNET

1. Consenti a Azure Load Balancer (ALB) di comunicare con la macchina virtuale del proxy in un NSG
    * [Esempio AZ CLI](image-builder-vnet.md#add-network-security-group-rule)
    * [Esempio di PowerShell](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. Disabilitare i criteri del servizio privato nella subnet
    * [Esempio AZ CLI](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [Esempio di PowerShell](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Consentire a Generatore di immagini di Azure di creare un ALB e aggiungere macchine virtuali a VNET
    * [Esempio AZ CLI](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [Esempio di PowerShell](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Consenti a Generatore di immagini di Azure di leggere/scrivere immagini di origine e creare immagini
    * [Esempio AZ CLI](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [Esempio di PowerShell](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Assicurarsi di usare VNET nella stessa area dell'area del servizio Generatore di immagini di Azure.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Panoramica di Azure Image Builder](image-builder-overview.md).