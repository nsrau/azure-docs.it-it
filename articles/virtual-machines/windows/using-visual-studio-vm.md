---
title: Uso di Visual Studio in una macchina virtuale di Azure | Microsoft Docs
description: Uso di Visual Studio in una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 01/30/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 813022f1778e2c7f3174e11192b845c2c33ad219
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a id="top"> </a> Immagini di Visual Studio in Azure
L'uso di Visual Studio in una macchina virtuale (VM) di Azure preconfigurata è il modo più semplice e veloce per avere subito a disposizione un ambiente di sviluppo pienamente operativo.  In [Azure Marketplace](https://portal.azure.com/) sono disponibili immagini di sistema con diverse configurazioni di Visual Studio. È sufficiente avviare una macchina virtuale per iniziare a usare l'ambiente di sviluppo.

Non si è ancora provato Azure? [Creare un account Azure gratuito](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Quali configurazioni e versioni sono disponibili?
In Azure Marketplace sono disponibili le immagini per le versioni principali più recenti: Visual Studio 2017 e Visual Studio 2015.  Per ogni versione principale sono elencate la versione originariamente rilasciata (nota anche come RTW) e le versioni aggiornate più recenti.  Per ognuna di queste diverse versioni sono disponibili le edizioni Visual Studio Enterprise e Visual Studio Community.

|               Versione di rilascio              |          Edizioni            |    Versione del prodotto    |
|:------------------------------------------:|:----------------------------:|:---------------------:|
| Visual Studio 2017 - Più recente (versione 15.5) |    Enterprise, Community     |     Versione 15.5.3    |
|         Visual Studio 2017 - RTW           |    Enterprise, Community     |     Versione 15.0.7    |
|   Visual Studio 2015 - Più recente (Update 3)   |    Enterprise, Community     | Versione 14.0.25431.01 |
|         Visual Studio 2015 - RTW           | Nessuna (supporto per la manutenzione scaduto) |          ---          |

> [!NOTE]
> In base ai criteri di manutenzione di Microsoft, il supporto per la manutenzione della versione di Visual Studio 2015 originariamente rilasciata (o RTW) è scaduto.  Visual Studio 2015 Update 3 è pertanto l'unica versione rimanente offerta per la linea di prodotti Visual Studio 2015.

Per altre informazioni, vedere [Criteri di manutenzione di Visual Studio](https://www.visualstudio.com/en-us/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Quali funzionalità vengono installate?
Ogni immagine contiene il set di funzionalità consigliato per una specifica edizione di Visual Studio.  L'installazione include in genere i componenti seguenti:

* Tutti i carichi di lavoro disponibili, inclusi i componenti facoltativi consigliati per uno specifico carico di lavoro
* Targeting Pack, Strumenti di sviluppo ed SDK per .NET 4.6.2 e .NET 4.7
* Visual F#
* Estensione GitHub per Visual Studio
* Strumenti di LINQ to SQL

Questa è la riga di comando usata per installare Visual Studio quando si creano le immagini:

   * vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
   * add Microsoft.Net.Component.4.7.SDK ^
   * add Microsoft.Net.Component.4.7.TargetingPack ^ 
   * add Microsoft.Net.Component.4.6.2.SDK ^
   * add Microsoft.Net.Component.4.6.2.TargetingPack ^
   * add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
   * add Microsoft.VisualStudio.Component.FSharp ^
   * add Component.GitHub.VisualStudio ^
   * add Microsoft.VisualStudio.Component.LinqToSql

Se le immagini non includono una funzionalità di Visual Studio che si ritiene necessaria, fornire commenti e suggerimenti tramite l'apposito strumento nell'angolo superiore destro della pagina.

## <a name="what-size-vm-should-i-choose"></a>Quale dimensione scegliere per la macchina virtuale?
Il provisioning di una nuova macchina virtuale è un'operazione semplice e Azure offre un'ampia gamma di macchine virtuali di diverse dimensioni.  Come per qualsiasi prodotto hardware, è opportuno trovare una soluzione con un buon rapporto tra costo e prestazioni.  Poiché Visual Studio è una potente applicazione multithreading, è in genere necessaria una macchina virtuale con almeno due processori e 7 GB di memoria.  Per altre informazioni sulle dimensioni delle macchine virtuali più recenti, vedere [Dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes).

Con Azure si può comunque sempre decidere di modificare la configurazione iniziale. È possibile optare per una diversa soluzione ridimensionando la macchina virtuale.  È possibile effettuare il provisioning di una nuova macchina virtuale con una dimensione più appropriata oppure ridimensionare la macchina virtuale esistente in base al diverso hardware sottostante.  Per altre informazioni, vedere [Ridimensionare una VM Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm).

## <a name="after-i-get-the-vm-running-then-what"></a>Che cosa si deve fare dopo che la macchina virtuale è in esecuzione?
Visual Studio segue il modello "Bring Your Own License" di Azure.  Pertanto, come per un'installazione in hardware proprietario, uno dei primi passaggi è quello di associare una licenza all'installazione di Visual Studio.  È possibile sbloccare Visual Studio eseguendo l'accesso con un account Microsoft associato a una sottoscrizione di Visual Studio oppure usando il codice Product Key ottenuto con l'acquisto iniziale.  Per altre informazioni, vedere [Accedere a Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/signing-in-to-visual-studio) e [Procedura: Sbloccare Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="after-i-build-out-the-dev-vm-how-do-i-save-capture-it-for-future-or-team-use"></a>Dopo aver configurato la macchina virtuale per lo sviluppo, come si fa a salvarla (o acquisirla) per un uso futuro o in team?

La gamma di ambienti di sviluppo è enorme e la configurazione di ambienti particolarmente complessi comporta costi notevoli.  Tuttavia, indipendentemente dalla configurazione dell'ambiente, Azure consente di preservare con facilità il proprio investimento. È infatti possibile salvare o acquisire la macchina virtuale perfettamente configurata come immagine di base per un uso futuro, per esigenze individuali e/o per altri membri del proprio team.  Quindi, all'avvio di una nuova macchina virtuale, effettuarne il provisioning dall'immagine di base anziché da quella di Marketplace.

In sintesi, è necessario eseguire sysprep e arrestare la macchina virtuale in esecuzione e quindi *acquisire (figura 1)* la macchina virtuale come immagine tramite l'interfaccia utente del portale di Azure.  Azure salverà il file `.vhd` contenente l'immagine nell'account di archiviazione selezionato  e la nuova immagine verrà visualizzata come risorsa Immagine nell'elenco delle risorse della sottoscrizione.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal’s UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Figura 1) Acquisire un'immagine tramite l'interfaccia utente del portale di Azure.*</center>

Per altre informazioni, vedere [Acquisizione di una macchina virtuale in un'immagine](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource).

  **Promemoria:** non dimenticare di eseguire sysprep per acquisire l'immagine della macchina virtuale.  Se si omette questo passaggio, Azure non potrà effettuare il provisioning di una macchina virtuale in base a tale immagine.

> [!NOTE]
> L'archiviazione di una o più immagini comporta un costo incrementale che è tuttavia pressoché insignificante rispetto ai costi di manodopera da sostenere se ogni membro del team dovesse riconfigurare da zero la macchina virtuale.  Ad esempio, a fronte di un costo di pochi euro, è possibile creare e archiviare per un mese un'immagine da 127 GB riutilizzabile da tutti i membri del team.  Tale costo è tuttavia trascurabile rispetto alle ore che ogni dipendente impiega per creare e convalidare una casella di sviluppo correttamente configurata per l'uso individuale.

Inoltre, le attività o le tecnologie di sviluppo possono richiedere maggiore scalabilità, ad esempio diverse configurazioni per l'ambiente di sviluppo e per le macchine virtuali.  Per soddisfare queste esigenze, è possibile usare Azure DevTest Labs per creare _modelli_ che consentano di automatizzare la creazione di un'immagine finale e gestire i criteri per le macchine virtuali usate dal proprio team.  L'articolo [Usare Azure DevTest Labs per sviluppatori](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-developer-lab) è la migliore fonte di informazioni su DevTest Labs.

## <a name="next-steps"></a>Passaggi successivi
Una volta acquisite le nozioni di base sulle immagini di Visual Studio preconfigurate, il passaggio successivo consiste nel creare una nuova macchina virtuale:

* [Creare una macchina virtuale Windows con il portale di Azure](quick-create-portal.md)
* [Panoramica di Macchine virtuali di Azure](overview.md)
