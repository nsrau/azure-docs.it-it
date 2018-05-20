---
title: Gestire i profili di versione API nello Stack di Azure | Documenti Microsoft
description: Informazioni sui profili di versione API nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: e568ffd2c3adb97ed0b727b85e7888fb797db1f9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Gestire i profili di versione API in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

I profili di API specificano il provider di risorse di Azure e la versione dell'API per gli endpoint REST di Azure. È possibile creare client personalizzati in lingue diverse usando i profili di API. Ogni client utilizza un profilo di API per contattare il provider di risorse corretta e la versione API per lo Stack di Azure.

È possibile creare un'app per funzionare con i provider di risorse di Azure senza la necessità di ordinare gli esattamente quale versione di ogni API del provider di risorse è compatibile con lo Stack di Azure. Allinea semplicemente l'applicazione a un profilo; il SDK viene ripristinata la versione corretta di API.

In questo argomento contribuisce di:

 - Comprendere i profili di API per lo Stack di Azure.
 - Informazioni su come utilizzare i profili di API per sviluppare le soluzioni.
 - Vedere dove trovare informazioni aggiuntive specifiche di codice.

## <a name="summary-of-api-profiles"></a>Riepilogo dei profili di API

- API profili vengono utilizzati per rappresentare un set di provider di risorse di Azure e le relative versioni API.
- I profili di API sono stati creati per gli sviluppatori possono creare modelli più cloud di Azure. I profili sono progettati per soddisfare le esigenze di un'interfaccia compatibile e stabile.
- I profili vengono rilasciati quattro volte all'anno.
- Vengono utilizzate tre convenzioni di denominazione di profilo:
    - **Più recente**  
        Contiene le versioni API più recenti rilasciate in Azure globale.
    - **yyyy-mm-dd-hybrid**  
    Rilasciato a un ritmo dilazionato, questa versione è incentrata sulla coerenza e la stabilità attraverso più cloud. Questo profilo è destinato a ottimale compatibilità dello Stack di Azure.
    - **aaaa-mm-gg-profilo** si trova tra stabilità ottimali e le funzionalità più recenti.

### <a name="azure-api-profiles-and-azure-stack-compatibility"></a>I profili API di Azure e la compatibilità dello Stack di Azure

I profili di API di Azure più recenti non sono compatibili con lo Stack di Azure. È possibile utilizzare le seguenti convenzioni di denominazione per identificare i profili da usare per le soluzioni Azure Stack.

**Più recente**  
Questo profilo disponga delle versioni API aggiornate trovate in Azure globale, che non funziona nello Stack di Azure. **Più recente** con il maggior numero di modifiche di interruzione. Il profilo inserisce adattata stabilità e la compatibilità con altri cloud. Se si sta tentando di usare le versioni aggiornate di API, **più recente** è il profilo è necessario utilizzare.

**Aaaa-mm-gg-ibrida**  
Questo profilo viene rilasciato nel mese di marzo e settembre ogni anno. Questo profilo è ottimale stabilità e la compatibilità con le varie aree. **Aaaa-mm-gg-ibrida** è progettato per globale Azure e Azure Stack. Le versioni di API di Azure elencate in questo profilo saranno uguali a quelli elencati nello Stack di Azure. È possibile utilizzare questo profilo per sviluppare il codice per soluzioni di cloud ibrido.

**yyyy-mm-dd-profile**  
Questo profilo viene rilasciato per Azure globale nel mese di giugno e dicembre. Questo profilo non funzionerà sullo Stack di Azure; in genere, saranno presenti molte modifiche di rilievo. Anche se si trova tra stabilità ottimali e le funzionalità più recenti, la differenza tra **più recente** senza che sia questo profilo che **più recente** sarà sempre costituito le versioni API più recenti, indipendentemente da quando l'API è stato rilasciato. Ad esempio, se viene creata una nuova versione di API per l'API di calcolo in futuro, tale versione API sarà elencato nel **più recente**, ma non nel **aaaa-mm-gg-profilo** perché questo profilo esiste già.  **aaaa-mm-gg-profilo** vengono illustrate le versioni più aggiornate rilasciate prima giugno oppure prima di dicembre.

