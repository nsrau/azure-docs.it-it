---
title: Gestire gli aggiornamenti in panoramica di Azure Stack | Documenti Microsoft
description: Informazioni sulla gestione degli aggiornamenti per i sistemi Azure Stack integrato.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3d0d5ea6cc3f3cc7bc0550b83dabbf0ae6af8a27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gestire gli aggiornamenti in panoramica di Azure Stack

*Si applica a: Azure Stack integrate di sistemi*

Microsoft rilascerà i pacchetti di aggiornamento per i sistemi Azure Stack integrato a intervalli regolari. Ogni versione di Microsoft degli aggiornamenti del software viene fornito come un singolo pacchetto di aggiornamento. Come operatore di Stack di Azure, è possibile importare, installare e monitorare l'avanzamento dell'installazione di questi pacchetti dal portale di amministrazione di aggiornamento. 

Il fornitore dell'hardware (OEM) original equipment manufacturer rilasceranno anche gli aggiornamenti, ad esempio aggiornamenti di driver e firmware. Questi aggiornamenti vengono distribuiti come pacchetti separati dal fornitore dell'hardware OEM e vengono gestiti separatamente da Microsoft Update.

Per mantenere il sistema di supporto, è necessario mantenere aggiornato a un livello di versione Stack di Azure. Verificare che il [dello Stack di Azure per la manutenzione dei criteri](azure-stack-servicing-policy.md).

> [!NOTE]
> Non è possibile applicare i pacchetti di aggiornamento dello Stack di Azure al Kit di sviluppo dello Stack di Azure. I pacchetti di aggiornamento sono progettati per sistemi integrati.

## <a name="the-update-resource-provider"></a>Il provider di risorse di aggiornamento

Stack di Azure include un provider di risorse di aggiornamento che coordina l'applicazione degli aggiornamenti software Microsoft. Questo provider di risorse garantisce che gli aggiornamenti vengono applicati a tutti gli host fisici, le applicazioni di Service Fabric e Runtime e tutte le macchine virtuali dell'infrastruttura e i servizi associati.

Come installare gli aggiornamenti, è possibile visualizzare facilmente lo stato di alto livello come destinazioni di processo di aggiornamento i vari sottosistemi nello Stack di Azure (ad esempio, gli host fisici e macchine virtuali di infrastruttura).

## <a name="plan-for-updates"></a>Pianificare gli aggiornamenti

È consigliabile inviare una notifica agli utenti di tutte le operazioni di manutenzione e pianificare le operazioni di manutenzione durante le ore non lavorative quanto possibile. Carichi di lavoro tenant sia le operazioni del portale, possono influire sulle operazioni di manutenzione.

## <a name="using-the-update-tile-to-manage-updates"></a>Utilizzando il riquadro di aggiornamento per gestire gli aggiornamenti
La gestione degli aggiornamenti dal portale di amministrazione è un processo semplice. Un operatore di Stack di Azure è possibile passare al riquadro nel dashboard di aggiornamento:

- Consente di visualizzare informazioni importanti, ad esempio la versione corrente.
- installare gli aggiornamenti e monitorare lo stato di avanzamento.
- Esaminare la cronologia degli aggiornamenti per gli aggiornamenti installati in precedenza.
 
## <a name="determine-the-current-version"></a>Determinare la versione corrente

Nel riquadro di aggiornamento vengono visualizzati la versione corrente dello Stack di Azure. È possibile ottenere il titolo dell'aggiornamento utilizzando uno dei metodi seguenti nel portale di amministrazione:

- Nel dashboard, visualizzare la versione corrente nel **aggiornamento** riquadro.
 
   ![Gli aggiornamenti riquadro nel dashboard predefinito](./media/azure-stack-updates/image1.png)
 
- Nel **Gestione area** riquadro, fare clic sul nome di area. Visualizzare la versione corrente nel **aggiornamento** riquadro.

## <a name="next-steps"></a>Passaggi successivi

- [Azure Stack di manutenzione dei criteri](azure-stack-servicing-policy.md) 
- [Gestione area nello Stack di Azure](azure-stack-region-management.md)     


