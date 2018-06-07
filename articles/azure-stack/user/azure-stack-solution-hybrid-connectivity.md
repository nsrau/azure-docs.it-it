---
title: Configurare la connettività di cloud ibrido con Azure e Azure Stack | Documenti Microsoft
description: Informazioni su come configurare la connettività ibrida cloud con Azure e Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/25/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 72c5c4b0f0ab752bb02e6bee7cd038afca44ee1b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605196"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Esercitazione: configurare la connettività di cloud ibrido con Azure e Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile accedere alle risorse con la protezione in Azure globale e Stack di Azure usando il modello di connettività ibrida.

In questa esercitazione si creerà un ambiente di esempio a:

> [!div class="checklist"]
> - Mantenere i dati in locale per soddisfare requisiti normativi o privacy, ma hanno accesso alle risorse di Azure globale.
> - Gestire un sistema legacy durante l'utilizzo della distribuzione di applicazioni con scalabilità cloud e le risorse in Azure globale.

## <a name="prerequisites"></a>Prerequisiti

Alcuni componenti sono necessari per compilare una distribuzione di connettività ibrida. Alcuni di questi componenti operazione richiederà un tempo di preparazione, pertanto è necessario pianificare di conseguenza.

**Azure Stack**

Un Partner OEM/Hardware di Azure è possibile distribuire un ambiente di produzione Azure Stack, mentre tutti gli utenti possono distribuire un Kit di sviluppo dello Stack di Azure (ASDK).

**Componenti di Azure Stack**

Un operatore di Stack di Azure deve distribuire il servizio App, creare i piani e alle offerte, creare una sottoscrizione tenant e aggiungere l'immagine di Windows Server 2016. Se si dispone già di alcuni di questi componenti, assicurarsi che soddisfino i requisiti prima di iniziare questa esercitazione.

