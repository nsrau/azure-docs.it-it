---
title: Creare o aggiornare un gateway applicazione con un web application firewall | Microsoft Docs
description: Informazioni su come creare un gateway applicazione con un web application firewall tramite il portale
services: application-gateway
documentationcenter: na
author: davidmu1
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
ms.author: davidmu
ms.openlocfilehash: bfc06c1b44974fd17a3794654503d21d6407a917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-by-using-the-portal"></a>Creare un gateway applicazione con un web application firewall tramite il portale

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Interfaccia della riga di comando di Azure](application-gateway-web-application-firewall-cli.md)

Informazioni su come creare un gateway applicazione con web application firewall (WAF) abilitato.

Il WAF nel gateway applicazione di Azure protegge le applicazioni Web dai comuni attacchi basati sul Web, come ad esempio gli attacchi SQL injection, gli attacchi di scripting intersito e il controllo delle sessioni. Un WAF protegge da molte delle 10 vulnerabilità Web OWASP più diffuse.

## <a name="scenarios"></a>Scenari

Questo articolo contiene due scenari. Nel primo scenario si apprenderà come [creare un gateway applicazione con un WAF](#create-an-application-gateway-with-web-application-firewall). Nel secondo scenario si apprenderà come [aggiungere un WAF a un gateway applicazione esistente](#add-web-application-firewall-to-an-existing-application-gateway).

![Esempio dello scenario][scenario]

> [!NOTE]
> È possibile aggiungere indirizzi di pool back-end, probe di integrità personalizzati e regole aggiuntive al gateway applicazione. Queste applicazioni vengono configurate dopo la configurazione del gateway applicazione e non durante la distribuzione iniziale.

## <a name="before-you-begin"></a>Prima di iniziare

 Un gateway applicazione richiede una propria subnet. Quando si crea una rete virtuale, assicurarsi di lasciare uno spazio indirizzi sufficiente per più subnet. Dopo che un gateway applicazione è stato distribuito in una subnet, alla subnet possono essere aggiunti solo altri gateway applicazione.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Aggiungere un web application firewall a un gateway applicazione esistente

Questo esempio aggiorna un gateway applicazione esistente per supportare un WAF in modalità **Prevenzione**.

1. Nel riquadro **Preferiti** del portale di Azure selezionare **Tutte le risorse**. Selezionare il gateway applicazione esistente nel pannello **Tutte le risorse**. Se nella sottoscrizione selezionata sono già presenti delle risorse, è possibile immettere il nome nella casella **Filtra per nome** per accedere facilmente alla zona DNS.

   ![Selezione del gateway applicazione esistente][1]

2. Selezionare **Web application firewall** e aggiornare le impostazioni del gateway applicazione. Al termine dell'aggiornamento selezionare **Salva**. 

3. Usare le impostazioni seguenti per aggiornare un gateway applicazione esistente per supportare un WAF:

   | **Impostazione** | **Valore** | **Dettagli**
   |---|---|---|
   |**Eseguire l'aggiornamento al livello WAF**| Selezionato | Questa opzione imposta il livello del gateway applicazione al livello WAF.|
   |**Stato del firewall**| Enabled | Questa impostazione abilita il firewall su WAF.|
   |**Modalità firewall** | Prevenzione | Questa impostazione riguarda la modalità di gestione del traffico dannoso da parte del WAF. La modalità **Rilevamento** consente solo di registrare gli eventi. La modalità **Prevenzione** registra gli eventi e blocca il traffico dannoso.|
   |**Set di regole**|3.0|Questa impostazione determina il [set di regole di base](application-gateway-web-application-firewall-overview.md#core-rule-sets) usato per proteggere i membri del pool back-end.|
   |**Configurare regole disabilitate**|Variabile|Per impedire possibili falsi positivi, è possibile usare questa impostazione per disabilitare alcune [regole e gruppi di regole](application-gateway-crs-rulegroups-rules.md).|

    >[!NOTE]
    > Quando si aggiorna un gateway applicazione esistente per lo SKU WAF, le dimensioni dello SKU cambiano in **Medium**. Al termine della configurazione è possibile riconfigurare questa impostazione.

    ![Impostazioni di base][2-1]

    > [!NOTE]
    > Per visualizzare i log WAF, abilitare la diagnostica e selezionare **ApplicationGatewayFirewallLog**. Solo a scopo di test scegliere **1** come numero di istanze. Non è consigliabile un numero di istanze inferiore a **2** perché questo valore non è coperto dal contratto di servizio. I gateway di piccole dimensioni non sono disponibili quando si usa un WAF.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Creare un gateway applicazione con un web application firewall

Questo scenario illustrerà come:

* Creare un gateway applicazione WAF con dimensione Medium con due istanze.
* Creare una rete virtuale denominata AdatumAppGatewayVNET con un blocco CIDR riservato 10.0.0.0/16.
* Creare una subnet denominata Appgatewaysubnet che usa 10.0.0.0/28 come blocco CIDR.
* Configurare un certificato per l'offload SSL.

1. Accedere al [portale di Azure](https://portal.azure.com). Se non si dispone già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita della durata di un mese](https://azure.microsoft.com/free).

2. Nel riquadro **Preferiti** del portale selezionare **Nuovo**.

3. Nel pannello **Nuovo** selezionare **Rete**. Nel pannello **Rete** selezionare **Gateway applicazione**, come mostrato nell'immagine seguente:

    ![Creazione del gateway applicazione][1]

4. Nel pannello **Informazioni di base** visualizzato immettere i valori seguenti e quindi selezionare **OK**:

   | **Impostazione** | **Valore** | **Dettagli**
   |---|---|---|
   |**Nome**|AdatumAppGateway|Il nome del gateway applicazione.|
   |**Livello**|WAF|I valori disponibili sono Standard e WAF. Per altre informazioni su un WAF, vedere [Web Application Firewall](application-gateway-web-application-firewall-overview.md).|
   |**Dimensioni SKU**|Media|Le opzioni di livello standard sono **Small**, **Medium** e **Large**. Le opzioni del livello WAF sono solo **Medium** and **Large**.|
   |**Numero di istanze**|2|Il numero di istanze del gateway applicazione per la disponibilità elevata. Solo a scopo di test usare 1 come numero di istanze.|
   |**Sottoscrizione**|[Sottoscrizione]|Selezionare una sottoscrizione da usare per creare il gateway applicazione.|
   |**Gruppo di risorse**|**Crea nuovo:** AdatumAppGatewayRG|Creare un gruppo di risorse. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione selezionata. Per altre informazioni sui gruppi di risorse, vedere l'articolo [Panoramica di Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Posizione**|Stati Uniti occidentali||

   ![Configurazione delle impostazioni di base][2-2]

5. Nel pannello **Impostazioni** che viene visualizzato in **Rete virtuale** selezionare **Scegliere una rete virtuale**. Nel pannello **Scegli rete virtuale** selezionare **Crea nuovo**.

   ![Scelta della rete virtuale][2]

6. Nel pannello **Crea rete virtuale** immettere i valori seguenti e quindi selezionare **OK**. Il campo **Subnet** nel pannello **Impostazioni** viene popolato con la subnet selezionata.

   |**Impostazione** | **Valore** | **Dettagli** |
   |---|---|---|
   |**Nome**|AdatumAppGatewayVNET|Il nome del gateway applicazione.|
   |**Spazio degli indirizzi**|10.0.0.0/16| Questo valore è lo spazio degli indirizzi per la rete virtuale.|
   |**Nome della subnet**|AppGatewaySubnet|Il nome della subnet per il gateway applicazione.|
   |**Intervallo di indirizzi subnet**|10.0.0.0/28 | Questa subnet consente subnet aggiuntive nella rete virtuale per i membri del pool back-end.|

7. Nel pannello **Impostazioni** in **Configurazione IP front-end** selezionare **Pubblico** in **Tipo di indirizzo IP**.

8. Nel pannello **Impostazioni** in **Indirizzo IP pubblico** selezionare **Scegliere un indirizzo IP pubblico**. Nel pannello **Scegli indirizzo IP pubblico** selezionare **Crea nuovo**.

   ![Scelta dell'indirizzo IP pubblico][3]

9. Nel pannello **Crea indirizzo IP pubblico** accettare il valore predefinito e selezionare **OK**. Il campo **Indirizzo IP pubblico** viene popolato con l'indirizzo IP pubblico scelto.

10. Nel pannello **Impostazioni** in **Configurazione listener** selezionare **HTTP** in **Protocollo**. Per usare **HTTPS** è necessario un certificato. È necessaria la chiave privata del certificato. Fornire un'esportazione in formato pfx del certificato e immettere la password per il file.

11. Configurare le impostazioni specifiche del **WAF**.

   |**Impostazione** | **Valore** | **Dettagli** |
   |---|---|---|
   |**Stato del firewall**| Enabled| Questa impostazione attiva o disattiva il WAF.|
   |**Modalità firewall** | Prevenzione| Questa impostazione determina le azioni che il WAF adotta contro il traffico dannoso. La modalità **Rilevamento** consente solo di registrare il traffico. La modalità **Prevenzione** registra e arresta il traffico con una risposta di mancata autorizzazione 403.|


12. Esaminare la pagina di **riepilogo** e selezionare **OK**. Il gateway applicazione verrà inserito in coda e creato.

13. Dopo la creazione del gateway applicazione, passare al gateway nel portale per proseguire la configurazione dell'applicazione.

    ![Visualizzazione della risorsa del gateway applicazione][10]

Questi passaggi creano un gateway applicazione di base con le impostazioni predefinite per il listener, il pool back-end, le impostazioni HTTP back-end e le regole. Queste impostazioni possono essere modificate in base alla propria distribuzione dopo che è stato completato il provisioning.

> [!NOTE]
> I gateway applicazione creati con la configurazione di base del WAF sono configurati con CRS 3.0 per la protezione.

## <a name="next-steps"></a>Passaggi successivi

Per configurare un alias di dominio personalizzato per l'[indirizzo IP pubblico](../dns/dns-custom-domain.md#public-ip-address), è possibile usare DNS di Azure o un altro provider DNS.

Per configurare la registrazione diagnostica, per registrare gli eventi che vengono rilevati o bloccati con il web application firewall, visitare [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

Per creare probe di integrità personalizzati, vedere [Creare un probe personalizzato per un gateway applicazione con il portale](application-gateway-create-probe-portal.md).

Per configurare l'offload SSL ed evitare costose attività di sottoscrizione SSL nei server Web, vedere [Configurare un gateway applicazione per l'offload SSL con Azure Resource Manager](application-gateway-ssl-portal.md).

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png
