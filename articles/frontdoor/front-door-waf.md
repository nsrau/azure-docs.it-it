---
title: Dimensionare e proteggere un'app Web con il servizio Frontdoor di Azure e WAF
description: Questa esercitazione illustra come usare Web application firewall di Azure con il servizio Frontdoor di Azure.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 2d531289a1d6e8c484b0334e570d943acdb82268
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276255"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Esercitazione: Dimensionare rapidamente e proteggere un'applicazione Web tramite il servizio Frontdoor di Azure e Web application firewall di Azure (WAF)

Molte applicazioni Web hanno riscontrato un rapido aumento del traffico nelle ultime settimane in relazione al COVID-19. Per queste applicazioni Web si osserva inoltre un picco di traffico dannoso con attacchi Denial of Service. Un modo efficace per gestire entrambe queste esigenze, aumentando le prestazioni per i picchi di traffico e attivando la protezione dagli attacchi, consiste nel configurare Frontdoor di Azure con Azure WAF come livello di accelerazione, memorizzazione nella cache e sicurezza davanti all'app Web. Questo articolo offre indicazioni su come configurare rapidamente il servizio Frontdoor di Azure con Azure WAF per tutte le app Web in esecuzione all'interno o all'esterno di Azure. 

In questa esercitazione verrà usata l'interfaccia della riga di comando di Azure per configurare WAF. In alternativa, è possibile usare il portale di Azure, Azure PowerShell, Azure Resource Manager o le API REST di Azure. 

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> - Creare un frontdoor.
> - Creare un criterio WAF di Azure.
> - Configurare i set di regole per un criterio WAF.
> - Associare un criterio WAF a una frontdoor.
> - Configurare un dominio personalizzato.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Le istruzioni fornite in questa esercitazione usano l'interfaccia della riga di comando di Azure. [Vedere questa guida](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) per iniziare a usare l'interfaccia della riga di comando di Azure.

  > [!TIP] 
  > Un modo semplice e rapido per iniziare a usare l'interfaccia della riga di comando di Azure è rappresentato da [Bash in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).

