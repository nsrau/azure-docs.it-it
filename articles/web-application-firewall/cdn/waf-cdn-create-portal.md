---
title: 'Esercitazione: Creare un criterio WAF per la rete CDN di Azure - Portale di Azure'
description: Questa esercitazione illustra come creare un criterio di web application firewall (WAF) nella rete CDN di Azure tramite il portale di Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 7a9e0cc3977892fd899b4a25e17ad72f13481506
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608814"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Esercitazione: Creare un criterio WAF nella rete CDN di Azure usando il portale di Azure

Questa esercitazione illustra come creare un criterio di web application firewall (WAF) di Azure di base e applicarlo a un host front-end nella rete di distribuzione dei contenuti (rete CDN) di Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un criterio WAF
> * Associarlo a un endpoint rete CDN. È possibile associare un criterio di WAF solo agli endpoint ospitati nello SKU della **rete CDN di Azure Standard di Microsoft**.
> * Configurare le regole per web application firewall

## <a name="prerequisites"></a>Prerequisiti

Creare un profilo e un endpoint della rete CDN di Azure seguendo le istruzioni riportate in [Avvio rapido: Creare un profilo e un endpoint della rete CDN di Azure](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Creare un criterio di web application firewall

In primo luogo, creare un criterio WAF di base con il set di regole predefinite gestito usando il portale.

1. Nella parte superiore sinistra della schermata selezionare **Crea una risorsa**, cercare **WAF**, selezionare **Web application firewall (anteprima)** e infine selezionare **Crea**.
2. Nella scheda **Generale** della pagina **Crea un criterio WAF** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le opzioni rimanenti e quindi selezionare **Rivedi e crea**:

    | Impostazione                 | valore                                              |
    | ---                     | ---                                                |
    | Criteri per            |Selezionare Rete CDN di Azure (anteprima).|
    | Subscription            |Selezionare il nome della sottoscrizione di Frontdoor.|
    | Resource group          |Selezionare il nome del gruppo di risorse di Frontdoor.|
    | Nome criteri             |Immettere un nome univoco per il criterio WAF.|

   ![Creare un criterio WAF](../media/waf-cdn-create-portal/basic.png)

3. Nella scheda **Associazione** della pagina **Crea un criterio WAF** selezionare **Aggiungi endpoint rete CDN**, immettere le impostazioni seguenti e quindi selezionare **Aggiungi**:

    | Impostazione                 | valore                                              |
    | ---                     | ---                                                |
    | Profilo CDN              | Selezionare il nome del profilo CDN.|
    | Endpoint           | Selezionare il nome dell'endpoint, quindi selezionare **Aggiungi**.|
    
    > [!NOTE]
    > Se l'endpoint è associato a un criterio WAF, viene visualizzato come inattivo. Occorre prima rimuovere l'endpoint dal criterio associato e quindi riassociare l'endpoint a un nuovo criterio WAF.
1. Selezionare **Rivedi e crea** e quindi **Crea**.

## <a name="configure-web-application-firewall-policy-optional"></a>Configurare il criterio di web application firewall (facoltativo)

### <a name="change-mode"></a>Cambiare modalità

Quando si crea un criterio WAF, per impostazione predefinita questo è in modalità *Rilevamento*. In modalità *Rilevamento* WAF non blocca alcuna richiesta. Al contrario, le richieste corrispondenti alle regole WAF vengono registrate nei log di WAF.

Per vedere WAF in azione, è possibile cambiare le impostazioni della modalità da *Rilevamento* a *Prevenzione*. In modalità *Prevenzione* le richieste che soddisfano le regole definite nel set di regole predefinite vengono bloccate e registrate nei log WAF.

 ![Cambiare la modalità del criterio WAF](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>Regole personalizzate

Per creare una regola personalizzata, selezionare **Aggiungi regola personalizzata** nella sezione **Regole personalizzate**. Verrà aperta la pagina di configurazione delle regole personalizzate. Esistono due tipi di regole personalizzate: **regola di corrispondenza** e **limite di frequenza**.

La schermata seguente mostra una regola di corrispondenza personalizzata per bloccare una richiesta se la stringa di query contiene il valore **blockme**.

![Cambiare la modalità del criterio WAF](../media/waf-cdn-create-portal/custommatch.png)

Le regole relative ai limiti di frequenza richiedono due campi aggiuntivi: **Durata limite di frequenza** e **Soglia limite di frequenza (richieste)** come illustrato nell'esempio seguente:

![Cambiare la modalità del criterio WAF](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>Set di regole predefinite

Il set di regole predefinite gestito da Azure è abilitato per impostazione predefinita. Per disabilitare una singola regola in un gruppo di regole, espandere le regole all'interno di tale gruppo, selezionare la casella di controllo davanti al numero della regola e quindi selezionare **Disabilita** nella scheda visualizzata sopra. Per cambiare i tipi di azioni per le singole regole all'interno del set di regole, selezionare la casella di controllo davanti al numero della regola, quindi selezionare la scheda **Modifica azione**.

 ![Modificare il set di regole WAF](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su web application firewall di Azure](../overview.md)
