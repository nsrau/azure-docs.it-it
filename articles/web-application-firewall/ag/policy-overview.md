---
title: Panoramica dei criteri WAF (Web Application Firewall) di AzureAzure Web Application Firewall (WAF) policy overview
description: Questo articolo è una panoramica dei criteri globali, globali WAF (Web Application Firewall) e per URI.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: fff4fb5707c07098fd7fac261a36909224365cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060277"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Panoramica dei criteri WAF (Web Application Firewall) di AzureAzure Web Application Firewall (WAF) policy overview

I criteri firewall per applicazioni Web contengono tutte le impostazioni e le configurazioni WAF. Sono incluse le esclusioni, le regole personalizzate, le regole gestite e così via. Questi criteri vengono quindi associati a un gateway applicazione (globale), un listener (per sito) o una regola basata sul percorso (per URI) per tali criteri.

> [!NOTE]
> I criteri WAF (Web Application Firewall) e PER URI di Azure sono disponibili nell'anteprima pubblica.
> 
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Non esiste alcun limite al numero di criteri che è possibile creare. Quando si crea un criterio, è necessario associarlo a un gateway applicazione per avere effetto. Può essere associato a qualsiasi combinazione di gateway applicazione, listener e regole basate su percorso.

## <a name="global-waf-policy"></a>Politica WAF globale

Quando si associa un criterio WAF a livello globale, ogni sito sottostante il WAF del gateway applicazione è protetto con le stesse regole gestite, regole personalizzate, esclusioni e qualsiasi altra impostazione configurata.

Se si desidera applicare un singolo criterio a tutti i siti, è possibile associare il criterio al gateway applicazione. Per altre informazioni, vedere [Creare criteri di Web Application Firewall per il gateway applicazione](create-waf-policy-ag.md) per creare e applicare criteri WAF tramite il portale di Azure.For more information, see Create Web Application Firewall policies for Application Gateway to create and apply a WAF policy using the Azure portal. 

## <a name="per-site-waf-policy"></a>Criteri WAF per sito

Con i criteri WAF per sito, è possibile proteggere più siti con esigenze di sicurezza diverse dietro un singolo WAF utilizzando criteri per sito. Ad esempio, se sono presenti cinque siti dietro il WAF, è possibile avere cinque criteri WAF separati (uno per ogni listener) per personalizzare le esclusioni, le regole personalizzate, i set di regole gestite e tutte le altre impostazioni WAF per ogni sito.

Si supponga che al gateway applicazione sia applicato un criterio globale. Quindi si applicano criteri diversi a un listener nel gateway applicazione. I criteri dell'ascoltatore ora hanno effetto solo per tale listener. I criteri globali del gateway applicazione si applicano ancora a tutti gli altri listener e regole basate su percorso a cui non è assegnato un criterio specifico.

## <a name="per-uri-policy"></a>Criteri per URI

Per una maggiore personalizzazione fino al livello URI, è possibile associare un criterio WAF a una regola basata sul percorso. Se all'interno di un singolo sito sono presenti pagine che richiedono criteri diversi, è possibile apportare modifiche ai criteri WAF che interessano solo un determinato URI. Ciò potrebbe essere applicabile a una pagina di pagamento o di accesso o a qualsiasi altro URI che necessita di criteri WAF ancora più specifici rispetto agli altri siti sottostanti il WAF.

Come per i criteri WAF per sito, criteri più specifici sostituiscono quelli meno specifici. Ciò significa che un criterio per URI in una mappa del percorso URL ha la precedenza su qualsiasi criterio WAF globale o per sito.

## <a name="example"></a>Esempio

Si supponga di disporre di tre siti: contoso.com, fabrikam.com e adatum.com tutti dietro lo stesso gateway applicazione. Si desidera un WAF applicato a tutti e tre i siti, ma è necessario una maggiore sicurezza con adatum.com perché è lì che i clienti visitano, sfogliano e acquistano i prodotti.

È possibile applicare un criterio globale al WAF, con alcune impostazioni di base, esclusioni o regole personalizzate, se necessario, per impedire ad alcuni falsi positivi di bloccare il traffico. In questo caso, non è necessario eseguire regole SQL injection globali perché fabrikam.com e contoso.com sono pagine statiche senza back-end SQL. In questo modo è possibile disabilitare tali regole nei criteri globali.

Questa politica globale è adatta per contoso.com e fabrikam.com, ma è necessario prestare maggiore attenzione con adatum.com in cui vengono gestite le informazioni di accesso e i pagamenti. È possibile applicare criteri per sito al listener adatum e lasciare in esecuzione le regole SQL. Si supponga inoltre che sia presente un cookie che blocca il traffico, pertanto è possibile creare un'esclusione per tale cookie per arrestare il falso positivo. 

L'URI adatum.com/payments è dove è necessario fare attenzione. Applicare quindi un altro criterio a tale URI e lasciare abilitate tutte le regole e rimuovere tutte le esclusioni.

In questo esempio, si dispone di un criterio globale che si applica a due siti. Si dispone di un criterio per sito che si applica a un sito e quindi un criterio per URI che si applica a una regola basata sul percorso specifico. Vedere (inserire qui il collegamento quando esiste) procedura per creare criteri per sito e per URI per PowerShell corrispondente per questo esempio.

## <a name="existing-waf-configurations"></a>Configurazioni WAF esistenti

Tutte le nuove impostazioni WAF di Web Application Firewall (regole personalizzate, configurazioni del set di regole gestite, esclusioni e così via). Se si dispone di un WAF esistente, queste impostazioni potrebbero essere ancora presenti nella configurazione WAF. Per ulteriori informazioni sul passaggio al nuovo criterio WAF, [Migrate WAF Config to a WAF Policy](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy). 


## <a name="next-steps"></a>Passaggi successivi

Creare criteri per sito e per URI usando Azure PowerShell.Create per-site and per-URI policies using Azure PowerShell.
