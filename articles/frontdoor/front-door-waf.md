---
title: Ridimensionare rapidamente e proteggere un'applicazione Web tramite Frontdoor di Azure e Azure Web Application Firewall (WAF) | Microsoft Docs
description: Questo articolo aiuta a capire come usare Web Application Firewall con il servizio Frontdoor di Azure
services: frontdoor
documentationcenter: ''
author: tremansdoerfer
ms.service: frontdoor
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: rimansdo
ms.openlocfilehash: 6f91a98372aa85a52a6013a121235ca354004a84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743542"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Ridimensionare rapidamente e proteggere un'applicazione Web tramite Frontdoor di Azure e Azure Web Application Firewall (WAF)

Molte applicazioni Web hanno riscontrato un rapido aumento del traffico nelle ultime settimane in relazione a COVID-19. Per queste applicazioni Web viene anche notato un picco di traffico dannoso con attacchi Denial of Service. Un modo efficace per gestire entrambe queste esigenze, aumentando le prestazioni per i picchi di traffico e attivando la protezione dagli attacchi, consiste nel configurare Frontdoor di Azure con Azure WAF come livello di accelerazione, memorizzazione nella cache e sicurezza davanti all'applicazione Web. Questo articolo offre indicazioni su come realizzare rapidamente la configurazione di Frontdoor di Azure con Azure WAF per tutte le applicazioni Web in esecuzione all'interno o all'esterno di Azure. 

In questa esercitazione, per configurare WAF si userà l'interfaccia della riga di comando di Azure, ma tutti questi passaggi sono completamente supportati anche nel portale di Azure, in Azure PowerShell, in Azure ARM e nelle API REST di Azure. 

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

Le istruzioni in questo blog usano l'interfaccia della riga di comando di Azure. Vedere questa guida per [iniziare con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Suggerimento: un modo semplice e rapido per iniziare a usare l'interfaccia della riga di comando di Azure è rappresentato da [Bash in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Assicurarsi che l'estensione Frontdoor sia stata aggiunta all'interfaccia della riga di comando di Azure

```azurecli-interactive 
az extension add --name front-door
```

Nota: Per altri dettagli sui comandi elencati di seguito, fare riferimento alle [informazioni di riferimento dell'interfaccia della riga di comando di Azure per Frontdoor](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>Passaggio 1: Creare una risorsa di Frontdoor di Azure


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-address**: l'indirizzo back-end è il nome di dominio completo (FQDN) dell'applicazione che si vuole proteggere, ad esempio, myapplication.contoso.com

**--accepted-protocols**: i protocolli accettati specificano tutti i protocolli che devono essere supportati da Frontdoor di Azure per l'applicazione Web. Esempio: --accepted-protocols Http Https.

**--name**: specificare un nome per la risorsa di Frontdoor di Azure

**--resource-group**: il gruppo di risorse in cui si vuole inserire la risorsa di Frontdoor di Azure.  Per altre informazioni sui gruppi di risorse, vedere la gestione dei gruppi di risorse in Azure

Nella risposta ottenuta dall'esecuzione di questo comando, cercare la chiave "hostName" e prendere nota del suo valore per usarlo in un passaggio successivo. La chiave hostName è il nome DNS della risorsa di Frontdoor di Azure creata

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Passaggio 2: Creare un profilo di Azure WAF da usare con le risorse di Frontdoor di Azure

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--name Specificare un nome per il criterio di Azure WAF

--resource-group Il gruppo di risorse in cui si vuole inserire la risorsa di WAF. 

Il codice sopra riportato dell'interfaccia della riga di comando crea un criterio WAF abilitato e in modalità di prevenzione. 

Nota: è anche consigliabile creare WAF in modalità di rilevamento e osservare come rileva e registra le richieste dannose (senza bloccarle) prima di decidere di passare alla modalità di protezione.

Nella risposta ottenuta dall'esecuzione di questo comando, cercare la chiave "ID" e prendere nota del suo valore per usarlo in un passaggio successivo. Il campo ID deve avere il formato

/subscriptions/**ID sottoscrizione**/resourcegroups/**nome gruppo risorse**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**nome criterio WAF**

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>Passaggio 3: Aggiungere set di regole gestiti al criterio WAF

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

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>Passaggio 4: Associare il criterio di WAF alla risorsa di Frontdoor

In questo passaggio si assocerà il criterio di WAF creato con la risorsa del servizio Frontdoor di Azure che si trova davanti all'applicazione Web.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--name Il nome specificato per la risorsa di Frontdoor di Azure

--resource-group Il gruppo di risorse in cui si è inserita la risorsa di Frontdoor di Azure.

--set Qui viene aggiornato l'attributo WebApplicationFirewallPolicyLink per l'attributo frontendEndpoint associato alla risorsa di Frontdoor di Azure con il criterio di WAF appena creato. L'ID del criterio di WAF è reperibile nella risposta ottenuta nel passaggio 2 precedente

Nota: l'esempio sopra riportato riguarda il caso in cui non si usa un dominio personalizzato

Se non si usano domini personalizzati per accedere alle applicazioni Web, è possibile ignorare il passaggio 5. In tal caso, si indicherà agli utenti finali il nome host ottenuto nel passaggio 1 per passare all'applicazione Web

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>Passaggio 5: Configurare un dominio personalizzato per l'applicazione Web

Il nome di dominio personalizzato dell'applicazione Web (quello che i clienti usano per fare riferimento all'applicazione, ad esempio, www.contoso.com) puntava inizialmente al punto in cui si eseguiva l'applicazione prima dell'introduzione di Frontdoor di Azure. Dopo questa modifica dell'architettura, con l'aggiunta di Frontdoor di Azure e WAF davanti all'applicazione, la voce DNS corrispondente al dominio personalizzato dovrebbe ora puntare a questa risorsa di Frontdoor di Azure. Per eseguire questa operazione, modificare il mapping di questa voce nel server DNS usando il nome host di Frontdoor di Azure annotato nel passaggio 1.

I passaggi specifici per aggiornare i record DNS dipendono dal provider di servizi DNS, ma se si usa DNS di Azure per ospitare il nome DNS, è possibile fare riferimento alla documentazione per i [passaggi per aggiornare un record DNS](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) e per fare in modo che punti al nome host di Frontdoor di Azure. 

Un aspetto fondamentale da tenere presente è che, se è necessario che gli utenti accedano al sito Web usando il dominio radice, ad esempio contoso.com, si deve usare DNS di Azure e il [tipo di record ALIAS](https://docs.microsoft.com/azure/dns/dns-alias) corrispondente per ospitare il nome DNS. 

È anche necessario aggiornare la configurazione di Frontdoor di Azure [aggiungendovi il dominio personalizzato](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) in modo da renderlo noto a Frontdoor di Azure.

Se per raggiungere l'applicazione Web si usa un dominio personalizzato e si vuole abilitare il protocollo HTTPS, è necessario avere i [certificati per la configurazione del dominio personalizzato in Frontdoor di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="step-6-lock-down-your-web-application"></a>Passaggio 6: Bloccare l'applicazione Web

Una procedura consigliata facoltativa da seguire è garantire che solo il perimetro di Frontdoor di Azure possa comunicare con l'applicazione Web. Questa azione garantisce che nessuno possa superare le protezioni di Frontdoor di Azure e accedere alle applicazioni direttamente. È possibile realizzare questo blocco visitando la [sezione relativa alle domande frequenti di Frontdoor di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-faq) e consultando la domanda relativa al blocco dei back-end per consentire l'accesso solo da parte di Frontdoor di Azure.
