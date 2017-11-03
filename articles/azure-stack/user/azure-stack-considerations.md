---
title: Differenze principali tra Azure e Azure Stack quando si utilizzano i servizi e compilare App | Documenti Microsoft
description: "Cosa è necessario sapere quando si utilizzano i servizi o compilare App per lo Stack di Azure."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 1e170f320292e3dbe920907a4ed81ab0d1eb388b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Considerazioni sulla chiave: utilizzo di servizi o creazione di applicazioni per Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Quando si utilizzano i servizi o compilare App per lo Stack di Azure, è necessario comprendere che non esistono differenze tra lo Stack di Azure e Azure. In questo articolo viene fornita una panoramica delle considerazioni principali quando la destinazione dello Stack di Azure come ambiente di sviluppo cloud ibrido.

## <a name="overview"></a>Panoramica

Azure Stack è una piattaforma cloud ibrida che permette di usare i servizi di Azure dal data center aziendale o del provider di servizi. Gli sviluppatori, è possibile compilare App eseguite sullo Stack di Azure. È quindi possibile distribuire queste App allo Stack di Azure, in Azure, oppure è possibile compilare effettivamente applicazioni ibride che consentono di sfruttare la connettività tra un cloud di Stack di Azure e Azure.

L'operatore di Stack di Azure consente di sapere quali servizi sono disponibili per l'utilizzo e come ottenere supporto. Offrono questi servizi tramite i piani personalizzati e offerte.

I contenuti tecnici di Azure si presuppone che le applicazioni vengano sviluppate per un servizio di Azure anziché Stack di Azure. Quando si compila e distribuisce l'App allo Stack di Azure, è necessario comprendere alcune differenze fondamentali, ad esempio:

* Stack di Azure offre un sottoinsieme di servizi e le funzionalità disponibili in Azure.
* Il società o provider di servizi è possibile scegliere quali servizi che desiderano offrire. Ciò include applicazioni o servizi personalizzati. Offrono propria documentazione personalizzata.
* È necessario utilizzare il corretto endpoint di Azure Stack specifici (ad esempio, gli URL per l'indirizzo di portale e l'endpoint di Azure Resource Manager).
* È necessario utilizzare le versioni di PowerShell e API supportate dallo Stack di Azure. Questa operazione assicura che le app funzionerà in entrambi Stack di Azure e Azure.

## <a name="cheat-sheet-high-level-differences"></a>Schede di riferimento rapido: differenze generali

Nella tabella seguente descrive le differenze generali tra Stack di Azure e Azure. Tenere presente quanto segue quando si sviluppa per lo Stack di Azure o utilizzare i servizi di Azure Stack.

