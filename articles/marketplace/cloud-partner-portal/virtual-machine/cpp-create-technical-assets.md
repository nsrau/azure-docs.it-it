---
title: Creare risorse tecniche per un'offerta di macchina virtuale per Azure Marketplace
description: Spiega come creare le risorse tecniche necessarie per un'offerta di macchina virtuale in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 08/20/2018
ms.author: pabutler
ms.openlocfilehash: c1ef00f846dfad76629b0603ab79fba17249417c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224519"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Creare risorse tecniche per un'offerta di macchina virtuale

Questa sezione illustra in dettaglio la creazione e la configurazione delle risorse tecniche per un'offerta di macchina virtuale (VM, Virtual Machine) per Azure Marketplace.  Una macchina virtuale è costituita da due componenti: il disco rigido virtuale (VHD, Virtual Hard Disk) e dischi dati associati facoltativi.  

- Il *disco rigido virtuale (VHD)* contiene il sistema operativo e la soluzione che verrà distribuita con l'offerta in Azure Marketplace. Il processo di preparazione del disco rigido virtuale è diverso a seconda del fatto che sia basato su Linux, su Windows o su una macchina virtuale personalizzata.
- I *dischi dati* rappresentano risorse di archiviazione dedicate e persistenti della macchina virtuale. *Non* usare il disco rigido virtuale della soluzione (ad esempio, l'unità `C:`) per archiviare informazioni persistenti.

Un'immagine di macchina virtuale contiene un disco del sistema operativo e zero o più dischi dati. È necessario un disco rigido virtuale per ogni disco. È necessario crearne uno anche per i dischi dati vuoti.
È necessario configurare il sistema operativo della macchina virtuale, le dimensioni di questa, le porte da aprire e fino a 15 dischi dati collegati.

> [!TIP] 
> Indipendentemente dal sistema operativo usato, aggiungere solo il numero minimo di dischi dati necessari per lo SKU. In fase di distribuzione i clienti non possono rimuovere dischi che fanno parte di un'immagine, ma possono sempre aggiungerne altri durante o dopo la distribuzione. 

> [!IMPORTANT]
> *Non modificare il numero di dischi in una nuova versione dell'immagine.* Se è necessario riconfigurare i dischi dati nell'immagine, definire un nuovo SKU. La pubblicazione di una nuova versione dell'immagine con un numero di dischi diverso può compromettere le attività di distribuzione basate sulla nuova versione dell'immagine in caso di scalabilità automatica, distribuzione automatica di soluzioni tramite modelli di Azure Resource Manager e altri scenari.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Conoscenze tecniche fondamentali

La progettazione, la compilazione e il test di questi asset richiedono tempo e richiedono conoscenze tecniche sulla piattaforma Azure e sulle tecnologie usate per creare l'offerta. Oltre al dominio della soluzione, il team di progettazione deve conoscere le tecnologie Microsoft seguenti: 
-   Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/) 
-   Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
-   Conoscenza pratica di [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [Archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage) e [Rete di Azure](https://azure.microsoft.com/services/?filter=networking)
-   Conoscenza pratica di [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Conoscenza pratica di [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Strumenti suggeriti 

Per la gestione di dischi rigidi virtuali e macchine virtuali, scegliere uno o entrambi gli ambienti di script seguenti:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)

È anche consigliabile aggiungere gli strumenti seguenti all'ambiente di sviluppo in uso: 

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

È anche consigliabile esaminare gli strumenti disponibili nella pagina [Strumenti di sviluppo di Azure](https://azure.microsoft.com/tools/) e, se si usa Visual Studio, in [Visual Studio Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Passaggi successivi

Gli articoli successivi in questa sezione illustrano in dettaglio la procedura di creazione e registrazione di risorse di macchina virtuale:

1. [Create an Azure-compatible virtual hard disk](./cpp-create-vhd.md) (Creare un disco rigido virtuale compatibile con Azure) spiega come creare un disco rigido virtuale basato su Linux o Windows compatibile con Azure.  Include procedure consigliate, ad esempio il ridimensionamento, l'applicazione di patch e la preparazione della macchina virtuale per il caricamento.

2. [Connect to the virtual machine](./cpp-connect-vm.md) (Connettersi alla macchina virtuale) illustra come connettersi e accedere alla macchina virtuale appena creata in modalità remota.  Questo articolo spiega anche come arrestare la macchina virtuale per risparmiare sui costi di utilizzo.

3. [Configurare la macchina virtuale](./cpp-configure-vm.md) spiega come scegliere la dimensione del disco rigido virtuale appropriata, generalizzare l'immagine, applicare gli aggiornamenti recenti (patch) e pianificare le configurazioni personalizzate.

4. [Deploy a virtual machine from a virtual hard disk](./cpp-deploy-vm-vhd.md) (Distribuire una macchina virtuale da un disco rigido virtuale) spiega come registrare una macchina virtuale da un disco rigido virtuale distribuito in Azure.  Elenca gli strumenti necessari e indica come usarli per creare un'immagine di macchina virtuale utente e quindi distribuirla in Azure usando il [portale di Microsoft Azure](https://ms.portal.azure.com/) o script di PowerShell. 

5. [Certify a virtual machine image](./cpp-certify-vm.md) (Certificare l'immagine di una macchina virtuale) spiega come testare e inviare un'immagine di VM per la certificazione Azure Marketplace. Spiega dove ottenere lo *strumento di test della certificazione per Azure Certified* e come usare questo strumento per certificare l'immagine di macchina virtuale. 

6. [Get SAS URI](./cpp-get-sas-uri.md) (Ottenere l'URI di firma di accesso condiviso) illustra come ottenere l'URI di firma di accesso condiviso per le immagini di macchina virtuale.
 
Come articolo di supporto, [Problemi comuni degli URL di firma di accesso condiviso e relative correzioni](./cpp-common-sas-url-issues.md) elenca alcuni problemi comuni che è possibile riscontrare usando gli URI di firma di accesso condiviso e le relative soluzioni possibili.

Dopo aver completato tutti questi passaggi, sarà possibile [pubblicare l'offerta di macchina virtuale](./cpp-publish-offer.md) in Azure Marketplace.
