---
title: Ridimensionare rapidamente e proteggere un'applicazione Web tramite Frontdoor di Azure e Azure Web Application Firewall (WAF) | Microsoft Docs
description: Questa esercitazione illustra come usare web application firewall con il servizio Frontdoor di Azure
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
ms.openlocfilehash: 1958481193b66c8cec2cb6a1ac6648a6900d70ac
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531203"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Esercitazione: Ridimensionare rapidamente e proteggere un'applicazione Web tramite Frontdoor di Azure e Azure Web Application Firewall (WAF)

Molte applicazioni Web hanno riscontrato un rapido aumento del traffico nelle ultime settimane in relazione a COVID-19. Per queste applicazioni Web viene anche notato un picco di traffico dannoso con attacchi Denial of Service. Un modo efficace per gestire entrambe queste esigenze, aumentando le prestazioni per i picchi di traffico e attivando la protezione dagli attacchi, consiste nel configurare Frontdoor di Azure con Azure WAF come livello di accelerazione, memorizzazione nella cache e sicurezza davanti all'applicazione Web. Questo articolo offre indicazioni su come realizzare rapidamente la configurazione di Frontdoor di Azure con Azure WAF per tutte le applicazioni Web in esecuzione all'interno o all'esterno di Azure. 

In questa esercitazione, per configurare WAF si userà l'interfaccia della riga di comando di Azure, ma tutti questi passaggi sono completamente supportati anche nel portale di Azure, in Azure PowerShell, in Azure ARM e nelle API REST di Azure. 

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> - Creare un frontdoor.
> - Creare un criterio WAF di Azure.
> - Configurare set di regole per i criteri WAF.
> - Associare criteri WAF a Frontdoor
> - Configurare un dominio personalizzato

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Le istruzioni in questo blog usano l'interfaccia della riga di comando di Azure. Vedere questa guida per [iniziare con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Suggerimento: un modo semplice e rapido per iniziare a usare l'interfaccia della riga di comando di Azure è rappresentato da [Bash in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Assicurarsi che l'estensione Frontdoor sia stata aggiunta all'interfaccia della riga di comando di Azure

```azurecli-interactive 
az extension add --name front-door
```

Nota: Per altri dettagli sui comandi elencati di seguito, fare riferimento alle [informazioni di riferimento dell'interfaccia della riga di comando di Azure per Frontdoor](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="create-an-azure-front-door-afd-resource"></a>Creare una risorsa di Frontdoor di Azure

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-address**: l'indirizzo back-end è il nome di dominio completo (FQDN) dell'applicazione che si vuole proteggere, ad esempio, myapplication.contoso.com

**--accepted-protocols**: i protocolli accettati specificano tutti i protocolli che devono essere supportati da Frontdoor di Azure per l'applicazione Web. Esempio: --accepted-protocols Http Https.

**--name**: specificare un nome per la risorsa di Frontdoor di Azure

**--resource-group**: il gruppo di risorse in cui si vuole inserire la risorsa di Frontdoor di Azure.  Per altre informazioni sui gruppi di risorse, vedere la gestione dei gruppi di risorse in Azure

Nella risposta ottenuta dall'esecuzione di questo comando, cercare la chiave "hostName" e prendere nota del suo valore per usarlo in un passaggio successivo. La chiave hostName è il nome DNS della risorsa di Frontdoor di Azure creata

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Creare un profilo di Azure WAF da usare con le risorse di Frontdoor di Azure

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--name Specificare un nome per il criterio di Azure WAF

--resource-group Il gruppo di risorse in cui si vuole inserire la risorsa di WAF. 

Il codice sopra riportato dell'interfaccia della riga di comando crea un criterio WAF abilitato e in modalità di prevenzione. 

Nota: è anche consigliabile creare WAF in modalità di rilevamento e osservare come rileva e registra le richieste dannose (senza bloccarle) prima di decidere di passare alla modalità di protezione.

Nella risposta ottenuta dall'esecuzione di questo comando, cercare la chiave "ID" e prendere nota del suo valore per usarlo in un passaggio successivo. Il campo ID deve avere il formato

/subscriptions/**ID sottoscrizione**/resourcegroups/**nome gruppo risorse**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**nome criterio WAF**

## <a name="add-managed-rulesets-to-this-waf-policy"></a>Aggiungere set di regole gestiti al criterio WAF

In un criterio WAF è possibile aggiungere set di regole creati e gestiti da Microsoft e offrire una protezione predefinita contro intere classi di minacce. In questo esempio si aggiungeranno due set di regole di questo tipo: (1) un set di regole predefinito che protegga da minacce Web comuni e (2) un set di regole di protezione bot, che protegga da bot dannosi

(1) Aggiungere il set di regole predefinito

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) Aggiungere il set di regole di gestione bot

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--policy-name Il nome assegnato alla risorsa di Azure WAF

--resource-group Il gruppo di risorse in cui si è inserita la risorsa di WAF.

## <a name="associate-the-waf-policy-with-the-afd-resource"></a>Associare il criterio di WAF alla risorsa di Frontdoor

In questo passaggio si assocerà il criterio di WAF creato con la risorsa del servizio Frontdoor di Azure che si trova davanti all'applicazione Web.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--name Il nome specificato per la risorsa di Frontdoor di Azure

--resource-group Il gruppo di risorse in cui si è inserita la risorsa di Frontdoor di Azure.

--set Qui viene aggiornato l'attributo WebApplicationFirewallPolicyLink per l'attributo frontendEndpoint associato alla risorsa di Frontdoor di Azure con il criterio di WAF appena creato. L'ID del criterio di WAF è reperibile nella risposta ottenuta nel passaggio 2 precedente

Nota: l'esempio sopra riportato riguarda il caso in cui non si usa un dominio personalizzato

Se non si usano domini personalizzati per accedere alle applicazioni Web, è possibile ignorare il passaggio 5. In tal caso, si indicherà agli utenti finali il nome host ottenuto nel passaggio 1 per passare all'applicazione Web

## <a name="configure-custom-domain-for-your-web-application"></a>Configurare un dominio personalizzato per l'applicazione Web

Il nome di dominio personalizzato dell'applicazione Web (quello che i clienti usano per fare riferimento all'applicazione, ad esempio, www.contoso.com) puntava inizialmente al punto in cui si eseguiva l'applicazione prima dell'introduzione di Frontdoor di Azure. Dopo questa modifica dell'architettura, con l'aggiunta di Frontdoor di Azure e WAF davanti all'applicazione, la voce DNS corrispondente al dominio personalizzato dovrebbe ora puntare a questa risorsa di Frontdoor di Azure. Per eseguire questa operazione, modificare il mapping di questa voce nel server DNS usando il nome host di Frontdoor di Azure annotato nel passaggio 1.

I passaggi specifici per aggiornare i record DNS dipendono dal provider di servizi DNS, ma se si usa DNS di Azure per ospitare il nome DNS, è possibile fare riferimento alla documentazione per i [passaggi per aggiornare un record DNS](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) e per fare in modo che punti al nome host di Frontdoor di Azure. 

Un aspetto fondamentale da tenere presente è che, se è necessario consentire agli utenti di accedere al sito Web con il dominio radice, ad esempio contoso.com, si deve usare DNS di Azure e il [tipo di record ALIAS](https://docs.microsoft.com/azure/dns/dns-alias) corrispondente per ospitare il nome DNS. 

È anche necessario aggiornare la configurazione di Frontdoor di Azure [aggiungendovi il dominio personalizzato](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) in modo da renderlo noto a Frontdoor di Azure.

Se per raggiungere l'applicazione Web si usa un dominio personalizzato e si vuole abilitare il protocollo HTTPS, è necessario avere i [certificati per la configurazione del dominio personalizzato in Frontdoor di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="lock-down-your-web-application"></a>Bloccare l'applicazione Web

Una procedura consigliata facoltativa da seguire è garantire che solo il perimetro di Frontdoor di Azure possa comunicare con l'applicazione Web. Questa azione garantisce che nessuno possa superare le protezioni di Frontdoor di Azure e accedere alle applicazioni direttamente. È possibile realizzare questo blocco visitando la [sezione relativa alle domande frequenti di Frontdoor di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-faq) e consultando la domanda relativa al blocco dei back-end per consentire l'accesso solo da parte di Frontdoor di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse di questa esercitazione non sono più necessarie, usare il comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) per rimuovere il gruppo di risorse, Frontdoor e i criteri WAF.

```azurecli-interactive
  az group delete \
    --name <>
```
--name Il nome del gruppo di risorse per tutte le risorse distribuite in questa esercitazione.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come risolvere i problemi relativi a Frontdoor, continuare con le guide pratiche.

> [!div class="nextstepaction"]
> [Risoluzione dei più frequenti problemi di routing](front-door-troubleshoot-routing.md)
