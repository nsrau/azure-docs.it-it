---
title: "Hosting di più siti con il gateway applicazione di Azure | Microsoft Docs"
description: "Questa pagina contiene istruzioni per configurare un gateway applicazione di Azure esistente per l'hosting di più applicazioni Web nello stesso gateway con il portale di Azure."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 95f892f6-fa27-47ee-b980-7abf4f2c66a9
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 28a7fcb3e08a9c4b6a27e9fbc8d3ebae309adc62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Configurare un gateway applicazione esistente per l'hosting di più applicazioni Web

> [!div class="op_single_selector"]
> * [portale di Azure](application-gateway-create-multisite-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-multisite-azureresourcemanager-powershell.md)
> 
> 

L'hosting di più siti consente di distribuire più applicazioni Web nello stesso gateway applicazione. La presenza dell'intestazione host nella richiesta HTTP in ingresso consente di determinare il listener che riceverà il traffico. Il listener indirizza quindi il traffico al pool back-end appropriato in base alla configurazione della definizione delle regole del gateway. Nelle applicazioni Web abilitate per SSL il gateway applicazione sceglie il listener corretto per il traffico Web in base all'estensione dell'indicazione nome server (SNI). L'hosting di più siti viene comunemente usato per bilanciare il carico delle richieste per diversi domini Web tra vari pool di server back-end. Analogamente, lo stesso gateway applicazione potrebbe ospitare anche più sottodomini dello stesso dominio radice.

## <a name="scenario"></a>Scenario

Nell'esempio seguente, il gateway applicazione gestisce il traffico per contoso.com e fabrikam.com con due pool di server back-end: il pool di server contoso e il pool di server fabrikam. Una configurazione simile potrebbe essere usata per ospitare sottodomini come app.contoso.com e blog.contoso.com.

![scenario multisito][multisite]

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario aggiunge il supporto multisito a un gateway applicazione esistente. Per completare questo scenario, è necessario che un gateway applicazione esistente sia disponibile per la configurazione. Visitare [Creare un gateway applicazione con il portale](application-gateway-create-gateway-portal.md) per imparare a creare un gateway applicazione di base nel portale.

Per aggiornare il gateway applicazione, seguire questa procedura:

1. Creare i pool di back-end da usare per ogni sito.
2. Creare un listener per ogni sito che viene supportato dal gateway applicazione.
3. Creare regole per eseguire il mapping di ogni listener con il back-end appropriato.

## <a name="requirements"></a>Requisiti

* **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici. È possibile usare anche FQDN.
* **Impostazioni del pool di server back-end:** ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
* **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener** : ha una porta front-end, un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL). Per i gateway applicazione abilitati per più siti vengono aggiunti anche indicatori SNI e nome host.
* **Regola**: associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. Le regole vengono elaborate nell'ordine in cui sono elencate e il traffico verrà indirizzato tramite la prima regola corrispondente indipendentemente dalla specificità. Se ad esempio si dispone di due regole, una che usa un listener di base e una che usa un listener multisito, entrambe sulla stessa porta, la regola con il listener multisito deve essere elencata prima della regola con il listener di base per funzionare come previsto. 
* **Certificati**: ogni listener richiede un certificato univoco. In questo esempio vengono creati due listener per più siti. È necessario creare due certificati con estensione pfx e le relative password.

## <a name="create-back-end-pools-for-each-site"></a>Creare i pool di back-end per ogni sito

È necessario un pool di back-end per ogni sito che supporta il gateway applicazione. In questo caso ne vengono creati due, uno per contoso11.com e uno per fabrikam11.com.

### <a name="step-1"></a>Passaggio 1

Passare a un gateway applicazione esistente nel portale di Azure (https://portal.azure.com). Selezionare **Pool back-end** e fare clic su **Aggiungi**.

![aggiunta di pool di back-end][7]

### <a name="step-2"></a>Passaggio 2

Immettere le informazioni per il pool di back-end **pool1**, aggiungendo gli indirizzi IP o i nomi di dominio completi per i server back-end, e fare clic su **OK**.

![impostazioni del pool di back-end pool1][8]

### <a name="step-3"></a>Passaggio 3

Nel pannello dei pool di back-end fare clic su **Aggiungi** per aggiungere un altro pool di back-end, **pool2**, aggiungendo gli indirizzi IP o i nomi di dominio completi per i server back-end, e fare clic su **OK**.

![impostazioni del pool di back-end pool2][9]

## <a name="create-listeners-for-each-back-end"></a>Creare listener per ogni back-end

Il gateway applicazione si basa su intestazioni host HTTP 1.1 per ospitare più siti Web nello stesso indirizzo IP pubblico e nella stessa porta. Il listener di base creato nel portale non contiene questa proprietà.

### <a name="step-1"></a>Passaggio 1

Fare clic su **Listener** sul gateway applicazione esistente e quindi su **Multisito** per aggiungere il primo listener.

![pannello della panoramica dei listener][1]

### <a name="step-2"></a>Passaggio 2

Immettere le informazioni relative al listener. In questo esempio la terminazione SSL è configurata, creare una nuova porta front-end. Caricare il certificato con estensione pfx da usare per la terminazione SSL. L'unica differenza tra questo pannello e quello del listener di base è data dal nome host.

![pannello delle proprietà del listener][2]

### <a name="step-3"></a>Passaggio 3

Fare clic su **Multisito** e creare un altro listener, come descritto nel passaggio precedente, per il secondo sito. Assicurarsi di usare un certificato diverso per il secondo listener. L'unica differenza tra questo pannello e quello del listener di base è data dal nome host. Immettere le informazioni relative al listener e fare clic su **OK**.

![pannello delle proprietà del listener][3]

> [!NOTE]
> La creazione di listener nel portale di Azure per il gateway applicazione è un'attività di lunga durata. Può essere necessario del tempo per creare i due listener in questo scenario. Al termine della procedura, i listener vengono visualizzati nel portale, come illustrato nell'immagine seguente:

![panoramica dei listener][4]

## <a name="create-rules-to-map-listeners-to-backend-pools"></a>Creare regole per associare i listener ai pool di back-end

### <a name="step-1"></a>Passaggio 1

Passare a un gateway applicazione esistente nel portale di Azure (https://portal.azure.com). Selezionare **Regole**, scegliere la regola predefinita esistente **rule1** e fare clic su **Modifica**.

### <a name="step-2"></a>Passaggio 2

Immettere i dati nel pannello delle regole come illustrato nella figura seguente. Scegliere il primo listener e il primo pool e fare clic su **Salva** al termine dell'operazione.

![modifica della regola esistente][6]

### <a name="step-3"></a>Passaggio 3

Fare clic su **Basic rule** (Regola di base) per creare la seconda regola. Compilare il modulo con il secondo listener e il secondo pool di back-end e fare clic su **OK** per salvare.

![pannello Aggiungi regola di base][10]

Con questo scenario viene completata la configurazione di un gateway applicazione esistente con supporto multisito tramite il portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come proteggere i siti Web con [Gateway applicazione: firewall applicazione Web](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png