## <a name="azure-resource-manager-api-profiles"></a>Profili di API di gestione risorse di Azure

Stack di Azure non utilizza la versione più recente delle versioni API disponibile in Azure globale. Quando si crea una soluzione, è necessario trovare la versione dell'API per ogni provider di risorse di Azure che è compatibile con lo Stack di Azure.

Invece di ricercare tutti i provider di risorse e la versione specifica supportata dallo Stack di Azure, è possibile utilizzare un profilo di API. Il profilo specifica un set di provider di risorse e le versioni dell'API. il SDK o uno strumento compilata con il SDK, verrà ripristinata la versione dell'api destinazione specificata nel profilo. Con i profili di API, è possibile specificare una versione del profilo che si applica a un modello intero e, in fase di esecuzione, Gestione risorse di Azure consente di selezionare la versione corretta della risorsa.

I profili di API funzionano con gli strumenti che usa Azure Resource Manager, ad esempio di codice fornito nel SDK di Microsoft Visual Studio, Azure CLI, PowerShell. SDK e gli strumenti possono usare i profili per leggere la versione dei moduli e delle librerie da includere quando si compila un'applicazione.

**Scenario di sviluppo per l'utilizzo del profilo**  
Si supponga che si utilizza PowerShell per creare:

* Un account di archiviazione che utilizza il **appartenga** provider di risorse, che supporta l'api-version 2016-03-30.
* Una macchina virtuale che utilizza il **Microsoft. COMPUTE** provider di risorse, che supporta l'api-version 2015-12-01.

Invece di ricerca e l'installazione i moduli di PowerShell che supportano le api-Version che è necessario per l'archiviazione e calcolo, è possibile utilizzare un profilo. Usare il cmdlet * * Installa profilo * profilename * * *, PowerShell e carica la versione corretta dei moduli.

Analogamente, se si utilizza il SDK di Python per compilare un'applicazione basata su Python, è possibile utilizzare un profilo. il SDK di carica i moduli corretti per il provider di risorse specificati nello script.

Gli sviluppatori, è possibile concentrarsi sulla scrittura di soluzione. È possibile utilizzare un profilo, sapendo che il codice funzionerà su tutti i cloud che supportano il profilo.

## <a name="api-profile-code-samples"></a>Esempi di codice API di profilo

È possibile trovare esempi di codice che consentono di integrare la soluzione con la lingua preferita con lo Stack di Azure usando i profili. Attualmente, è possibile trovare istruzioni ed esempi per le lingue seguenti:

- **PowerShell**  
È possibile usare il **AzureRM.Bootstrapper** modulo disponibile tramite la raccolta di PowerShell per ottenere i cmdlet di PowerShell necessari per lavorare con i profili di versione API. Per informazioni, vedere [profili della versione di utilizzare l'API per PowerShell](azure-stack-version-profiles-powershell.md).
- **Interfaccia della riga di comando di Azure 2.0**  
È possibile aggiornare la configurazione dell'ambiente per utilizzare il profilo di versione API specifico dello Stack di Azure. Per informazioni, vedere [profili di utilizzare l'API della versione per Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
Nel SDK di GO, un profilo è una combinazione di diversi tipi di risorse con versioni diverse da servizi diversi. i profili sono disponibili con i profili / percorso con la versione nel **AAAA-MM-GG** formato. Per informazioni, vedere [profili della versione di utilizzare l'API di GO](azure-stack-version-profiles-go.md).
- **Ruby**  
il SDK di Ruby per Gestione risorse di Azure Stack fornisce strumenti che consentono di compilare e gestire l'infrastruttura. Provider di risorse SDK includono calcolo, le reti virtuali e archiviazione con il linguaggio Ruby. Per informazioni, vedere [profili di utilizzare l'API della versione con Ruby](azure-stack-version-profiles-ruby.md)

## <a name="next-steps"></a>Passaggi successivi

* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)
* [Esaminare i dettagli sul versioni API del provider di risorse supportate per i profili](azure-stack-profiles-azure-resource-manager-versions.md).
