---
title: Restrizioni di accesso al servizio app Azure
description: Informazioni su come proteggere l'app nel servizio app Azure impostando restrizioni di accesso.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e1549dda367105db34272eab8a90c1760dd5bb5c
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576445"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Configurare le restrizioni di accesso al servizio app Azure

Se si configurano restrizioni di accesso, è possibile definire un elenco Consenti/Nega ordinato per priorità che controlla l'accesso alla rete per l'app. L'elenco può includere indirizzi IP o subnet di rete virtuale di Azure. Quando sono presenti una o più voci, alla fine dell'elenco esiste una negazione implicita *All* .

La funzionalità di restrizione dell'accesso funziona con tutti i carichi di lavoro ospitati dal servizio app Azure. I carichi di lavoro possono includere app Web, app per le API, app Linux, app contenitore Linux e funzioni.

Quando viene effettuata una richiesta all'app, l'indirizzo FROM viene valutato in base alle regole degli indirizzi IP nell'elenco di restrizioni di accesso. Se l'indirizzo FROM si trova in una subnet configurata con gli endpoint di servizio in Microsoft. Web, la subnet di origine viene confrontata con le regole della rete virtuale nell'elenco di restrizioni di accesso. Se l'indirizzo non è consentito per l'accesso in base alle regole nell'elenco, il servizio risponde con un codice di stato [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) .

La funzionalità di restrizione dell'accesso è implementata nei ruoli front-end del servizio app, che sono upstream degli host di lavoro in cui viene eseguito il codice. Pertanto, le restrizioni di accesso sono in effetti elenchi di controllo di accesso di rete (ACL).

La possibilità di limitare l'accesso all'app Web da una rete virtuale di Azure è abilitata dagli [endpoint del servizio][serviceendpoints]. Con gli endpoint di servizio, è possibile limitare l'accesso a un servizio multi-tenant dalle subnet selezionate. Non funziona per limitare il traffico alle app ospitate in un ambiente del servizio app. Se ci si trova in una ambiente del servizio app, è possibile controllare l'accesso all'app applicando le regole degli indirizzi IP.

> [!NOTE]
> Gli endpoint di servizio devono essere abilitati sia sul lato rete sia per il servizio di Azure con cui sono abilitati. Per un elenco dei servizi di Azure che supportano gli endpoint di servizio, vedere [endpoint del servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).
>