Questa esercitazione si presuppone una discreta conoscenza di base dello Stack di Azure e Azure. Per altre informazioni prima di iniziare l'esercitazione, leggere gli articoli seguenti:

 - [Introduzione a Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Concetti chiave di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
 - Creare una [App Web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) in Azure. Prendere nota dell'URL dell'App Web, perché sarà necessaria nell'esercitazione.

### <a name="azure-stack"></a>Azure Stack

 - Utilizzare dello Stack di Azure di produzione o distribuire il Kit di sviluppo dello Stack di Azure da https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Distribuzione di ASDK può richiedere fino a 7 ore, pertanto, pianificare di conseguenza.

 - Distribuire [servizio App](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) servizi PaaS allo Stack di Azure.
 - [Creare i piani e alle offerte](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) nell'ambiente dello Stack di Azure.
 - [Creare una sottoscrizione tenant](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) all'interno dell'ambiente dello Stack di Azure.

### <a name="before-you-begin"></a>Prima di iniziare

Verificare che siano soddisfatti i criteri seguenti prima di iniziare a configurare la connettività ibrida cloud:

 - È necessario un indirizzo IPv4 pubblico accessibile pubblicamente per il dispositivo VPN. Questo indirizzo IP non può trovarsi dietro un NAT.
 - Tutte le risorse vengono distribuite nella stessa area o posizione.

#### <a name="tutorial-example-values"></a>Valori di esempio dell'esercitazione

Negli esempi di questa esercitazione usano i valori seguenti. È possibile utilizzare questi valori per creare un ambiente di test o vi fanno riferimento per una migliore comprensione degli esempi. Per altre informazioni sulle impostazioni generali del gateway VPN, vedere [Informazioni sulle impostazioni del gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specifiche per le connessioni:

 - **Tipo VPN**: Basato su route
 - **Tipo di connessione**: Site-to-site (IPsec)
 - **Tipo di gateway**: VPN
 - **Nome della connessione Azure**: Azure-Gateway-AzureStack-S2SGateway (portale di riempimento automatico verrà questo valore)
 - **Nome della connessione Azure Stack**: AzureStack-Gateway-Azure-S2SGateway (portale di riempimento automatico verrà questo valore)
 - **Chiave condivisa**: qualsiasi compatibile con i requisiti hardware VPN, con i valori su entrambi i lati della connessione corrispondenti
 - **Sottoscrizione**: qualsiasi preferito sottoscrizione
 - **Gruppo di risorse**: Test-infrastrutture-

Indirizzi IP di rete e subnet:

| Connessione di Azure/Azure Stack | NOME | Subnet | Indirizzo IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Rete virtuale di Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Rete virtuale di Azure Stack | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Gateway di rete virtuale di Azure | Gateway di Azure |  |  |
| Gateway di rete virtuale di Azure Stack | AzureStack Gateway |  |  |
| Indirizzo IP pubblico di Azure | Azure-GatewayPublicIP |  | Determinato al momento della creazione |
| Indirizzo IP pubblico di Azure Stack | AzureStack GatewayPublicIP |  | Determinato al momento della creazione |
| Gateway di rete locale di Azure | AzureStack S2SGateway<br>   10.100.100.0/23 |  | Valore di indirizzo IP pubblico di Azure Stack |
| Gateway di rete locale Azure Stack | Azure-S2SGateway<br>10.100.102.0/23 |  | Valore di indirizzo IP pubblico di Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Creare una rete virtuale in globale Azure e Azure Stack

Utilizzare la procedura seguente per creare una rete virtuale tramite il portale. È possibile utilizzare tali [i valori di esempio](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) se si usa questo articolo come un'esercitazione. Tuttavia, se si usa questo articolo per configurare un ambiente di produzione, sostituire le impostazioni di esempio con i valori desiderati.

> [!IMPORTANT]
> È necessario assicurarsi che non vi è una sovrapposizione di indirizzi IP in Azure o Azure Stack spazi di indirizzi della rete virtuale.

Per creare una rete virtuale in Azure:

1. Utilizzare il browser per connettersi ai [portale di Azure](http://portal.azure.com/) e accedere con l'account di Azure.
2. Selezionare **creare una risorsa**. Nel **Cerca nel marketplace** immettere `virtual network`'. Trovare **rete virtuale** nell'elenco risultati e quindi selezionare **rete virtuale**.
3. Dal **selezionare un modello di distribuzione** elenco, selezionare **Gestione risorse**, quindi selezionare **crea**.
4. Nel **crea rete virtuale**, configurare le impostazioni di rete virtuale. I nomi di campi obbligatori sono preceduti da un asterisco rosso.  Quando si immette un valore valido, l'asterisco viene modificato in un segno di spunta verde.

Per creare una rete virtuale nello Stack di Azure:

* Ripetere i passaggi precedenti (1-4) usando lo Stack di Azure **portale tenant**.

## <a name="add-a-gateway-subnet"></a>Aggiungere una subnet del gateway

Prima di connettersi alla rete virtuale a un gateway, è necessario creare la subnet del gateway per la rete virtuale che si desidera connettersi. I servizi gateway utilizzano gli indirizzi IP che si specifica nella subnet del gateway.

Nel [portale di Azure](http://portal.azure.com/), passare alla gestione risorse di rete virtuale in cui si desidera creare un gateway di rete virtuale.

1. Selezionare la rete virtuale per aprire la **reti virtuali** pagina.
2. In **impostazioni**, selezionare **subnet**.
3. Nel **subnet** pagina, selezionare **+ subnet del Gateway** per aprire la **Aggiungi subnet** pagina.

    ![Aggiungi subnet gateway](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. Il **nome** per la subnet viene compilata automaticamente con il valore 'GatewaySubnet'. Questo valore è obbligatorio per Azure di riconoscere la subnet come la subnet del gateway.
5. Modifica il **intervallo di indirizzi** valori forniti per corrispondenti ai propri requisiti di configurazione, quindi selezionare **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Creare un Gateway di rete virtuale in Azure e Azure Stack

Utilizzare la procedura seguente per creare un gateway di rete virtuale in Azure.

1. Sul lato sinistro della pagina del portale, selezionare **+** e immettere "gateway di rete virtuale" nel campo della ricerca.
2. In **risultati**, selezionare **gateway di rete virtuale**.
3. In **gateway di rete virtuale**, selezionare **Create** per aprire la **gateway di rete virtuale crea** pagina.
4. Nel **gateway di rete virtuale Create**, specificare i valori per il gateway di rete, come illustrato nel **i valori di esempio dell'esercitazione**e i valori aggiuntivi seguenti:

    - **SKU**: base
    - **Rete virtuale**: selezionare la rete virtuale creata in precedenza. La subnet del gateway che è stato creato viene selezionata automaticamente.
    - **Prima configurazione IP**: questo è l'indirizzo IP pubblico del Gateway.
        - Selezionare **configurazione IP del gateway Create**, andare al **scegliere l'indirizzo IP pubblico** pagina.
        - Selezionare **+ Crea nuovo** per aprire la **Crea indirizzo IP pubblico** pagina.
        - Immettere un **nome** per l'indirizzo IP pubblico. Lasciare la SKU come **base**, quindi selezionare **OK** per salvare le modifiche.

       > [!Note]
       > Gateway VPN attualmente supporta solo allocazione di indirizzi IP pubblici dinamici. Tuttavia, ciò non significa che l'indirizzo IP cambia dopo che è stato assegnato al gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Il ridimensionamento, reimpostare o altri interni/aggiornamenti di manutenzione per il gateway VPN non modificare l'indirizzo IP.

4. Verificare le impostazioni del gateway.
5. Selezionare **crea** per creare il gateway VPN. Vengono convalidate le impostazioni del gateway e il riquadro "Gateway di rete virtuale di distribuzione" viene visualizzato nel dashboard.

   >[!Note]
   >La creazione di un gateway può richiedere fino a 45 minuti. Potrebbe essere necessario aggiornare la pagina del portale per visualizzare lo stato di completamento.

    Dopo aver creato il gateway, è possibile visualizzare l'indirizzo IP assegnato a esso osservando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso. Per ulteriori informazioni sul gateway, selezionare il dispositivo.

6. Ripetere i passaggi precedenti (da 1 a 5) nella distribuzione di Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Creare il gateway di rete locale in Azure e Azure Stack

Il gateway di rete locale in genere fa riferimento al percorso locale. Il sito di assegnare un nome a Azure o Azure Stack può fare riferimento a e quindi specificare:

- L'indirizzo IP del dispositivo VPN locale che si sta creando una connessione per.
- I prefissi di indirizzo IP che verranno tracciati attraverso il gateway VPN al dispositivo VPN. I prefissi degli indirizzi specificati sono quelli disponibili nella rete locale.

  >[!Note]
  >Se le modifiche di rete locale o è necessario modificare l'indirizzo IP pubblico per il dispositivo VPN, è possibile aggiornare facilmente questi valori in un secondo momento.

1. Nel portale, selezionare **+ creare una risorsa**.
2. Nella casella di ricerca, immettere **gateway di rete locale**, quindi selezionare **invio** per la ricerca. Verrà restituito un elenco di risultati.
3. Selezionare **gateway di rete locale**, quindi selezionare **Create** per aprire la **gateway di rete locale crea** pagina.
4. Nel **gateway di rete locale crea**, specificare i valori per il gateway di rete locale, tramite il nostro **i valori di esempio dell'esercitazione**. Includere i valori aggiuntivi seguenti.

    - **Indirizzo IP**: questo è l'indirizzo IP pubblico del dispositivo VPN che si desidera Azure o Azure Stack a cui connettersi. Specificare un indirizzo IP pubblico valido che si trova dietro un dispositivo NAT, in modo Azure può raggiungere l'indirizzo. Se non si dispone ora l'indirizzo IP, è possibile utilizzare un valore dell'esempio come segnaposto, ma sarà necessario tornare indietro e sostituire il segnaposto con l'indirizzo IP pubblico del dispositivo VPN. Azure non è possibile connettersi al dispositivo finché non viene fornito un indirizzo valido.
    - **Lo spazio degli indirizzi**: questo è l'intervallo di indirizzi della rete che rappresenta la rete locale. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati non si sovrappongano con intervalli di altre reti che si desidera connettersi. Azure indirizzerà l'intervallo di indirizzi specificato all'indirizzo IP del dispositivo VPN locale. Se si desidera connettersi al sito locale, non è un valore di esempio, utilizzare i valori desiderati.
    - **Configurare le impostazioni BGP**: usare solo quando si configura il protocollo BGP. In caso contrario, non selezionare questa opzione.
    - **Sottoscrizione**: verificare che sia visualizzata la sottoscrizione corretta.
    - **Gruppo di risorse**: selezionare il gruppo di risorse che si desidera utilizzare. È possibile creare un nuovo gruppo di risorse oppure selezionarne uno al quale è già stato creato.
    - **Percorso**: selezionare il percorso che verrà creato in questo oggetto. È possibile selezionare lo stesso percorso della rete virtuale si trova in, ma non ti viene richiesto di farlo.
5. Dopo aver specificato i valori richiesti, selezionare **crea** per creare il gateway di rete locale.
6. Ripetere questi passaggi (1-5) nella distribuzione di Azure Stack.

## <a name="configure-your-connection"></a>Configurare la connessione

Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. Il dispositivo VPN che configuri viene definito una connessione. Per configurare la connessione, è necessario:

- Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa semplice. È consigliabile generare una chiave più complessa per l'uso effettivo.
- Indirizzo IP pubblico del gateway di rete virtuale. È possibile visualizzare l'indirizzo IP pubblico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando. Per trovare l'indirizzo IP pubblico del gateway VPN tramite il portale di Azure, passare ai gateway di rete virtuale, quindi selezionare il nome del gateway.

Utilizzare la procedura seguente per creare una connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN locale.

1. Nel portale di Azure, selezionare **+ creare una risorsa**.
2. Cercare **connessioni**.
3. In **risultati**, selezionare **connessioni**.
4. Nel **connessione**, selezionare **crea**.
5. Nel **Crea connessione**, configurare le impostazioni seguenti:

    - **Tipo di connessione**: selezionare Site-to-site (IPSec).
    - **Gruppo di risorse**: selezionare il gruppo di risorse di test.
    - **Gateway di rete virtuale**: selezionare il gateway di rete virtuale è stato creato.
    - **Gateway di rete locale**: selezionare il Gateway della rete locale è stato creato.
    - **Nome della connessione**: questo campo viene automaticamente impostato utilizzando i valori da due gateway.
    - **Chiave condivisa**: questo valore deve corrispondere al valore che si sta utilizzando per il dispositivo VPN locale. Nell'esempio dell'esercitazione viene utilizzato 'abc123', ma può (e consigliabile) utilizzare un nome più complesse. La cosa importante è che questo valore deve essere lo stesso valore specificato quando si configura il dispositivo VPN.
    - I valori di **Sottoscrizione**, **Gruppo di risorse** e **Località** sono fissi.

6. Selezionare **OK** per creare la connessione.

È possibile visualizzare la connessione nel **connessioni** pagina del gateway di rete virtuale. Lo stato verrà inviata da *sconosciuto* alla *connessione*e quindi *Succeeded*.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sui pattern di Cloud di Azure, vedere [schemi progettuali Cloud](https://docs.microsoft.com/azure/architecture/patterns).