| Area | Azure (globale) | Azure Stack |
| -------- | ------------- | ----------|
| Che opera la? | Microsoft | L'organizzazione o provider di servizi.|
| Chi è necessario contattare per il supporto? | Microsoft | Per un sistema integrato, contattare l'operatore di Stack di Azure (presso il provider di servizio o organizzazione) per il supporto.<br><br>Per il supporto di Azure Stack Development Kit, visitare il [forum Microsoft](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Poiché il kit di sviluppo è un ambiente di valutazione, non è previsto alcun supporto ufficiale offerto tramite Microsoft cliente il supporto tecnico clienti Microsoft.
| Servizi disponibili | Visualizzare l'elenco di [prodotti Azure](https://azure.microsoft.com/services/?b=17.04b). Servizi disponibili variano a seconda area di Azure. | Stack di Azure supporta un sottoinsieme di servizi di Azure. Servizi effettivi variano in base a ciò che sceglie il provider di servizio o dell'organizzazione offrire.
| Azure Resource Manager endpoint * | https://management.azure.com | Per un sistema integrato dello Stack di Azure, utilizzare l'endpoint che l'operatore di Stack di Azure fornito.<br><br>Per il kit di sviluppo, utilizzare: https://management.local.azurestack.external
| Portale URL * | [https://portal.azure.com](https://portal.azure.com) | Per un sistema integrato dello Stack di Azure, andare all'URL fornito l'operatore di Stack di Azure.<br><br>Per il kit di sviluppo, utilizzare: https://portal.local.azurestack.external
| Region | È possibile selezionare l'area in cui si desidera distribuire. | Per un sistema integrato dello Stack di Azure, utilizzare l'area che è disponibile nel sistema.<br><br>Per il kit di sviluppo, area sarà sempre **locale**.
| Gruppi di risorse | Un gruppo di risorse può estendersi su aree. | Per i sistemi integrati e il kit di sviluppo, è disponibile una sola area.
|Spazi dei nomi supportati, i tipi di risorse e le versioni dell'API | La versione più recente (o versioni precedenti che non sono ancora deprecate). | Stack di Azure supporta versioni specifiche. Vedere la sezione "Requisiti di versione" di questo articolo.
| | |

* Se si è un operatore di Stack di Azure, vedere [mediante il portale dell'amministratore](../azure-stack-manage-portals.md) e [nozioni fondamentali di amministrazione](../azure-stack-manage-basics.md) per ulteriori informazioni.

## <a name="helpful-tools-and-best-practices"></a>Strumenti utili e le procedure consigliate
 
 Microsoft offre diversi strumenti e informazioni aggiuntive che consentono di sviluppare per lo Stack di Azure.

| Raccomandazione | Riferimenti | 
| -------- | ------------- | 
| Installare gli strumenti corretti nella workstation di sviluppo. | - [Installare PowerShell](azure-stack-powershell-install.md)<br>- [Scaricare gli strumenti](azure-stack-powershell-download.md)<br>- [Configurazione di PowerShell](azure-stack-powershell-configure-user.md)<br>- [Installazione di Visual Studio](azure-stack-install-visual-studio.md) 
| Esaminare le seguenti informazioni:<br>-Considerazioni sui modelli di gestione risorse azure<br>-Per trovare i modelli di avvio rapido procedura<br>-Usa un modulo criteri che consentono di usare Azure per lo sviluppo per lo Stack di Azure | [Sviluppare per Azure Stack](azure-stack-developer.md) | 
| Esaminare e seguire le procedure consigliate per i modelli. | [Modelli di avvio rapido di gestione risorse](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Requisiti di versione

Stack di Azure supporta versioni specifiche di Azure PowerShell e API del servizio di Azure. È necessario utilizzare le versioni supportate per garantire che l'app è possibile distribuire per due Stack di Azure e Azure.

Per assicurarsi che si utilizza una versione corretta di PowerShell di Azure, utilizzare [profili della versione API](azure-stack-version-profiles.md). Per determinare il profilo di versione API più recente che è possibile utilizzare, è necessario conoscere la generazione dello Stack di Azure in uso. È possibile ottenere queste informazioni all'amministratore di Azure Stack.

>[!NOTE]
 Se si utilizza il Kit di sviluppo dello Stack di Azure e si dispone dell'accesso amministrativo, vedere la sezione "Per determinare la versione corrente" di [gestire gli aggiornamenti](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) per determinare la generazione dello Stack di Azure.

Per altre API, eseguiti il comando PowerShell seguente per restituire i spazi dei nomi, tipi di risorse e le versioni dell'API che sono supportate nella sottoscrizione Azure Stack. Nota potrebbe ancora essere differenze a livello di proprietà. (Per questo comando, è necessario avere già [installato](azure-stack-powershell-install.md) e [configurato](azure-stack-powershell-configure-user.md) PowerShell per un ambiente dello Stack di Azure. È inoltre necessario avere una sottoscrizione a un'offerta di Azure Stack.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Output di esempio (troncato): ![output di esempio del comando Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>Passaggi successivi

Per informazioni più dettagliate sulle differenze tra un livello di servizio, vedere:

* [Considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md)
* [Considerazioni per l'archiviazione in Azure Stack](azure-stack-acs-differences.md)
* [Considerazioni per la rete di Azure Stack](azure-stack-network-differences.md)
