---
title: Configurare la connettività cloud ibrida con Azure e Azure Stack | Microsoft Docs
description: Informazioni su come configurare la connettività cloud ibrida con Azure e Azure Stack.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: a05021255c6226329f1d7a3f0e7fa8c9be756646
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546722"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Esercitazione: configurare la connettività di cloud ibrido con Azure e Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile accedere alle risorse della sicurezza globale Azure e Azure Stack usando il modello di connettività ibrida.

In questa esercitazione si creerà un ambiente di esempio:

> [!div class="checklist"]
> - Mantenere i dati in locale per soddisfare requisiti normativi o privacy, ma è possibile accedere alle risorse di Azure globale.
> - Gestione di un sistema legacy quando si usa la distribuzione di app cloud a scalabilità e le risorse in Azure globale.

> [!Tip]  
> ![ibrido-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack è un'estensione di Azure. Azure Stack offre l'agilità e innovazione del cloud computing al tuo ambiente locale e abilitare l'unico cloud ibrido che consente di compilare e distribuire le app ibride ovunque.  
> 
> Il white paper [considerazioni sulla progettazione per applicazioni ibride](https://aka.ms/hybrid-cloud-applications-pillars) esamina i concetti fondamentali della qualità del software (selezione host, scalabilità, disponibilità, resilienza, gestibilità e sicurezza) per la progettazione, distribuzione e gestione ibrida applicazioni. Le considerazioni di progettazione assistere nell'ottimizzazione della progettazione di applicazioni ibride, riducendo al minimo le sfide negli ambienti di produzione.


## <a name="prerequisites"></a>Prerequisiti

Alcuni componenti sono necessari per creare una distribuzione di connettività ibrida. Alcuni di questi componenti richiederà tempo per prepararsi, pertanto sarà necessario pianificare di conseguenza.

**Azure Stack**

Un Partner OEM/Hardware di Azure possono distribuire un ambiente di produzione Azure Stack e tutti gli utenti possono distribuire un Azure Stack Development Kit (ASDK).

**Componenti di Azure Stack**

Un operatore di Azure Stack è necessario distribuire il servizio App, creare offerte e piani, creare una sottoscrizione tenant e aggiungere l'immagine di Windows Server 2016. Se si dispone già di alcuni di questi componenti, verificare che soddisfino i requisiti prima di iniziare questa esercitazione.

Questa esercitazione si presuppone una conoscenza di base di Azure e Azure Stack. Per altre informazioni prima di iniziare l'esercitazione, vedere gli articoli seguenti:

 - [Introduzione ad Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Concetti chiave di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Se non si ha una sottoscrizione di Azure, creare un  [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)  prima di iniziare.
 - Creare un [App Web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) in Azure. Prendere nota dell'URL dell'App Web, perché sarà necessaria nell'esercitazione.

### <a name="azure-stack"></a>Azure Stack

 - Usare l'ambiente di produzione Azure Stack o distribuire Azure Stack Development Kit da https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Distribuzione di ASDK può richiedere fino a 7 ore, pertanto, pianificare di conseguenza.

 - Distribuire [servizio App](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) servizi PaaS per Azure Stack.
 - [Creare i piani e offerte](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) nell'ambiente Azure Stack.
 - [Creare una sottoscrizione tenant](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) all'interno dell'ambiente Azure Stack.

### <a name="before-you-begin"></a>Prima di iniziare

Verificare che siano soddisfatti i criteri seguenti prima di iniziare la configurazione della connettività cloud ibrido:

 - È necessario un indirizzo IPv4 pubblico accessibile pubblicamente per il dispositivo VPN. Questo indirizzo IP non può trovarsi dietro un NAT.
 - Tutte le risorse vengono distribuite nella stessa area o posizione.

#### <a name="tutorial-example-values"></a>Valori di esempio dell'esercitazione

Gli esempi in questa esercitazione usano i valori seguenti. È possibile usare questi valori per creare un ambiente di test o per una migliore comprensione degli esempi, fare riferimento a essi. Per altre informazioni sul Gateway VPN in generale, vedere le impostazioni [About VPN Gateway Settings](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specifiche per le connessioni:

 - **Tipo VPN**: basato su route
 - **Tipo di connessione**: da sito a sito (IPsec)
 - **Tipo di gateway**: VPN
 - **Nome della connessione Azure**: Azure-Gateway-AzureStack-S2SGateway (portale compilato automaticamente questo valore)
 - **Nome di connessione di Azure Stack**: AzureStack-Gateway-Azure-S2SGateway (portale compilato automaticamente questo valore)
 - **Chiave condivisa**: Qualsiasi compatibile con l'hardware VPN, con i valori in entrambi i lati della connessione corrispondenti
 - **Sottoscrizione** Qualsiasi sottoscrizione preferita
 - **Gruppo di risorse**: Test-Infra

Indirizzi IP di rete e subnet:

| Connessione di Azure/Azure Stack | NOME | Subnet | Indirizzo IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Rete virtuale di Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Rete virtuale di Azure Stack | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Gateway di rete virtuale di Azure | Azure-Gateway |  |  |
| Gateway di rete virtuale di Azure Stack | AzureStack-Gateway |  |  |
| IP pubblico di Azure | Azure-GatewayPublicIP |  | Determinato al momento della creazione |
| Indirizzo IP pubblico di Azure Stack | AzureStack-GatewayPublicIP |  | Determinato al momento della creazione |
| Gateway di rete locale di Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Valore di indirizzo IP pubblico di Azure Stack |
| Gateway di rete locale di Azure Stack | Azure-S2SGateway<br>10.100.102.0/23 |  | Valore di indirizzo IP pubblico di Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Creare una rete virtuale globale Azure e Azure Stack

Usare la procedura seguente per creare una rete virtuale usando il portale. È possibile usare questi [valori di esempio](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) se si usa questo articolo come un'esercitazione. Tuttavia, se si usa questo articolo per configurare un ambiente di produzione, sostituire le impostazioni di esempio con i propri valori.

> [!IMPORTANT]
> È necessario assicurarsi che non vi è una sovrapposizione di indirizzi IP in spazi di indirizzi della rete virtuale di Azure o Azure Stack.

Per creare una rete virtuale in Azure:

1. Il browser per connettersi a usare il [portale di Azure](https://portal.azure.com/) e accedere con l'account di Azure.
2. Selezionare **crea una risorsa**. Nel **Cerca nel marketplace** campo, immettere `virtual network`'. Trovare **rete virtuale** nell'elenco risultati e quindi selezionare **rete virtuale**.
3. Dal **selezionare un modello di distribuzione** , selezionare dalla lista **Resource Manager**e quindi selezionare **Create**.
4. Sul **crea rete virtuale**, configurare le impostazioni di rete virtuale. I nomi di campi obbligatori sono preceduti da un asterisco rosso.  Quando si immette un valore valido, l'asterisco modificato un segno di spunta verde.

Per creare una rete virtuale in Azure Stack:

* Ripetere i passaggi precedenti (1-4) tramite Azure Stack **portale tenant**.

## <a name="add-a-gateway-subnet"></a>Aggiungere una subnet del gateway

Prima di connettere la rete virtuale a un gateway, è necessario creare la subnet del gateway per la rete virtuale che si desidera connettersi. Il servizio del gateway Usa gli indirizzi IP specificati nella subnet del gateway.

Nel [portale di Azure](https://portal.azure.com/), passare alla rete virtuale di Resource Manager in cui si desidera creare un gateway di rete virtuale.

1. Selezionare la rete virtuale per aprire la **rete virtuale** pagina.
2. Nelle **le impostazioni**, selezionare **subnet**.
3. Nel **subnet** pagina, selezionare **+ subnet del Gateway** per aprire la **aggiungere subnet** pagina.

    ![Aggiungere la subnet del gateway](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. Il **Name** la subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Questo valore è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway.
5. Modifica il **intervallo indirizzi** i valori forniti a corrispondenti ai propri requisiti di configurazione e quindi selezionare **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Creare un Gateway di rete virtuale in Azure e Azure Stack

Usare la procedura seguente per creare un gateway di rete virtuale in Azure.

1. Sul lato sinistro della pagina del portale, selezionare **+**  e immettere "gateway di rete virtuale" nel campo di ricerca.
2. Nelle **risultati**, selezionare **gateway di rete virtuale**.
3. Nelle **gateway di rete virtuale**, selezionare **Create** per aprire il **crea gateway di rete virtuale** pagina.
4. Sul **crea gateway di rete virtuale**, specificare i valori per il gateway di rete, come illustrato **valori di esempio dell'esercitazione**e i valori aggiuntivi seguenti:

    - **SKU**: base
    - **Rete virtuale**: Selezionare la rete virtuale creata in precedenza. La subnet del gateway creato viene selezionata automaticamente.
    - **Prima configurazione IP**:  Questo è l'indirizzo IP pubblico del Gateway.
        - Selezionare **Crea configurazione IP gateway**, che consente di passare per il **Scegli indirizzo IP pubblico** pagina.
        - Selezionare **+ Crea nuovo** per aprire la **Crea indirizzo IP pubblico** pagina.
        - Immettere un **Name** per l'indirizzo IP pubblico. Lasciare lo SKU impostato **base**, quindi selezionare **OK** per salvare le modifiche.

       > [!Note]
       > Attualmente, i Gateway VPN supporta solo l'allocazione di indirizzi IP pubblici dinamici. Tuttavia, ciò non significa che l'indirizzo IP viene modificato dopo l'assegnazione al gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Il ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti per il gateway VPN non modificare l'indirizzo IP.

4. Verificare le impostazioni del gateway.
5. Selezionare **Create** per creare il gateway VPN. Le impostazioni del gateway vengono convalidate e viene visualizzato il riquadro "Gateway di rete virtuale di distribuzione" nel dashboard.

   >[!Note]
   >La creazione di un gateway può richiedere fino a 45 minuti. Potrebbe essere necessario aggiornare la pagina del portale per visualizzare lo stato di completamento.

    Dopo la creazione del gateway, è possibile visualizzare l'indirizzo IP assegnato esaminando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso. Per altre informazioni sul gateway, selezionare il dispositivo.

6. Ripetere i passaggi precedenti (1-5) nella distribuzione di Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Creare il gateway di rete locale in Azure e Azure Stack

Il gateway di rete locale in genere fa riferimento al percorso locale. Assegnare al sito un nome che Azure o Azure Stack può fare riferimento a e quindi specificare:

- L'indirizzo IP del dispositivo VPN da sito locale che si sta creando una connessione.
- I prefissi di indirizzo IP che verranno instradati tramite il gateway VPN al dispositivo VPN. I prefissi degli indirizzi specificati sono quelli disponibili nella rete locale.

  >[!Note]
  >Se le modifiche di rete in locale o è necessario modificare l'indirizzo IP pubblico per il dispositivo VPN, è possibile aggiornare facilmente questi valori in un secondo momento.

1. Nel portale, selezionare **+ crea una risorsa**.
2. Nella casella di ricerca, immettere **gateway di rete locale**, quindi selezionare **invio** da cercare. Verrà restituito un elenco di risultati.
3. Selezionare **gateway di rete locale**, quindi selezionare **Create** per aprire la **crea gateway di rete locale** pagina.
4. Sul **crea gateway di rete locale**, specificare i valori per il gateway di rete locale, con nostro **valori di esempio dell'esercitazione**. Includere i valori aggiuntivi seguenti.

    - **Indirizzo IP**: Questo è l'indirizzo IP pubblico del dispositivo VPN da Azure o Azure Stack a cui connettersi. Specificare un indirizzo IP pubblico valido che non è protetto da un dispositivo NAT, in modo che Azure possa raggiungere l'indirizzo. Se ora si ha l'indirizzo IP, è possibile usare un valore dell'esempio come segnaposto, ma è possibile tornare indietro e sostituire il segnaposto con l'indirizzo IP pubblico del dispositivo VPN. Azure non è possibile connettersi al dispositivo finché non fornisci un indirizzo valido.
    - **Addressspace**: Questo è l'intervallo di indirizzi della rete che rappresenta la rete locale. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati non si sovrappongano agli intervalli di altre reti che si desidera connettersi a. Azure indirizzerà l'intervallo di indirizzi specificato all'indirizzo IP del dispositivo VPN locale. Se si desidera connettersi al sito locale, non un valore di esempio, usare i propri valori.
    - **Configurare le impostazioni BGP**: usare solo quando si configura BGP. In caso contrario, non selezionare questa opzione.
    - **Sottoscrizione** verificare che sia visualizzata la sottoscrizione corretta.
    - **Gruppo di risorse**: selezionare il gruppo di risorse che si vuole usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno al quale è già stato creato.
    - **Località**: selezionare la località in cui verrà creato questo oggetto. È possibile selezionare lo stesso percorso che la rete virtuale si trova in, ma non viene richiesto di farlo.
5. Dopo aver specificato i valori richiesti, selezionare **Create** per creare il gateway di rete locale.
6. Ripetere questi passaggi (1-5) per la distribuzione di Azure Stack.

## <a name="configure-your-connection"></a>Configurare la connessione

Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. Il dispositivo VPN che è configurare si intende una connessione. Per configurare la connessione, è necessario:

- Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa semplice. È consigliabile generare una chiave più complessa per l'uso effettivo.
- Indirizzo IP pubblico del gateway di rete virtuale. È possibile visualizzare l'indirizzo IP pubblico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando. Per trovare l'indirizzo IP pubblico del gateway VPN usando il portale di Azure, passare al gateway di rete virtuale, quindi selezionare il nome del gateway.

Usare la procedura seguente per creare una connessione VPN Site-to-Site tra il gateway di rete virtuale e il dispositivo VPN locale.

1. Nel portale di Azure, selezionare **+ crea una risorsa**.
2. Cercare **connessioni**.
3. Nelle **risultati**, selezionare **connessioni**.
4. Sul **Connection**, selezionare **crea**.
5. Sul **Crea connessione**, configurare le impostazioni seguenti:

    - **Tipo di connessione**: Selezionare Site-to-site (IPSec).
    - **Gruppo di risorse**: Selezionare il gruppo di risorse di test.
    - **Gateway di rete virtuale**: Selezionare il gateway di rete virtuale creato.
    - **Gateway di rete locale**: Selezionare il Gateway di rete locale creata.
    - **Nome della connessione**: Viene automaticamente popolato usando i valori da due gateway.
    - **Chiave condivisa**: Questo valore deve corrispondere al valore che si usa per il dispositivo VPN locale. Nell'esempio dell'esercitazione viene usato "abc123", ma è possibile (e consigliabile) utilizzare un elemento più complesso. La cosa importante è che questo valore deve essere lo stesso valore specificato durante la configurazione del dispositivo VPN.
    - I valori di **Sottoscrizione**, **Gruppo di risorse** e **Località** sono fissi.

6. Selezionare **OK** per creare la connessione.

È possibile visualizzare la connessione nel **connessioni** pagina del gateway di rete virtuale. Lo stato passa da *sconosciuto* al *Connecting*, quindi a *Succeeded*.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui modelli Cloud di Azure, vedere [Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns).
