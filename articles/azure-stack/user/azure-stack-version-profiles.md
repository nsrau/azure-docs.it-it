---
title: Gestire i profili delle versioni API in Azure Stack | Microsoft Docs
description: Scopri i profili delle versioni API in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 4977e96e215aa58873146fe57203bbd895df551e
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769828"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Gestire i profili delle versioni API in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

I profili delle API specificano il provider di risorse di Azure e la versione dell'API per gli endpoint REST di Azure. È possibile creare client personalizzati in lingue diverse usando i profili delle API. Ogni client usa un profilo di API per contattare il provider di risorse corretto e la versione API per Azure Stack.

È possibile creare un'app di interagire con i provider di risorse di Azure senza dover sistemare esattamente quale versione di ogni API del provider di risorse è compatibile con Azure Stack. Allinea semplicemente l'applicazione in un profilo. il SDK viene ripristinata la versione API corretta.

Questo argomento consente di:

 - Comprendere i profili delle API di Azure Stack.
 - Informazioni su come è possibile usare i profili delle API per lo sviluppo di soluzioni.
 - Vedere dove trovare le linee guida specifiche di codice.

## <a name="summary-of-api-profiles"></a>Riepilogo dei profili di API

- I profili dell'API vengono utilizzati per rappresentare un set di provider di risorse di Azure e le versioni dell'API.
- I profili dell'API sono stati creati per poter creare modelli tra più cloud di Azure. I profili offrono un'interfaccia compatibile e stabile.
- I profili vengono rilasciati un anno a quattro volte.
- Vengono usate tre le convenzioni di denominazione di profilo:
    - **latest**  
        Contiene le versioni API più recenti rilasciate in Azure globale.
    - **yyyy-mm-dd-hybrid**  
    Rilasciato semestralmente, questa versione è incentrata sulla stabilità e la coerenza tra più cloud. Questo profilo è destinato a compatibilità ottimale di Azure Stack.
    - **aaaa-mm-gg-profilo** equilibrio ottimale stabilità e le funzionalità più recenti.

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Profili di API di Azure e la compatibilità di Azure Stack

I profili di API di Azure più recenti non sono compatibili con Azure Stack. È possibile usare le seguenti convenzioni di denominazione per identificare i profili da usare per le soluzioni di Azure Stack.

**Più recente**  
Questo profilo include le versioni API più aggiornate disponibili in Azure globale, che non funzionano in Azure Stack. **Più recente** con il maggior numero di modifiche di rilievo. Il profilo inserisce riservato stabilità e la compatibilità con altri cloud. Se si sta tentando di usare le versioni dell'API più aggiornate, **più recente** è il profilo è necessario utilizzare.

**Aaaa-mm-gg-hybrid**  
Questo profilo viene rilasciato nel mese di marzo e settembre ogni anno. Dispone di stabilità ottimale e garantire la compatibilità con vari cloud. **Aaaa-mm-gg-hybrid** è progettato per globale di Azure e Azure Stack. Le versioni di API di Azure elencate in questo profilo saranno uguali a quelli elencati in Azure Stack. È possibile usare questo profilo per sviluppare il codice per le soluzioni cloud ibride.

**yyyy-mm-dd-profile**  
Questo profilo viene rilasciato per Azure globale in giugno e dicembre. Non funziona con Azure Stack. in genere, vi saranno molte modifiche di rilievo. Anche se è un compromesso ottimale stabilità e le funzionalità più recenti, la differenza tra **più recente** e questo profilo è che **più recente** consiste sempre le versioni dell'API più recente, indipendentemente dal fatto quando si trovava l'API rilasciato. Ad esempio, se una nuova versione dell'API viene creata per l'API di calcolo in futuro, tale versione dell'API è elencato nel **più recente**, ma non nel **aaaa-mm-gg-profilo** profilare, perché questo profilo esiste già. **aaaa-mm-gg-profilo** illustra le versioni più aggiornate, rilasciate prima di giugno o prima di dicembre.

## <a name="azure-resource-manager-api-profiles"></a>Profili di API di gestione risorse di Azure

Azure Stack non utilizza la versione più recente delle versioni API disponibili in Azure globale. Quando si crea una soluzione, è necessario trovare la versione dell'API per ogni provider di risorse di Azure che è compatibile con Azure Stack.

