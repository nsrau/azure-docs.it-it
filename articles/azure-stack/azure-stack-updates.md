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
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: bf797404b173d6febe133eacbb9d36310160aff6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281574"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gestire gli aggiornamenti nella panoramica di Azure Stack

*Si applica a Sistemi integrati di Azure Stack*

I pacchetti di aggiornamento di Microsoft per Azure Stack in genere i sistemi integrati di una versione per il quarto martedì di ogni mese. Chiedere l'original equipment manufacturer (OEM) sul processo di notifica specifici per garantire le notifiche di aggiornamento raggiungano l'organizzazione. È inoltre possibile controllare in questa libreria della documentazione sotto **Overview** > **note sulla versione** per informazioni sulle versioni che sono in supporto attivo. 

Ogni versione di aggiornamenti software Microsoft viene incluso come un singolo pacchetto di aggiornamento. Un operatore di Azure Stack, è possibile importare, install e monitorare lo stato dell'installazione di questi pacchetti dal portale di amministrazione di aggiornamento. 

Il fornitore OEM rilasceranno anche gli aggiornamenti, ad esempio gli aggiornamenti di driver e firmware. Durante questi aggiornamenti vengono distribuiti come pacchetti separati dal fornitore, che ha importati, installati e gestiti allo stesso modo come pacchetti di aggiornamento da Microsoft.

Per mantenere il sistema sottoposto a supporto, è necessario mantenere aggiornato a un livello di versione specifica di Azure Stack. Assicurarsi di rivedere le [criteri di manutenzione Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> È possibile applicare i pacchetti di aggiornamento di Azure Stack per Azure Stack Development Kit. I pacchetti di aggiornamento sono progettati per i sistemi integrati. Per informazioni, vedere [ridistribuire il ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Il provider di risorse di aggiornamento

Lo Stack di Azure include un provider di risorse di aggiornamento che gestisce l'applicazione degli aggiornamenti software Microsoft. Il provider verifica che gli aggiornamenti vengano applicati a tutti gli host fisici, le applicazioni di Service Fabric e i Runtime e tutte le macchine virtuali dell'infrastruttura e i servizi associati.

Come installare gli aggiornamenti, è possibile visualizzare lo stato di alto livello come destinazioni di processo di aggiornamento i sottosistemi diversi in Azure Stack (ad esempio, gli host fisici e macchine virtuali dell'infrastruttura).

## <a name="plan-for-updates"></a>Piano per gli aggiornamenti

È consigliabile inviare notifiche agli utenti di tutte le operazioni di manutenzione e pianificare finestre di manutenzione normale durante non lavorative se possibile. Carichi di lavoro tenant sia operazioni nel portale, possono influire sulle operazioni di manutenzione.

- Prima di iniziare l'installazione di questo aggiornamento, eseguire [Test-AzureStack](azure-stack-diagnostic-test.md) con i parametri seguenti per convalidare lo stato di Azure Stack e risolvere eventuali problemi operativi trovati, inclusi tutti gli avvisi e gli errori. Anche gli avvisi attivi rivedere e risolvere gli eventuali che richiedono un'azione.  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Usando il riquadro Update per gestire gli aggiornamenti

Gestire gli aggiornamenti dal portale di amministratore. Un operatore di Azure Stack è possibile usare il riquadro di aggiornamento nel dashboard di:

- Consente di visualizzare informazioni importanti, ad esempio la versione corrente.
- installare gli aggiornamenti e monitorare lo stato di avanzamento.
- Esaminare la cronologia degli aggiornamenti per gli aggiornamenti installati in precedenza.
- Visualizzazione versione del pacchetto OEM corrente poiché il cloud
 
## <a name="determine-the-current-version"></a>Determinare la versione corrente

Nel riquadro aggiornamenti, è possibile visualizzare la versione corrente di Azure Stack. Per aprire il riquadro:

1. Aprire il portale di amministrazione di Azure Stack.
2. Selezionare **Dashboard**. Nel **Update** riquadro, la versione corrente è elencato. 

    ![Gli aggiornamenti riquadro nel dashboard predefinito](./media/azure-stack-updates/image1.png)

    Nella schermata, ad esempio, la versione è 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Installare gli aggiornamenti e monitorare lo stato di avanzamento


1. Aprire il portale di amministrazione di Azure Stack.
2. Selezionare **Dashboard**. Selezionare il riquadro di aggiornamento.
3. Selezionare **aggiornale**.

    ![Aggiornamento dello Stack di Azure i dettagli dell'esecuzione](media/azure-stack-updates/azure-stack-update-button.png)

4.  È possibile visualizzare lo stato di alto livello come il processo di aggiornamento esegue l'iterazione attraverso i vari sottosistemi in Azure Stack. Esempio sottosistemi includono gli host fisici, Service Fabric, le macchine virtuali di infrastruttura e servizi che forniscono i portali di amministratore e utente. Durante il processo di aggiornamento, il provider di risorse di aggiornamento sono contenute informazioni dettagliate aggiuntive sull'aggiornamento, ad esempio il numero di passaggi che hanno avuto esito positivo, nonché il numero in corso.

5. Selezionare il **Scarica i log completi** rispetto all'aggiornamento di Pannello di dettagli per scaricare i log completi di esecuzione.

    ![Aggiornamento dello Stack di Azure i dettagli dell'esecuzione](media/azure-stack-updates/update-run-details.png)

6. Al termine, il provider di risorse di aggiornamento fornisce una **Succeeded** conferma per essere informati che il processo di aggiornamento è stato completato e il tempo impiegato. Da qui, è possibile visualizzare informazioni su tutti gli aggiornamenti, gli aggiornamenti disponibili o installate tramite il filtro.

    ![Eseguire operazioni riuscite dettagli aggiornamento dello Stack di Azure](media/azure-stack-updates/update-success.png)

   Se l'aggiornamento non riesce, l'aggiornamento riquadro reports **necessita di attenzione**. Usare la **Scarica i log completi** per ottenere lo stato generale in cui l'aggiornamento potrebbe avere non riuscito. La raccolta di log di Azure Stack consente di agevolare la diagnostica e risoluzione dei problemi.

## <a name="next-steps"></a>Passaggi successivi

- [Criteri di manutenzione Azure Stack](azure-stack-servicing-policy.md) 
- [Gestione delle aree in Azure Stack](azure-stack-region-management.md)
