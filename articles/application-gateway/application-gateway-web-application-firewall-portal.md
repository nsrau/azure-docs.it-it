---
title: Creare o aggiornare un gateway applicazione di Azure con il web application firewall | Microsoft Docs
description: Informazioni su come creare un gateway applicazione con il firewall applicazione Web tramite il portale
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 3ee146a0be3c3338cf0037e2ec92a3b8d0c05a4e
ms.contentlocale: it-it
ms.lasthandoff: 07/28/2017

---

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Creare un gateway applicazione con il firewall applicazione Web tramite il portale

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-web-application-firewall-powershell.md)

Informazioni su come creare un gateway applicazione con firewall applicazione Web abilitato.

Il firewall applicazione Web (WAF) nel gateway applicazione di Azure protegge le applicazioni Web dai comuni attacchi basati sul Web, come ad esempio gli attacchi SQL injection, gli attacchi di scripting intersito e il controllo delle sessioni. L'applicazione Web protegge da molte delle 10 vulnerabilità Web OWASP più diffuse.

## <a name="scenarios"></a>Scenari

Questo articolo presenta due scenari:

Nel primo scenario si apprenderà come [creare un gateway applicazione con il firewall applicazione Web](#create-an-application-gateway-with-web-application-firewall)

Nel secondo scenario si apprenderà come [aggiungere un firewall applicazione Web a un gateway applicazione esistente](#add-web-application-firewall-to-an-existing-application-gateway).

![Esempio dello scenario][scenario]

> [!NOTE]
> La configurazione aggiuntiva del gateway applicazione, che include i probe di integrità personalizzati, gli indirizzi del pool back-end e le regole aggiuntive, viene definita dopo la configurazione del gateway applicazione e non durante la distribuzione iniziale.

## <a name="before-you-begin"></a>Prima di iniziare

Il gateway applicazione di Azure richiede una propria subnet. Quando si crea una rete virtuale, assicurarsi di lasciare uno spazio indirizzi sufficiente per più subnet. Dopo che un gateway applicazione è stato distribuito in una subnet, alla subnet possono essere aggiunti solo altri gateway applicazione.

##<a name="add-web-application-firewall-to-an-existing-application-gateway"></a> Aggiungere un firewall applicazione Web a un gateway applicazione esistente

Questo esempio aggiorna un gateway applicazione esistente per supportare il firewall applicazione Web in modalità di prevenzione.

1. Nel riquadro **Preferiti** del portale di Azure fare clic su **Tutte le risorse**. Fare clic sul gateway applicazione esistente nel pannello **Tutte le risorse**. Se nella sottoscrizione selezionata sono già presenti delle risorse, è possibile immettere il nome nella casella **Filtra per nome** per accedere facilmente alla zona DNS.

   ![Creazione di un gateway applicazione][1]

1. Fare clic su **Firewall applicazione Web** e aggiornare le impostazioni del gateway applicazione. Al termine, fare clic su **Salva**

    Di seguito vengono elencate le impostazioni per aggiornare un gateway applicazione esistente per supportare il firewall applicazione Web:

   | **Impostazione** | **Valore** | **Dettagli**
   |---|---|---|
   |**Eseguire l'aggiornamento al livello WAF**| Selezionato | Il livello del gateway applicazione viene impostato al livello WAF.|
   |**Stato del firewall**| Enabled | Enabled | Questa impostazione abilita il firewall su WAF.|
   |**Modalità firewall** | Prevenzione | Questa impostazione riguarda la modalità di gestione del traffico dannoso da parte del firewall applicazione Web. La modalità **Rilevamento** registra solo gli eventi. La modalità **Prevenzione** registra gli eventi e blocca il traffico dannoso.|
   |**Set di regole**|3.0|Questa impostazione determina il [set di regole di base](application-gateway-web-application-firewall-overview.md#core-rule-sets) usato per proteggere i membri del pool back-end.|
   |**Configurare regole disabilitate**|variabile|Per impedire possibili falsi positivi, questa impostazione consente di disabilitare alcune [regole e gruppi di regole](application-gateway-crs-rulegroups-rules.md).|

    >[!NOTE]
    > Quando si aggiorna un gateway applicazione esistente per lo SKU WAF, le dimensioni dello SKU cambiano in **medie**. L'impostazione può essere riconfigurata al termine della configurazione.

    ![Pannello con impostazioni di base][2-1]

    > [!NOTE]
    > Per visualizzare i log del firewall applicazione Web, è necessario abilitare la diagnostica e selezionare ApplicationGatewayFirewallLog. A scopo di test si può scegliere 1 come numero di istanze. È importante sapere che un numero di istanze inferiore a due non è coperto dal contratto di servizio e non è quindi consigliabile. Con il firewall applicazione Web non è possibile usare gateway di piccole dimensioni.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>creare un gateway applicazione con il firewall applicazione Web

Questo scenario illustrerà come:

* Creare un gateway applicazione del firewall applicazione Web Medium con due istanze.
* Creare una rete virtuale denominata AdatumAppGatewayVNET con un blocco CIDR riservato 10.0.0.0/16.
* Creare una subnet denominata Appgatewaysubnet che usa 10.0.0.0/28 come blocco CIDR.
* Configurare un certificato per l'offload SSL.

1. Accedere al [Portale di Azure](https://portal.azure.com). Se non si dispone già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita della durata di un mese](https://azure.microsoft.com/free)
1. Nel riquadro Preferiti del portale fare clic su **Nuovo**
1. Nel pannello **Nuovo** fare clic su **Rete**. Nel pannello **Rete** fare clic su **Gateway applicazione**, come mostrato nell'immagine seguente:
1. Passare al portale di Azure e fare clic su **Nuovo** > **Rete** > **Gateway applicazione**

    ![Creazione di un gateway applicazione][1]

1. Nel pannello **Informazioni di base** visualizzato immettere i valori seguenti e quindi fare clic su **OK**:

   | **Impostazione** | **Valore** | **Dettagli**
   |---|---|---|
   |**Nome**|AdatumAppGateway|Il nome del gateway applicazione|
   |**Livello**|WAF|I valori disponibili sono Standard e WAF. Per altre informazioni su WAF, visitare [firewall applicazione Web](application-gateway-web-application-firewall-overview.md).|
   |**Dimensioni SKU**|Media|Opzioni disponibili quando si sceglie il livello Standard: Small, Medium e Large. Quando si sceglie il livello WAF, le opzioni sono solo Medium e Large.|
   |**Numero di istanze**|2|Numero di istanze del gateway applicazione per la disponibilità elevata. I numeri di istanze di 1 devono essere usati solo a scopo di test.|
   |**Sottoscrizione**|[Sottoscrizione]|Selezionare una sottoscrizione in cui creare il gateway applicazione.|
   |**Gruppo di risorse**|**Crea nuovo:** AdatumAppGatewayRG|Creare un gruppo di risorse. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione selezionata. Per altre informazioni sui gruppi di risorse, vedere l'articolo [Panoramica di Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Posizione**|Stati Uniti occidentali||

   ![Pannello con impostazioni di base][2-2]

1. Nel pannello **Impostazioni** che viene visualizzato in **Rete virtuale**, fare clic su **Scegliere una rete virtuale**. Si apre il pannello **Scegli rete virtuale**.  Fare clic su **Crea nuovo** per aprire il pannello **Crea rete virtuale**.

   ![scegliere una rete virtuale][2]

1. Nel pannello **Crea rete virtuale** immettere i valori seguenti e quindi fare clic su **OK**. I pannelli **Crea rete virtuale** e **Scegli rete virtuale** vengono chiusi. Il campo **Subnet** nel pannello **Impostazioni** verrà inoltre popolato con la subnet selezionata.

   |**Impostazione** | **Valore** | **Dettagli** |
   |---|---|---|
   |**Nome**|AdatumAppGatewayVNET|Nome del gateway applicazione|
   |**Spazio di indirizzi:**|10.0.0.0/16| È lo spazio degli indirizzi per la rete virtuale|
   |**Nome della subnet**|AppGatewaySubnet|Nome della subnet per il gateway applicazione|
   |**Intervallo di indirizzi subnet**|10.0.0.0/28| Questa subnet consente subnet aggiuntive nella rete virtuale per i membri del pool back-end|

1. Nel pannello **Impostazioni** in **Configurazione IP front-end** scegliere **Pubblico** in **Tipo di indirizzo IP**

1. Nel pannello **Impostazioni** in **Indirizzo IP pubblico** fare clic su **Scegliere un indirizzo IP pubblico**. Nel pannello **Scegliere un indirizzo IP pubblico** visualizzato fare clic su **Crea nuovo**.

   ![scegliere un indirizzo IP pubblico][3]

1. Nel pannello **Crea indirizzo IP pubblico** accettare il valore predefinito e fare clic su **OK**. Questo chiude i pannelli **Scegli indirizzo IP pubblico** e **Crea indirizzo IP pubblico** e inserirà l'indirizzo IP selezionato nel campo **Indirizzo IP pubblico**.

1. Nel pannello **Impostazioni** in **Configurazione listener** fare clic su **HTTP** in **Protocollo**. Per usare **HTTPS**è necessario un certificato. Poiché è necessaria la chiave privata del certificato, si devono specificare un file di esportazione con estensione pfx del certificato e la password per il file.

1. Configurare le impostazioni specifiche del **WAF** .

   |**Impostazione** | **Valore** | **Dettagli** |
   |---|---|---|
   |**Stato del firewall**| Enabled| Questa impostazione attiva o disattiva il WAF.|
   |**Modalità firewall** | Prevenzione| Questa impostazione determina le misure che WAF deve adottare contro il traffico dannoso. Se viene scelta l'opzione **Rilevamento** , il traffico sarà solo registrato.  Se viene scelta l'opzione **Prevenzione**, il traffico viene registrato e interrotto con una risposta 403 di accesso non autorizzato.|


1. Esaminare la pagina di riepilogo e fare clic su **OK**.  Il gateway applicazione verrà inserito in coda e creato.

1. Al termine della creazione del gateway applicazione, passare al gateway applicazione nel portale per proseguirne la configurazione.

    ![Visualizzazione della risorsa del gateway applicazione][10]

Questi passaggi creano un gateway applicazione di base con le impostazioni predefinite per il listener, il pool back-end, le impostazioni HTTP back-end e le regole. Queste impostazioni possono essere modificate in base alla propria distribuzione dopo che è stato completato il provisioning.

> [!NOTE]
> I gateway applicazione creati con la configurazione di base del web application firewall sono configurati con CRS 3.0 per la protezione.

## <a name="next-steps"></a>Passaggi successivi

Successivamente, è possibile imparare a configurare un alias di dominio personalizzato per l'[indirizzo IP pubblico](../dns/dns-custom-domain.md#public-ip-address) tramite DNS di Azure o un altro provider DNS.

Per informazioni su come configurare la registrazione diagnostica, per registrare gli eventi che vengono rilevati o bloccati con il web application firewall, visitare [Registrazione diagnostica per il gateway applicazione](application-gateway-diagnostics.md)

Per informazioni su come creare probe di integrità personalizzati, vedere [Creare un probe personalizzato per un gateway applicazione con il portale](application-gateway-create-probe-portal.md)

Per informazioni su come configurare l'offload SSL ed evitare costose attività di decrittografia SSL nei server Web, vedere [Configurare un gateway applicazione per l'offload SSL con Azure Resource Manager](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png