![Diagramma del flusso di restrizioni di accesso.](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>Aggiungere o modificare le regole di restrizione dell'accesso nel portale

Per aggiungere una regola di restrizione di accesso all'app, seguire questa procedura:

1. Accedere al portale di Azure.

1. Nel riquadro sinistro selezionare **rete**.

1. Nel riquadro **rete** , in **restrizioni di accesso** , selezionare **configura restrizioni di accesso**.

   ![Screenshot del riquadro Opzioni di rete del servizio app nel portale di Azure.](media/app-service-ip-restrictions/access-restrictions.png)  

1. Nella pagina **restrizioni di accesso** esaminare l'elenco di regole di restrizione dell'accesso definite per l'app.

   ![Screenshot della pagina restrizioni di accesso nella portale di Azure, che mostra l'elenco delle regole di restrizione dell'accesso definite per l'app selezionata.](media/app-service-ip-restrictions/access-restrictions-browse.png)

   L'elenco Visualizza tutte le restrizioni correnti applicate all'app. Se si dispone di una restrizione della rete virtuale nell'app, nella tabella viene indicato se gli endpoint di servizio sono abilitati per Microsoft. Web. Se non è stata definita alcuna restrizione nell'app, l'app è accessibile da qualsiasi posizione.  

### <a name="add-an-access-restriction-rule"></a>Aggiungere una regola di restrizione dell'accesso

Per aggiungere una regola di restrizione di accesso all'app, nel riquadro **restrizioni di accesso** selezionare **Aggiungi regola**. Dopo aver aggiunto una regola, questa diventa effettiva immediatamente. 

Le regole vengono applicate in ordine di priorità, a partire dal numero più basso nella colonna **priorità** . Un'istruzione *Deny* implicita All è attiva dopo aver aggiunto anche una singola regola.

Quando si crea una regola nel riquadro **Aggiungi restrizione IP** , procedere come segue:

1. In **azione** selezionare **Consenti** o **Nega**.  

   ![Screenshot del riquadro "Aggiungi restrizione IP".](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. Facoltativamente, immettere un nome e una descrizione per la regola.  
1. Nell'elenco a discesa **tipo** selezionare il tipo di regola.  
1. Nella casella **priorità** immettere un valore di priorità.  
1. Negli elenchi a discesa **sottoscrizione** , **rete virtuale** e **subnet** selezionare gli elementi a cui si vuole limitare l'accesso.  

### <a name="set-an-ip-address-based-rule"></a>Impostare una regola basata sull'indirizzo IP

Attenersi alla procedura descritta nella sezione precedente, ma con la variante seguente:
* Per il passaggio 3, nell'elenco a discesa **tipo** selezionare **IPv4** o **IPv6**. 

Specificare l'indirizzo IP nella notazione CIDR (Classy Inter-Domain routing) per gli indirizzi IPv4 e IPv6. Per specificare un indirizzo, è possibile usare qualcosa come *1.2.3.4/32* , in cui i primi quattro ottetti rappresentano l'indirizzo IP e */32* è la maschera. La notazione CIDR IPv4 per tutti gli indirizzi è 0.0.0.0/0. Per ulteriori informazioni sulla notazione CIDR, vedere [Routing Inter-Domain di classi](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="use-service-endpoints"></a>Usare endpoint di servizio

Usando gli endpoint di servizio, è possibile limitare l'accesso alle subnet della rete virtuale di Azure selezionate. Per limitare l'accesso a una subnet specifica, creare una regola di restrizione con un tipo di **rete virtuale** . È quindi possibile selezionare la sottoscrizione, la rete virtuale e la subnet a cui si vuole concedere o negare l'accesso. 

Se gli endpoint di servizio non sono già abilitati con Microsoft. Web per la subnet selezionata, verranno abilitati automaticamente a meno che non si selezioni la casella di controllo **Ignora endpoint dei servizi Microsoft. Web mancanti** . Lo scenario in cui potrebbe essere necessario abilitare gli endpoint di servizio nell'app, ma non la subnet, dipende principalmente dal fatto che siano disponibili le autorizzazioni per abilitarle nella subnet. 

Se è necessario un altro utente per abilitare gli endpoint di servizio nella subnet, selezionare la casella di controllo **Ignora endpoint dei servizi Microsoft. Web mancanti** . L'app verrà configurata per gli endpoint di servizio in previsione di essere abilitata in un secondo momento nella subnet. 

![Screenshot del riquadro "Aggiungi restrizione IP" con il tipo di rete virtuale selezionato.](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Non è possibile usare gli endpoint di servizio per limitare l'accesso alle app eseguite in un ambiente del servizio app. Quando l'app si trova in una ambiente del servizio app, è possibile controllarne l'accesso applicando regole di accesso IP. 

Con gli endpoint di servizio è possibile configurare l'app con gateway applicazione o altri dispositivi web application firewall (WAF). È anche possibile configurare applicazioni multilivello con back-end protetti. Per altre informazioni, vedere [funzionalità di rete e integrazione del servizio app](networking-features.md) e del [gateway applicazione con gli endpoint di servizio](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> - Gli endpoint di servizio non sono attualmente supportati per le app Web che usano IP virtuale (VIP) IP Secure Sockets Layer (SSL).
> - È previsto un limite di 512 righe di restrizioni IP o di endpoint del servizio. Se sono necessarie più di 512 righe di restrizioni, è consigliabile prendere in considerazione l'installazione di un prodotto di sicurezza autonomo, ad esempio front door di Azure, app Azure gateway o un WAF.
>

## <a name="manage-access-restriction-rules"></a>Gestire le regole di restrizione dell'accesso

È possibile modificare o eliminare una regola di restrizione dell'accesso esistente.

### <a name="edit-a-rule"></a>Modificare una regola

1. Per iniziare a modificare una regola di restrizione dell'accesso esistente, nella pagina **restrizioni di accesso** fare doppio clic sulla regola che si desidera modificare.

1. Nel riquadro **Modifica restrizione IP** apportare le modifiche e quindi selezionare **Aggiorna regola**. Le modifiche hanno effetto immediato, incluse le modifiche nell'ordine di priorità.

   ![Screenshot del riquadro "Modifica restrizione IP" nella portale di Azure, che mostra i campi per una regola di restrizione dell'accesso esistente.](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

   > [!NOTE]
   > Quando si modifica una regola, non è possibile passare tra una regola di indirizzo IP e una regola della rete virtuale. 

   ![Screenshot del riquadro "Modifica restrizione IP" in portale di Azure, che mostra le impostazioni per una regola della rete virtuale.](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

### <a name="delete-a-rule"></a>Eliminare una regola

Per eliminare una regola, nella pagina **restrizioni di accesso** selezionare i puntini di sospensione ( **...** ) accanto alla regola che si desidera eliminare, quindi selezionare **Rimuovi**.

![Screenshot della pagina "restrizioni di accesso", che mostra i puntini di sospensione "Rimuovi" accanto alla regola di restrizione dell'accesso da eliminare.](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>Blocca un singolo indirizzo IP

Quando si aggiunge la prima regola di restrizione IP, il servizio aggiunge una regola esplicita *Deny All* con una priorità pari a 2147483647. In pratica, la regola esplicita *Deny All* è la regola finale da eseguire e blocca l'accesso a qualsiasi indirizzo IP non consentito in modo esplicito da una regola di *autorizzazione* .

Per uno scenario in cui si desidera bloccare in modo esplicito un singolo indirizzo IP o un blocco di indirizzi IP, ma consentire l'accesso a tutti gli altri elementi, aggiungere una regola *Consenti tutto* esplicitamente.

![Screenshot della pagina "restrizioni di accesso" nella portale di Azure, che mostra un singolo indirizzo IP bloccato.](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>Limitazione dell'accesso a un sito SCM 

Oltre a essere in grado di controllare l'accesso all'app, è possibile limitare l'accesso al sito SCM usato dall'app. Il sito SCM è sia l'endpoint di distribuzione Web che la console Kudu. È possibile assegnare restrizioni di accesso al sito SCM dall'app separatamente oppure usare lo stesso set di restrizioni sia per l'app che per il sito SCM. Quando si selezionano le **stesse restrizioni \<app name>** della casella di controllo, tutti gli elementi sono vuoti. Se si deseleziona la casella di controllo, le impostazioni del sito SCM verranno riapplicate. 

![Screenshot della pagina "restrizioni di accesso" nella portale di Azure, che indica che non è stata impostata alcuna restrizione di accesso per il sito SCM o l'app.](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>Gestire le regole di restrizione dell'accesso a livello

È possibile aggiungere restrizioni di accesso a livello eseguendo una delle operazioni seguenti: 

* Usare [l'interfaccia della riga di comando di Azure](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true). Ad esempio:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
  --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Usare [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true). Ad esempio:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```

È anche possibile impostare manualmente i valori eseguendo una delle operazioni seguenti:

* Usare un'operazione di inserimento dell' [API REST di Azure](/rest/api/azure/) nella configurazione dell'app in Azure Resource Manager. Il percorso di queste informazioni in Azure Resource Manager è:

  management.azure.com/subscriptions/ **subscription ID** /resourceGroups/ **resource groups** /providers/Microsoft.Web/sites/ **web app name** /config/web?api-version=2018-02-01

* Usare un modello ARM. Come esempio, è possibile usare resources.azure.com e modificare il blocco ipSecurityRestrictions per aggiungere il JSON necessario.

  La sintassi JSON per l'esempio precedente è:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```

## <a name="set-up-azure-functions-access-restrictions"></a>Configurare le restrizioni di accesso alle funzioni di Azure

Le restrizioni di accesso sono disponibili anche per le app per le funzioni con le stesse funzionalità dei piani di servizio app. Quando si abilitano le restrizioni di accesso, si disabilita anche l'editor di codice portale di Azure per gli indirizzi IP non consentiti.

## <a name="next-steps"></a>Passaggi successivi
[Limitazioni di accesso per funzioni di Azure](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Integrazione del gateway applicazione con gli endpoint di servizio](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
