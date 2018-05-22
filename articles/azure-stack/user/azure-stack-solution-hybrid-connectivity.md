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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 048e2636aabe406728c8fe1b93ef861f13346256
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Esercitazione: configurare la connettività di cloud ibrido con Azure e Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile accedere alle risorse con la protezione in Azure globale e Stack di Azure usando il modello di connettività ibrida. 

In questa esercitazione si creerà un ambiente di esempio a:

> [!div class="checklist"]
> - Mantenere i dati in locale per la privacy o requisiti normativi, ma dispone dell'accesso alle risorse di Azure globale.
> - Gestire un sistema legacy durante l'utilizzo della distribuzione di applicazioni con scalabilità cloud e le risorse in Azure globale.

## <a name="prerequisites"></a>Prerequisiti

Alcuni componenti necessari per compilare una connettività distribuzione ibrida e potrebbero richiedere alcuni minuti per preparare. 

Un Partner OEM/Hardware di Azure può distribuire un ambiente di produzione Azure Stack e tutti gli utenti possono distribuire un ASDK. Anche un operatore di Stack di Azure deve distribuire il servizio App, creare i piani e alle offerte, creare una sottoscrizione tenant e aggiungere l'immagine di Windows Server 2016. 

Se si dispone già di alcuni di questi componenti, verificare che soddisfino i requisiti prima di iniziare.

