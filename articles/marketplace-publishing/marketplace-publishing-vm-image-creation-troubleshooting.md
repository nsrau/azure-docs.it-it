---
title: Come risolvere problemi comuni durante la creazione del disco rigido virtuale | Documentazione Microsoft
description: Risposte alle domande frequenti sulla risoluzione dei problemi durante la creazione del disco rigido virtuale.
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: 
editor: 
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: c4e88a9fbb15dd90d619b159ae1065dfacc1907f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Come risolvere problemi comuni incontrati durante la creazione del disco rigido virtuale
Questo articolo viene fornito per aiutare gli sviluppatori e/o i co-amministratori di Azure Marketplace nel caso in cui registrino un problema o abbiano domande generiche nel momento in cui pubblicano o gestiscono la propria soluzione basata su macchina virtuale.

1. Come è possibile modificare il nome dell'host?
   
    Non è possibile aggiornare il nome dell'host dopo aver creato la macchina virtuale.
2. Come reimpostare il servizio Desktop remoto o la relativa password di accesso?
   
   * [Riferimento per macchine virtuali Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Riferimento per macchine virtuali Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Come generare nuovi certificati SSH?
   
   Vedere il collegamento: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Come configurare un certificato VPN aperto?
   
   Vedere il collegamento: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Quali sono i criteri di supporto per l'esecuzione di software server Microsoft nell'ambiente di macchina virtuale di Microsoft Azure (Infrastructure-as-a-Service) ?
   
   Vedere il collegamento: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Le macchine virtuali hanno un identificatore univoco?
   
   Azure codifica un ID univoco in ogni macchina virtuale di Azure. Per maggiori informazioni, visitare il blog e la documentazione appropriati.
7. In una macchina virtuale, com'è possibile gestire l'estensione di script personalizzati nell'attività di avvio?
   
   Vedere il collegamento: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Come creare una macchina virtuale dal portale di Azure usando il disco rigido virtuale caricato in archiviazione Premium?
   
   Questa funzionalità non è ancora supportata.
9. In Azure Marketplace sono supportate applicazioni a 32 bit?
   
   Per informazioni sui criteri di supporto, vedere il collegamento: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. Ogni volta che si tenta di creare un'immagine dai dischi rigidi virtuali, in PowerShell viene visualizzato un errore che indica che il disco rigido virtuale è già registrato con l'archivio immagini come risorsa. Ma non ho creato alcun tipo di risorsa in precedenza, né ho trovato un'immagine con questo nome in Azure. Come posso risolvere il problema?
    
    Questo problema si verifica in genere se l'utente ha eseguito il provisioning di una macchina virtuale da questo disco rigido virtuale, creando un blocco sul disco virtuale. Verificare che non sia stata allocata nessuna macchina virtuale da questo disco rigido virtuale. Se il problema persiste, generare un ticket di supporto usando questo collegamento o dal portale di pubblicazione (maggiori dettagli sono disponibili nella risposta alla domanda 11).