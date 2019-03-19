---
title: Gestire gli aggiornamenti nella panoramica di Azure Stack | Microsoft Docs
description: Informazioni sulla gestione degli aggiornamenti per i sistemi integrati di Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: 6cddb513249b424ef0df2ed2d1b5609de17c0744
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894037"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gestire gli aggiornamenti nella panoramica di Azure Stack

*Si applica a: Sistemi integrati di Azure Stack*

I pacchetti di aggiornamento di Microsoft per Azure Stack in genere i sistemi integrati di una versione per il quarto martedì di ogni mese. Chiedere l'OEM sul processo di notifica specifici per garantire le notifiche di aggiornamento raggiungano l'organizzazione. È inoltre possibile controllare in questa libreria della documentazione sotto **Overview** > **note sulla versione** per informazioni sulle versioni che sono in supporto attivo. 

Ogni versione di aggiornamenti software Microsoft viene incluso come un singolo pacchetto di aggiornamento. Un operatore di Azure Stack, è possibile importare, install e monitorare lo stato dell'installazione di questi pacchetti dal portale di amministrazione di aggiornamento. 

Il fornitore dell'hardware (OEM) original equipment manufacturer rilasceranno anche gli aggiornamenti, ad esempio gli aggiornamenti di driver e firmware. Durante questi aggiornamenti vengono distribuiti come pacchetti separati dal fornitore dell'hardware OEM, questi vengono importati, installato e gestiti i pacchetti di aggiornamento stesso modo di Microsoft, i pacchetti di aggiornamento vengono importati, installati e gestiti.

Per mantenere il sistema sottoposto a supporto, è necessario mantenere aggiornato a un livello di versione specifica di Azure Stack. Assicurarsi di rivedere le [criteri di manutenzione Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> È possibile applicare i pacchetti di aggiornamento di Azure Stack per Azure Stack Development Kit. I pacchetti di aggiornamento sono progettati per i sistemi integrati. Per informazioni, vedere [ridistribuire il ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Il provider di risorse di aggiornamento

Lo Stack di Azure include un provider di risorse di aggiornamento che Orchestra l'applicazione degli aggiornamenti software Microsoft. Questo provider di risorse garantisce che gli aggiornamenti vengano applicati a tutti gli host fisici, le applicazioni di Service Fabric e i Runtime e tutte le macchine virtuali dell'infrastruttura e i servizi associati.

Come installare gli aggiornamenti, è possibile visualizzare lo stato di alto livello come destinazioni di processo di aggiornamento i sottosistemi diversi in Azure Stack (ad esempio, gli host fisici e macchine virtuali dell'infrastruttura).

## <a name="plan-for-updates"></a>Piano per gli aggiornamenti

È consigliabile inviare notifiche agli utenti di tutte le operazioni di manutenzione e pianificare finestre di manutenzione normale durante non lavorative se possibile. Carichi di lavoro tenant sia operazioni nel portale, possono influire sulle operazioni di manutenzione.

- Prima di iniziare l'installazione di questo aggiornamento, eseguire [Test-AzureStack](azure-stack-diagnostic-test.md) con i parametri seguenti per convalidare lo stato di Azure Stack e risolvere eventuali problemi operativi trovati, inclusi tutti gli avvisi e gli errori. Anche gli avvisi attivi rivedere e risolvere gli eventuali che richiedono un'azione.  

  ```PowerShell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Usando il riquadro Update per gestire gli aggiornamenti
Gestire gli aggiornamenti dal portale di amministratore. Un operatore di Azure Stack è possibile usare il riquadro di aggiornamento nel dashboard di:

- Consente di visualizzare informazioni importanti, ad esempio la versione corrente.
- installare gli aggiornamenti e monitorare lo stato di avanzamento.
- Esaminare la cronologia degli aggiornamenti per gli aggiornamenti installati in precedenza.
 
## <a name="determine-the-current-version"></a>Determinare la versione corrente

Il riquadro di aggiornamento Visualizza la versione corrente di Azure Stack. È possibile ottenere al riquadro aggiornamento utilizzando uno dei metodi seguenti nel portale di amministrazione:

- Nel dashboard di visualizzare la versione corrente nel **Update** riquadro.
 
   ![Gli aggiornamenti riquadro nel dashboard predefinito](./media/azure-stack-updates/image1.png)
 
- Nel **gestione delle aree** riquadro, fare clic sul nome di area. Visualizzare la versione corrente nel **Update** riquadro.

## <a name="next-steps"></a>Passaggi successivi

- [Azure Stack di manutenzione dei criteri](azure-stack-servicing-policy.md) 
- [Gestione area nello Stack di Azure](azure-stack-region-management.md)     