Invece di ricerche ogni provider di risorse e la versione specifica supportate da Azure Stack, è possibile usare un profilo di API. Il profilo specifica un set di provider di risorse e le versioni dell'API. il SDK o uno strumento incorporato con il SDK, verrà ripristinato alla destinazione `api-version` specificato nel profilo. Con i profili delle API, è possibile specificare una versione del profilo che si applica a un intero modello e in fase di esecuzione, Azure Resource Manager consente di selezionare la versione corretta della risorsa.

I profili di API funzionano con gli strumenti che usano Azure Resource Manager, ad esempio PowerShell, CLI di Azure, codice fornito nel SDK e Microsoft Visual Studio. SDK e gli strumenti possono usare i profili per leggere la versione delle librerie da includere quando si compila un'applicazione e i moduli.

Ad esempio, se si usa PowerShell per creare un account di archiviazione usando il **Microsoft. Storage** provider di risorse, che supporta **api-version** 2016-03-30 e una macchina virtuale usando il  **Microsoft. COMPUTE** provider di risorse **api-version** 2015-12-01, è necessario cercare che supporta di modulo di PowerShell 2016-03-30 per l'archiviazione e il modulo supporta 2015-02-01 per il calcolo e quindi è necessario installarli. In alternativa, è possibile usare un profilo. Usare il cmdlet `Install-Profile <profilename>`, e PowerShell carica la versione corretta dei moduli.

Analogamente, quando si usano Python SDK per compilare un'applicazione basata su Python, è possibile specificare il profilo. il SDK carica i moduli appropriati per i provider di risorse specificato nello script.

Gli sviluppatori, è possibile concentrarsi sulla scrittura della soluzione. Invece di ricerca quali cloud, provider di risorse e le versioni API interagiscono, è possibile usare un profilo e sapere che il codice funziona in tutti i cloud che supportano tale profilo.

## <a name="api-profile-code-samples"></a>Esempi di codice API profilo

È possibile trovare esempi di codice che consentono di integrare la soluzione con il tuo linguaggio preferito con Azure Stack usando i profili. Attualmente, è possibile trovare informazioni aggiuntive ed esempi per le lingue seguenti:

- **.NET** è possibile usare il profilo di API .NET per ottenere la versione più recente e più stabile di ogni tipo di risorsa in un pacchetto di provider di risorse. Per altre informazioni, vedere [profili della versione di usare l'API con .NET in Azure Stack](azure-stack-version-profiles-net.md).
- **PowerShell**  
È possibile usare la **AzureRM.Bootstrapper** modulo disponibile tramite la raccolta di PowerShell per ottenere i cmdlet di PowerShell necessari per lavorare con i profili della versione API. Per informazioni, vedere [profili di usare l'API della versione per PowerShell](azure-stack-version-profiles-powershell.md).
- **Interfaccia della riga di comando di Azure**  
È possibile aggiornare la configurazione dell'ambiente per utilizzare il profilo di versione API specifico dello Stack di Azure. Per informazioni, vedere [i profili delle versioni API di utilizzo della riga di comando di Azure](azure-stack-version-profiles-azurecli2.md).
- **Go**  
In Go SDK, un profilo è una combinazione di diversi tipi di risorse con versioni diverse da servizi diversi. I profili sono disponibili nel profili/percorso, con la rispettiva versione nel **AAAA-MM-GG** formato. Per informazioni, vedere [i profili delle versioni API di utilizzo per Go](azure-stack-version-profiles-go.md).
- **Ruby**  
Ruby SDK per Azure Stack Resource Manager fornisce strumenti che consentono di compilare e gestire l'infrastruttura. I provider di risorse nel SDK includono calcolo, le reti virtuali e archiviazione con il linguaggio Ruby. Per informazioni, vedere [i profili delle versioni API di utilizzo con Ruby](azure-stack-version-profiles-ruby.md)
- **Python**  
Python SDK supporta i profili delle versioni API per diverse piattaforme cloud, ad esempio Azure Stack e Azure globale di destinazione. È possibile usare i profili delle API per creare soluzioni per un cloud ibrido. Per informazioni, vedere [i profili delle versioni API di utilizzo di Python](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>Passaggi successivi

* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)
* [Esaminare i dettagli sulle versioni di API del provider di risorse supportati per i profili](azure-stack-profiles-azure-resource-manager-versions.md).