In questo argomento si presuppone inoltre che si dispone di conoscenza dello Stack di Azure e Azure. Se si desidera per altre informazioni prima di procedere, assicurarsi di avviare con questi argomenti:
 - [Introduzione a Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Concetti chiave di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure
 - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
 - Creare una [App Web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) in Azure. Prendere nota dell'URL del nuovo Web App, perché è usato in un secondo momento.

### <a name="azure-stack"></a>Azure Stack
 - Usare il telefono produzione Azure Stack o distribuire Azure Stack Development Kit riportate di seguito:
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - In genere, l'installazione richiede alcune ore per completare, pertanto, pianificare di conseguenza.
 - Distribuire [servizio App](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) servizi PaaS allo Stack di Azure. 
 - [Crea piano/offerte](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) all'interno dell'ambiente dello Stack di Azure. 
 - [Creare una sottoscrizione tenant](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) all'interno dell'ambiente dello Stack di Azure. 


### <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

 - Verificare di avere un indirizzo IPv4 pubblico esterno per il dispositivo VPN. L'indirizzo IP non può trovarsi dietro un NAT.
 - Verificare che tutte le risorse distribuite nella stessa area o posizione.

#### <a name="example-values"></a>Valori di esempio

Gli esempi di questo articolo usano i valori seguenti. È possibile utilizzare questi valori per creare un ambiente di test o per averle a disposizione per comprendere meglio gli esempi in questo articolo. Per altre informazioni sulle impostazioni generali del gateway VPN, vedere [Informazioni sulle impostazioni del gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specifiche per le connessioni:
 - **Tipo VPN**: Basato su route
 - **Tipo di connessione**: Site-to-site (IPsec)
 - **Tipo di gateway**: VPN
 - **Nome della connessione Azure**: Azure-Gateway-AzureStack-S2SGateway (portale di riempimento automatico verrà questo valore)
 - **Nome della connessione Azure Stack**: AzureStack-Gateway-Azure-S2SGateway (portale di riempimento automatico verrà questo valore)
 - **Chiave condivisa**: qualsiasi compatibile con i requisiti hardware VPN, con i valori su entrambi i lati della connessione corrispondenti
 - **Sottoscrizione**: qualsiasi preferito sottoscrizione
 - **Gruppo di risorse**: Test-infrastrutture-

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

> [!Note]  
> È necessario assicurarsi che non si verificano sovrapposizioni di indirizzi IP in Azure o Azure Stack spazi di indirizzi della rete virtuale. 

Per creare una rete virtuale nel modello di distribuzione di gestione risorse tramite il portale di Azure. Usare i [valori di esempio](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) se si usa questa procedura come esercitazione. In caso contrario, assicurarsi di sostituire i valori di esempio con valori reali. 

1. In un browser passare al [portale di Azure](http://portal.azure.com/) e accedere con l'account Azure.
2. Fare clic su **Crea una risorsa**. Nel **Cerca nel marketplace** immettere `virtual network`'. Individuare **rete virtuale** nell'elenco restituito e aprire la **rete virtuale** pagina.
3. Nella parte inferiore della pagina rete virtuale, dal **selezionare un modello di distribuzione** , selezionare **Gestione risorse**, quindi selezionare **crea**. Verrà visualizzata la pagina "Crea rete virtuale".
4. Nella pagina **Crea rete virtuale** configurare le impostazioni della rete virtuale. Durante la compilazione dei campi, il punto esclamativo rosso diventa un segno di spunta verde se i caratteri immessi nel campo sono validi.
5. Ripetere i passaggi dal **portale tenant** dello Stack di Azure.

## <a name="add-a-gateway-subnet"></a>Aggiungere una subnet del gateway

Prima di connettere la rete virtuale a un gateway, è necessario creare la subnet del gateway per la rete virtuale con cui si vuole stabilire la connessione. Il servizio del gateway usa gli indirizzi IP specificati nella subnet del gateway.

Nel [portale](http://portal.azure.com/) passare alla rete virtuale di Resource Manager per cui si vuole creare un gateway di rete virtuale.

1. Nel **impostazioni** sezione della pagina di rete virtuale, seleziona **subnet** per espandere la **subnet** pagina.
2. Nel **subnet** pagina, selezionare **+ subnet del Gateway** per aprire la **Aggiungi subnet** pagina.

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Questo valore è obbligatorio per Azure di riconoscere la subnet come la subnet del gateway. Modificare il riempimento automatico **intervallo di indirizzi** i valori corrispondano a requisiti di configurazione, quindi selezionare **OK** nella parte inferiore della pagina per creare la subnet.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Creare un Gateway di rete virtuale in Azure e Azure Stack

1. Sul lato sinistro della pagina del portale, selezionare + e immettere 'Gateway della rete virtuale' nella ricerca. In **risultati**, individuare e selezionare **gateway di rete virtuale**.
2. In fondo il **gateway di rete virtuale** pagina, selezionare **crea**. Verrà visualizzata la pagina **Crea gateway di rete virtuale**.
3. Nel **gateway di rete virtuale crea** , specificare i valori per il gateway di rete virtuale, come descritto in dettaglio i valori di esempio oltre i valori aggiuntivi descritti di seguito.
    - **SKU**: base
    - **Rete virtuale**: selezionare la rete virtuale creata in precedenza. La subnet del gateway creato in precedenza verranno automaticamente selezionati. 
    - **Prima configurazione IP**: questo è l'indirizzo IP pubblico del Gateway. 
        - Fare clic sul **configurazione IP del gateway Create** e verrà visualizzata per il **scegliere l'indirizzo IP pubblico** pagina.
        - Fare clic su **+ Crea nuovo** per aprire la **Crea indirizzo IP pubblico** pagina.
        - Immettere un **nome** per l'indirizzo IP pubblico. Lasciare la SKU come **base**, quindi selezionare **OK** nella parte inferiore della pagina per salvare le modifiche.

    > [!Note]  
    > Gateway VPN supporta attualmente solo allocazione di indirizzi IP pubblici dinamici. Ciò non significa tuttavia che l'indirizzo IP viene modificato dopo l'assegnazione al gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

4. Verificare le impostazioni. 
5. Fare clic su **Crea** per iniziare a creare il gateway VPN. Le impostazioni verranno convalidate e nel dashboard verrà visualizzato il riquadro relativo alla distribuzione del gateway di rete virtuale. La creazione di un gateway può richiedere fino a 45 minuti. Potrebbe essere necessario aggiornare la pagina del portale per visualizzare lo stato di completamento.

    Dopo la creazione del gateway, è possibile visualizzare l'indirizzo IP assegnato esaminando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso. È possibile selezionare il dispositivo connesso (il gateway di rete virtuale) per visualizzare ulteriori informazioni.
6. Ripetere questi passaggi per la distribuzione dello Stack di Azure.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Creare il gateway di rete locale in Azure e Azure Stack

Il gateway di rete locale in genere fa riferimento al percorso locale. Si assegna il sito di un nome con cui Azure o Azure Stack possibile farvi riferimento, quindi specificare l'indirizzo IP del dispositivo VPN da sito locale a cui verrà creata una connessione. Specificare anche i prefissi degli indirizzi IP che verranno instradati tramite il gateway VPN al dispositivo VPN. I prefissi degli indirizzi specificati sono quelli disponibili nella rete locale. Se la rete locale viene modificata o è necessario modificare l'indirizzo IP pubblico del dispositivo VPN, è possibile aggiornare facilmente i valori in un secondo momento.

1. Nel portale, selezionare **+ creare una risorsa**.
2. Nella casella di ricerca, immettere **gateway di rete locale**, quindi premere **invio** per la ricerca. Verrà restituito un elenco di risultati. Fare clic su **gateway di rete locale**, quindi selezionare il **Create** pulsante per aprire la **gateway di rete locale crea** pagina.
3. Nel **pagina gateway di rete locale crea**, specificare i valori per il gateway di rete locale, come descritto in dettaglio i valori di esempio oltre i valori aggiuntivi descritti di seguito.
    - **Indirizzo IP**: questo è l'indirizzo IP pubblico del dispositivo VPN che si desidera Azure o Azure Stack a cui connettersi. Specificare un indirizzo IP pubblico valido: non può essere protetto da NAT e deve essere raggiungibile da Azure. Se al momento non è disponibile l'indirizzo IP, è possibile usare i valori visualizzati nell'esempio, ma sarà necessario tornare indietro e sostituire l'indirizzo IP segnaposto con l'indirizzo IP pubblico del dispositivo VPN. In caso contrario, Azure non sarà in grado di connettersi.
    - **Spazio di indirizzi** fa riferimento agli intervalli di indirizzi per la rete rappresentata da questa rete locale. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati non si sovrappongano con gli intervalli di altre reti a cui ci si vuole connettere. Azure indirizzerà l'intervallo di indirizzi specificato all'indirizzo IP del dispositivo VPN locale. Utilizzare i valori in questo caso, se si desidera connettersi al sito locale, non i valori mostrati nell'esempio.
    - **Configurare le impostazioni BGP**: usare solo quando si configura il protocollo BGP. In caso contrario, non selezionare questa opzione.
    - **Sottoscrizione**: verificare che sia visualizzata la sottoscrizione corretta.
    - **Gruppo di risorse**: selezionare il gruppo di risorse che si desidera utilizzare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già creato.
    - **Percorso**: selezionare il percorso che verrà creato in questo oggetto. È possibile, ma non necessario, selezionare la stessa località in cui risiede la rete virtuale.
4. Dopo aver specificato i valori, selezionare il **crea** pulsante nella parte inferiore della pagina per creare il gateway di rete locale.
5. Ripetere questi passaggi per la distribuzione dello Stack di Azure.

## <a name="configure-your-connection"></a>Configurare la connessione

Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. In questo passaggio, configurare il dispositivo VPN, noto come una connessione. Quando si configura la connessione, è necessario quanto segue:
    - Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa semplice. È consigliabile generare una chiave più complessa per l'uso effettivo.
    - Indirizzo IP pubblico del gateway di rete virtuale. È possibile visualizzare l'indirizzo IP pubblico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando. Per trovare l'indirizzo IP pubblico del gateway VPN tramite il portale di Azure, passare ai gateway di rete virtuale, quindi selezionare il nome del gateway.
Creare la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN locale.
1. Nel portale, selezionare **+ creare una risorsa**.
2. Nella casella di ricerca, immettere **connessioni**, quindi premere **invio** per la ricerca. Verrà restituito un elenco di risultati. Fare clic su **connessioni**, quindi selezionare il pulsante Crea per aprire la **creare connessioni** pagina.
3. Nel **creare connessioni** pagina, configurare i valori per la connessione.
     - Nozioni fondamentali:
        1. **Tipo di connessione**: selezionare Site-to-site (IPSec).
        2. **Gruppo di risorse**: (selezionare il gruppo di risorse di test)
     - Impostazioni:
        1. **Gateway di rete virtuale**: selezionare il gateway di rete virtuale creata in precedenza.
        2. **Gateway di rete locale**: selezionare il Gateway della rete locale creata in precedenza. 
        3. **Nome della connessione**: questo verrà automaticamente popolare con i valori da due gateway. 
        4. **Chiave condivisa**: il valore deve corrispondere al valore che si sta utilizzando per il dispositivo VPN locale. L'esempio usa "abc123", ma è possibile e consigliabile usare un elemento più complesso. È importante che il valore specificato qui sia lo stesso valore specificato durante la configurazione del dispositivo VPN.
    - I restanti valori di **Sottoscrizione**, **Gruppo di risorse** e **Località** sono fissi.
4. Fare clic su **OK** per creare la connessione. Verrà visualizzata la creazione di connessione flash sullo schermo.
5. È possibile visualizzare la connessione nel * * pagina connessioni di gateway di rete virtuale. Lo stato verrà inviata da **sconosciuto** alla **connessione**e quindi **Succeeded**.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sui pattern di Cloud di Azure, vedere [schemi progettuali Cloud](https://docs.microsoft.com/azure/architecture/patterns).