- Assicurarsi che l'estensione `front-door` sia stata aggiunta all'interfaccia della riga di comando di Azure:

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> Per altre informazioni sui comandi usati in questa esercitazione, vedere [Informazioni di riferimento dell'interfaccia della riga di comando di Azure per il servizio Frontdoor](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="create-an-azure-front-door-resource"></a>Creare una risorsa Frontdoor di Azure

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: nome di dominio completo (FQDN) dell'applicazione che si vuole proteggere. Ad esempio: `myapplication.contoso.com`.

`--accepted-protocols`: specifica i protocolli che devono essere supportati dal servizio Frontdoor di Azure per l'applicazione Web. Ad esempio: `--accepted-protocols Http Https`.

`--name`: Nome della risorsa Frontdoor di Azure.

`--resource-group`: gruppo di risorse in cui risiederà la risorsa Frontdoor di Azure. Per altre informazioni sui gruppi di risorse, vedere [Gestire i gruppi di risorse in Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal).

Nella risposta ottenuta quando si esegue questo comando, cercare la chiave `hostName`. Questo valore sarà necessario in un passaggio successivo. `hostName` è il nome DNS della risorsa Frontdoor di Azure creata.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Creare un profilo di Azure WAF da usare con le risorse di Frontdoor di Azure

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: nome del nuovo criterio di Azure WAF.

`--resource-group`: gruppo di risorse in cui risiederà la risorsa WAF. 

Il codice dell'interfaccia della riga di comando precedente crea un criterio WAF abilitato e in modalità di prevenzione. 

> [!NOTE] 
> Potrebbe essere necessario creare il criterio WAF in modalità di rilevamento e osservare come rileva e registra le richieste dannose (senza bloccarle) prima di decidere di usare la modalità di protezione.

Nella risposta ottenuta quando si esegue questo comando, cercare la chiave `ID`. Questo valore sarà necessario in un passaggio successivo. 

Il campo `ID` deve essere nel formato seguente:

/subscriptions/**ID sottoscrizione**/resourcegroups/**nome gruppo risorse**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**nome criterio WAF**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>Aggiungere set di regole gestiti al criterio WAF

È possibile aggiungere set di regole gestiti a un criterio WAF. Un set di regole gestito è un set di regole compilate e gestite da Microsoft che consentono di proteggersi da una classe di minacce. In questo esempio verranno aggiunti due set di regole:
- Il set di regole predefinito, che consente di proteggersi dalle minacce Web comuni. 
- Il set di regole di protezione dai bot, che consente di proteggersi dai bot dannosi.

Aggiungere il set di regole predefinito:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

Aggiungere il set di regole di protezione dai bot:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: nome assegnato alla risorsa Azure WAF.

`--resource-group`: gruppo di risorse in cui risiede la risorsa WAF.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>Associare il criterio WAF alla risorsa Frontdoor di Azure

In questo passaggio il criterio WAF creato verrà associato alla risorsa Frontdoor di Azure che si trova davanti all'applicazione Web:

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: nome assegnato alla risorsa Frontdoor di Azure.

`--resource-group`: gruppo di risorse in cui risiede la risorsa Frontdoor di Azure.

`--set`: qui verrà aggiornato l'attributo `WebApplicationFirewallPolicyLink` per l'oggetto `frontendEndpoint` associato alla risorsa Frontdoor di Azure con il nuovo criterio WAF. È necessario disporre dell'ID del criterio WAF dalla risposta ottenuta al momento della creazione del profilo WAF in un passaggio precedente di questa esercitazione.

 > [!NOTE] 
> L'esempio precedente è applicabile quando non si usa un dominio personalizzato. Se non si usano domini personalizzati per accedere alle applicazioni Web, è possibile ignorare la sezione successiva. In tal caso, si fornirà ai clienti il valore di `hostName` ottenuto al momento della creazione della risorsa Frontdoor di Azure. Useranno il valore di `hostName` per accedere all'applicazione Web.

## <a name="configure-the-custom-domain-for-your-web-application"></a>Configurare il dominio personalizzato per l'applicazione Web

Il nome di dominio personalizzato dell'applicazione Web è quello che i clienti usano per fare riferimento all'applicazione, ad esempio www.contoso.com. Inizialmente, questo nome di dominio personalizzato puntava al percorso in cui era in esecuzione prima dell'introduzione di Frontdoor di Azure. Dopo aver aggiunto il servizio Frontdoor di Azure e WAF davanti all'applicazione, la voce DNS corrispondente a tale dominio personalizzato deve puntare alla risorsa Frontdoor di Azure. Per apportare questa modifica, eseguire un nuovo mapping tra la voce nel server DNS e l'host `hostName` di Frontdoor di Azure annotato quando è stata creata la risorsa Frontdoor di Azure.

I passaggi specifici per aggiornare i record DNS dipendono dal provider di servizi DNS. Se si usa il servizio DNS di Azure per ospitare il nome DNS, vedere la documentazione per i [passaggi per aggiornare un record DNS](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) e puntare all'host `hostName` di Frontdoor di Azure. 

Se è necessario che i clienti accedano al sito Web usando il dominio radice, ad esempio, contoso.com, occorre tenere conto di un aspetto importante. In questo caso, è necessario usare DNS di Azure e il relativo [tipo di record alias](https://docs.microsoft.com/azure/dns/dns-alias) per ospitare il nome DNS. 

È anche necessario aggiornare la configurazione di Frontdoor di Azure per [aggiungere il dominio personalizzato](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) affinché riconosca questo mapping.

Se per raggiungere l'applicazione Web si usa un dominio personalizzato e si vuole abilitare il protocollo HTTPS, è necessario [configurare i certificati per il dominio personalizzato in Frontdoor di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="lock-down-your-web-application"></a>Bloccare l'applicazione Web

Si consiglia di verificare che solo i vertici di Frontdoor di Azure possano comunicare con l'applicazione Web. In questo modo, non sarà possibile aggirare la protezione di Frontdoor di Azure e accedere direttamente all'applicazione. Per eseguire questa operazione di blocco, vedere [Come si blocca l'accesso al back-end limitandolo solo a Frontdoor di Azure?](https://docs.microsoft.com/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse di questa esercitazione non sono più necessarie, usare il comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) per rimuovere il gruppo di risorse, Frontdoor e i criteri WAF:

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: nome del gruppo di risorse per tutte le risorse usate in questa esercitazione.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come risolvere i problemi relativi a Frontdoor, vedere le guide per la risoluzione dei problemi:

> [!div class="nextstepaction"]
> [Risoluzione dei più frequenti problemi di routing](front-door-troubleshoot-routing.md)

> [!div class="nextstepaction"]
> [Autorità di certificazione consentite](https://docs.microsoft.com/azure/frontdoor/front-door-troubleshoot-allowed-ca)
