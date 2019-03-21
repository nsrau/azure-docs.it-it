---
title: Comprendere le differenze principali tra Azure e Azure Stack quando si usano i servizi e creazione di App | Microsoft Docs
description: Quello che devi sapere per utilizzare i servizi o creare applicazioni per Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 12/27/2018
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 158f22ec2ab07bfc2f893d4b0c55d862e0b19de0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58106662"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Considerazioni principali: Uso dei servizi o creazione di App per Azure Stack

Prima di usare i servizi o creare applicazioni per Azure Stack, è necessario comprendere le differenze tra Azure e Azure Stack. Questo articolo identifica le principali considerazioni quando si usa Azure Stack come ambiente di sviluppo cloud ibrido.

## <a name="overview"></a>Panoramica

Azure Stack è una piattaforma cloud ibrida che permette di usare i servizi di Azure dal data center aziendale o del provider di servizi. È possibile compilare un'app in Azure Stack e quindi distribuirla in Azure Stack, in Azure o per il cloud ibrido di Azure.

L'operatore di Azure Stack consentirà di conoscere quali servizi sono disponibili per l'utilizzo e come ottenere supporto. Queste connessioni offrono questi servizi tramite i piani personalizzati e le offerte.

Il contenuto tecnico di Azure presuppone che le app sviluppate per un servizio di Azure anziché Azure Stack. Quando si compilano e distribuiscono le App in Azure Stack, è necessario conoscere alcune delle differenze principali, ad esempio:

* Azure Stack offre un' **subset** dei servizi e funzionalità disponibili in Azure.
* Il provider della società o un servizio può scegliere quali servizi che vogliono offrire. Le opzioni disponibili possono includere servizi personalizzati o le applicazioni. Offrono la propria documentazione personalizzati.
* È necessario usare i valori corretti gli endpoint di Azure Stack-specifici (ad esempio, gli URL per l'indirizzo di portale e l'endpoint Azure Resource Manager).
* È necessario usare le versioni di PowerShell e API supportate da Azure Stack. Utilizzare le versioni supportate assicura che le app di lavoro in Azure Stack e Azure.

## <a name="cheat-sheet-high-level-differences"></a>Foglio informativo: Differenze di alto livello

Nella tabella seguente vengono descritte le differenze generali tra Azure e Azure Stack. Quando si sviluppa per Azure Stack o usano servizi di Azure Stack, tenere presente queste differenze.

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

| Area | Azure (globale) | Azure Stack |
| -------- | ------------- | ----------|
| Chi agisce che? | Microsoft | L'organizzazione o provider di servizi.|
| Chi è necessario contattare per il supporto? | Microsoft | Per un sistema integrato, contattare l'operatore di Azure Stack (nel provider del servizio o dell'organizzazione) per il supporto.<br><br>Per il supporto di Azure Stack Development Kit, visitare il [forum Microsoft](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Poiché il kit di sviluppo è un ambiente di valutazione, non è previsto alcun supporto ufficiale offerto tramite Microsoft dei clienti supporto tecnico clienti Microsoft.
| Servizi disponibili | Visualizzare l'elenco delle [prodotti Azure](https://azure.microsoft.com/services/?b=17.04b). Servizi disponibili variano in base all'area di Azure. | Azure Stack supporta un subset dei servizi di Azure. Servizi effettivi variano in base alle quali sceglie l'organizzazione o provider di servizi offerti.
| Azure Resource Manager endpoint * | https://management.azure.com | Per un sistema integrato Azure Stack, usare l'endpoint che ha fornito l'operatore di Azure Stack.<br><br>Per il kit di sviluppo, usare: https://management.local.azurestack.external
| Portale URL * | [https://portal.azure.com](https://portal.azure.com) | Per un sistema integrato Azure Stack, passare all'URL che ha fornito l'operatore di Azure Stack.<br><br>Per il kit di sviluppo, usare: https://portal.local.azurestack.external
| Region | È possibile selezionare l'area in cui si desidera distribuire. | Per un sistema integrato Azure Stack, usare l'area in cui è disponibile nel sistema.<br><br>Per il kit di sviluppo, area sarà sempre **locale**.
| Gruppi di risorse | Un gruppo di risorse possa estendersi a più aree. | Per i sistemi integrati e il kit di sviluppo, è disponibile una sola area.
|Spazi dei nomi supportati, i tipi di risorse e le versioni dell'API | La versione più recente (o versioni precedenti che non sono ancora deprecate). | Azure Stack supporta versioni specifiche. Vedere le [i requisiti di versione](#version-requirements) sezione di questo articolo.
| | |

* Se si è un operatore di Azure Stack, vedere [tramite il portale dell'amministratore](../azure-stack-manage-portals.md) e [nozioni fondamentali sull'amministrazione](../azure-stack-manage-basics.md) per altre informazioni.

## <a name="helpful-tools-and-best-practices"></a>Strumenti utili e procedure consigliate
 
 Microsoft offre gli strumenti e linee guida che consentono di sviluppare per Azure Stack.

| Recommendation | Riferimenti |
| -------- | ------------- |
| Installare gli strumenti corretti in della workstation dello sviluppatore. | - [Installare PowerShell](azure-stack-powershell-install.md)<br>- [Scarica gli strumenti](azure-stack-powershell-download.md)<br>- [Configurare PowerShell](azure-stack-powershell-configure-user.md)<br>- [Installare Visual Studio](azure-stack-install-visual-studio.md) 
| Esaminare le informazioni sugli elementi seguenti:<br>-Considerazioni sui modelli di Resource Manager azure<br>-Procedura trovare modelli di avvio rapido<br>-Usare un modulo criteri che consentono di usare Azure per lo sviluppo per Azure Stack | [Sviluppare per Azure Stack](azure-stack-developer.md) | 
| Esaminare e seguire le procedure consigliate per i modelli. | [Modelli di avvio rapido di Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Requisiti di versione

Azure Stack supporta versioni specifiche di Azure PowerShell e API del servizio di Azure. Usare le versioni supportate per garantire che l'app è possibile distribuire entrambi Azure Stack e Azure.

Per assicurarsi che si usa una versione corretta di Azure PowerShell, usare [i profili delle versioni API](azure-stack-version-profiles.md). Per determinare il profilo di versione API più recente che è possibile usare, scoprire la compilazione dello Stack di Azure in uso. È possibile ottenere queste informazioni dall'amministratore di Azure Stack.

> [!NOTE]
>  Se si usa Azure Stack Development Kit e si dispone dell'accesso amministrativo, vedere la [determinare la versione corrente](../azure-stack-updates.md#determine-the-current-version) sezione per determinare la compilazione di Azure Stack.

Per altre API, eseguiti il comando PowerShell seguente per l'output di spazi dei nomi, tipi di risorse e le versioni API supportate nella sottoscrizione di Azure Stack. Nota potrebbe essere ancora differenze a livello una proprietà. Per questo comando funziona, è necessario avere già [installata](azure-stack-powershell-install.md) e [configurato](azure-stack-powershell-configure-user.md) PowerShell per un ambiente Azure Stack. È anche necessario avere una sottoscrizione a un'offerta di Azure Stack.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Output di esempio (troncato): ![Output di esempio del comando Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>Passaggi successivi

Per informazioni più dettagliate sulle differenze tra un livello di servizio, vedere:

* [Considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md)
* [Considerazioni per l'archiviazione in Azure Stack](azure-stack-acs-differences.md)
* [Considerazioni sulla rete di Azure Stack](azure-stack-network-differences.md)
